# 🚨 エラー設計・例外処理規約

<module_info>
このモジュールは、エラー設計と例外処理の体系的な規約を定義します。
明確なエラー分類と一貫した処理により、トラブルシューティングを効率化します。
</module_info>

## 📐 基本規約

### エラー分類

```yaml
ビジネスエラー（4xx系）:
  - 検証エラー: 入力値の不正
  - 認証エラー: 認証失敗
  - 認可エラー: 権限不足
  - リソース不在: 404 Not Found
  - ビジネスルール違反: 業務制約違反

システムエラー（5xx系）:
  - 接続エラー: DB/外部サービス接続失敗
  - タイムアウト: 処理時間超過
  - リソース枯渇: メモリ/ディスク不足
  - 予期しないエラー: バグ等
```

### エラーコード体系

```yaml
形式: {SERVICE}_{CATEGORY}_{SPECIFIC}
例:
  USER_VALIDATION_EMAIL_INVALID
  ORDER_BUSINESS_INSUFFICIENT_STOCK
  PAYMENT_SYSTEM_GATEWAY_TIMEOUT

カテゴリ:
  VALIDATION: 検証エラー
  BUSINESS: ビジネスルール違反
  AUTH: 認証・認可
  SYSTEM: システムエラー
```

## 🏗️ エラーレスポンス構造

### 標準エラー形式

```json
{
  "error": {
    "code": "USER_VALIDATION_EMAIL_INVALID",
    "message": "メールアドレスの形式が正しくありません",
    "details": {
      "field": "email",
      "value": "invalid-email",
      "constraint": "email_format"
    },
    "timestamp": "2024-01-01T00:00:00Z",
    "traceId": "abc-123-def"
  }
}
```

### 複数エラーの場合

```json
{
  "errors": [
    {
      "code": "USER_VALIDATION_EMAIL_INVALID",
      "field": "email",
      "message": "メールアドレスの形式が正しくありません"
    },
    {
      "code": "USER_VALIDATION_PASSWORD_WEAK",
      "field": "password",
      "message": "パスワードは8文字以上必要です"
    }
  ]
}
```

## 🔄 リトライ戦略

### リトライ可能なエラー

```yaml
リトライ対象:
  - ネットワークタイムアウト
  - 一時的な接続エラー
  - 429 Too Many Requests
  - 503 Service Unavailable

リトライ不可:
  - 400 Bad Request
  - 401 Unauthorized
  - 404 Not Found
  - ビジネスエラー全般
```

### 指数バックオフ

```typescript
const retryConfig = {
  maxRetries: 3,
  initialDelay: 1000, // 1秒
  maxDelay: 10000,    // 10秒
  factor: 2,          // 2倍ずつ増加
  jitter: true        // ランダム性を追加
};
```

## 🔌 サーキットブレーカー

```yaml
状態:
  CLOSED: 正常（リクエスト通過）
  OPEN: 遮断（即座にエラー返却）
  HALF_OPEN: 半開（一部リクエストで確認）

設定:
  failureThreshold: 50%  # エラー率閾値
  volumeThreshold: 20    # 最小リクエスト数
  timeout: 60000         # OPEN継続時間（ms）
  
実装例:
  - 外部API呼び出し
  - データベース接続
  - マイクロサービス間通信
```

## 💼 ドメインエラーの実装

```typescript
// domain/errors/BusinessError.ts
export abstract class BusinessError extends Error {
  constructor(
    public readonly code: string,
    message: string,
    public readonly details?: any
  ) {
    super(message);
    this.name = this.constructor.name;
  }
}

// 具体的なエラー
export class InsufficientStockError extends BusinessError {
  constructor(productId: string, requested: number, available: number) {
    super(
      'ORDER_BUSINESS_INSUFFICIENT_STOCK',
      `在庫が不足しています。要求数: ${requested}, 在庫数: ${available}`,
      { productId, requested, available }
    );
  }
}
```

## 🌍 グローバルエラーハンドリング

```typescript
// エラーハンドラーミドルウェア
export const errorHandler = (err: Error, req: Request, res: Response, next: NextFunction) => {
  // ログ記録
  logger.error('エラー発生', {
    error: err,
    request: {
      method: req.method,
      url: req.url,
      headers: req.headers,
      body: req.body
    },
    traceId: req.id
  });

  // エラー種別に応じた処理
  if (err instanceof BusinessError) {
    return res.status(400).json({
      error: {
        code: err.code,
        message: err.message,
        details: err.details,
        traceId: req.id
      }
    });
  }

  // システムエラー（詳細は隠蔽）
  return res.status(500).json({
    error: {
      code: 'SYSTEM_INTERNAL_ERROR',
      message: 'システムエラーが発生しました',
      traceId: req.id
    }
  });
};
```

## 📝 ロギング規約

```yaml
エラーログに含める情報:
  必須:
    - timestamp
    - level (ERROR/WARN)
    - error_code
    - message
    - trace_id
    - service_name
    
  推奨:
    - user_id
    - request_path
    - stack_trace（開発環境）
    - context（関連データ）
    
  禁止:
    - パスワード
    - 個人情報（マスキング必須）
    - APIキー・トークン
```

## 🧪 エラーのテスト

```typescript
// エラーケースのテスト例
describe('注文処理', () => {
  it('在庫不足時にInsufficientStockErrorを投げる', async () => {
    // Arrange
    const product = { id: '123', stock: 5 };
    const orderQuantity = 10;

    // Act & Assert
    await expect(
      orderService.createOrder(product.id, orderQuantity)
    ).rejects.toThrow(InsufficientStockError);
  });
});
```

## 🔍 分散システムでのエラー伝播

```yaml
エラー伝播ルール:
  - 元のエラーコードを保持
  - トレースIDで追跡可能に
  - 各サービスでログ記録
  - タイムアウトは明示的に設定

実装:
  - X-Trace-Id ヘッダーで伝播
  - エラーレスポンスに含める
  - 集約ログで横断的に確認
```