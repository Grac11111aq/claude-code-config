# 🔍 可観測性（Observability）設計規約

<module_info>
このモジュールは、システムの可観測性を実現する設計規約を定義します。
分散システムでも問題の迅速な発見と解決を可能にします。
</module_info>

## 📐 基本規約

### 3つの柱

```yaml
メトリクス（Metrics）:
  - システムの数値的な状態
  - 時系列データ
  - アラートの基準

ログ（Logs）:
  - イベントの詳細記録
  - デバッグ情報
  - 監査証跡

トレース（Traces）:
  - リクエストの流れ
  - サービス間の依存関係
  - パフォーマンスボトルネック
```

## 📊 メトリクス設計

### 標準メトリクス

```yaml
RED方式:
  Rate: リクエスト数/秒
  Errors: エラー率
  Duration: レスポンスタイム

USE方式（リソース）:
  Utilization: 使用率
  Saturation: 飽和度
  Errors: エラー数

ビジネスメトリクス:
  - ユーザー登録数
  - 注文完了率
  - 売上金額
```

### Prometheus実装

```typescript
import { register, Counter, Histogram, Gauge } from 'prom-client';

// カウンター（単調増加）
const httpRequestsTotal = new Counter({
  name: 'http_requests_total',
  help: 'Total number of HTTP requests',
  labelNames: ['method', 'route', 'status']
});

// ヒストグラム（分布）
const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route'],
  buckets: [0.1, 0.3, 0.5, 0.7, 1, 3, 5, 7, 10]
});

// ゲージ（増減可能）
const activeConnections = new Gauge({
  name: 'active_connections',
  help: 'Number of active connections'
});

// ミドルウェア実装
export const metricsMiddleware = (req: Request, res: Response, next: NextFunction) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    
    httpRequestsTotal.inc({
      method: req.method,
      route: req.route?.path || 'unknown',
      status: res.statusCode
    });
    
    httpRequestDuration.observe({
      method: req.method,
      route: req.route?.path || 'unknown'
    }, duration);
  });
  
  next();
};
```

## 📝 構造化ログ

### ログフォーマット

```json
{
  "timestamp": "2024-01-01T12:00:00.000Z",
  "level": "INFO",
  "service": "order-service",
  "traceId": "abc123",
  "spanId": "def456",
  "userId": "user-789",
  "message": "注文処理完了",
  "context": {
    "orderId": "order-123",
    "amount": 10000,
    "items": 3
  },
  "duration": 150
}
```

### ログレベル活用

```typescript
// 適切なログレベルの使用
logger.debug('詳細なデバッグ情報', { query: sql });
logger.info('重要なビジネスイベント', { orderId, userId });
logger.warn('警告すべき状況', { retryCount, maxRetries });
logger.error('エラー発生', { error: err.message, stack: err.stack });
```

## 🔗 分散トレーシング

### OpenTelemetry実装

```typescript
import { trace, context, SpanStatusCode } from '@opentelemetry/api';

const tracer = trace.getTracer('order-service');

export class OrderService {
  async createOrder(orderData: OrderData): Promise<Order> {
    // 新しいスパン開始
    const span = tracer.startSpan('createOrder');
    
    try {
      // コンテキスト伝播
      return await context.with(trace.setSpan(context.active(), span), async () => {
        // 子スパン：在庫確認
        const inventorySpan = tracer.startSpan('checkInventory');
        const available = await this.checkInventory(orderData.items);
        inventorySpan.end();
        
        // 子スパン：注文作成
        const orderSpan = tracer.startSpan('saveOrder');
        const order = await this.orderRepo.save(orderData);
        orderSpan.setAttribute('order.id', order.id);
        orderSpan.end();
        
        // 子スパン：決済処理
        const paymentSpan = tracer.startSpan('processPayment');
        await this.paymentService.charge(order);
        paymentSpan.end();
        
        return order;
      });
    } catch (error) {
      span.recordException(error);
      span.setStatus({ code: SpanStatusCode.ERROR });
      throw error;
    } finally {
      span.end();
    }
  }
}
```

### コンテキスト伝播

```typescript
// HTTPヘッダーでトレース情報伝播
export const traceMiddleware = (req: Request, res: Response, next: NextFunction) => {
  const traceHeader = req.headers['x-trace-id'] as string;
  const spanHeader = req.headers['x-span-id'] as string;
  
  // コンテキスト復元
  const ctx = propagation.extract(context.active(), req.headers);
  
  context.with(ctx, () => {
    next();
  });
};

// 下流サービスへの伝播
const callDownstream = async (url: string, data: any) => {
  const headers = {};
  propagation.inject(context.active(), headers);
  
  return axios.post(url, data, { headers });
};
```

## 📊 ダッシュボード設計

### Grafanaダッシュボード構成

```yaml
Overview Dashboard:
  - サービスヘルスマップ
  - エラー率トレンド
  - レスポンスタイム分布
  - スループット

Service Dashboard:
  - RED メトリクス
  - リソース使用状況
  - 依存関係マップ
  - SLI/SLO 達成率

Business Dashboard:
  - KPIメトリクス
  - ファネル分析
  - ユーザー行動
  - 収益指標
```

### アラート設計

```yaml
優先度別アラート:
  P1（緊急）:
    - サービスダウン
    - エラー率 > 10%
    - レスポンスタイム > 5秒
    
  P2（重要）:
    - エラー率 > 5%
    - CPU使用率 > 90%
    - ディスク残量 < 10%
    
  P3（警告）:
    - エラー率 > 1%
    - メモリ使用率 > 80%
    - キュー滞留 > 1000
```

## 🔧 APM統合

### エラー追跡

```typescript
// Sentryとの統合例
import * as Sentry from '@sentry/node';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  integrations: [
    new Sentry.Integrations.Http({ tracing: true }),
    new ProfilingIntegration()
  ],
  tracesSampleRate: 0.1, // 10%サンプリング
  profilesSampleRate: 0.1
});

// エラーキャプチャ
app.use(Sentry.Handlers.errorHandler());
```

## 🎯 SLI/SLO定義

```yaml
SLI（Service Level Indicators）:
  可用性:
    定義: 成功したリクエスト / 全リクエスト
    計測: 5分間隔
    
  レイテンシ:
    定義: 95%タイルが200ms以下
    計測: 1分間隔
    
  エラー率:
    定義: エラーレスポンス / 全レスポンス
    計測: 1分間隔

SLO（Service Level Objectives）:
  可用性: 99.9%（月間43分のダウンタイムまで）
  レイテンシ: 95%が200ms以下
  エラー率: 0.1%以下
```

## 🔐 セキュリティ考慮

```yaml
ログのマスキング:
  - パスワード
  - APIキー
  - 個人情報（必要に応じて）
  
アクセス制御:
  - メトリクスエンドポイントの認証
  - ダッシュボードへのアクセス制限
  - 監査ログの保護
```

## 📚 ベストプラクティス

```yaml
メトリクス:
  - カーディナリティに注意
  - 適切なラベル使用
  - 保持期間の設定

ログ:
  - 構造化ログの徹底
  - 適切なログレベル
  - コンテキスト情報の付与

トレース:
  - 重要な操作にスパン設定
  - サンプリング率の調整
  - 機密情報を含めない
```