# 📊 ログ・モニタリング規約

<module_info>
このモジュールは、ロギングとモニタリングの規約を定義します。
適切なログ設計により、トラブルシューティングと運用監視を効率化します。
</module_info>

## 📐 基本規約

### ログレベル

```yaml
ERROR: エラー・例外
WARN: 警告・非推奨機能の使用
INFO: 重要なビジネスイベント
DEBUG: デバッグ情報（開発環境のみ）
```

### ログ形式

```json
{
  "timestamp": "2024-01-01T00:00:00.000Z",
  "level": "INFO",
  "service": "api",
  "message": "ユーザー登録完了",
  "context": {
    "userId": "123",
    "requestId": "abc-123",
    "duration": 150
  }
}
```

## 🏷️ 構造化ログ

### 必須フィールド

```yaml
timestamp: ISO 8601形式
level: ログレベル
service: サービス名
message: 日本語の説明文
requestId: リクエスト追跡用ID
```

### コンテキスト情報

```yaml
ユーザー操作:
  - userId
  - action
  - resource
  
パフォーマンス:
  - duration
  - memory
  - cpu
  
エラー:
  - error_code
  - error_type
  - stack_trace（開発環境のみ）
```

## 🚫 ログに含めてはいけない情報

```yaml
禁止:
  - パスワード
  - APIキー・トークン
  - クレジットカード情報
  - 個人情報（必要な場合はマスク）
```

## 📈 メトリクス

### 基本メトリクス

```yaml
システム:
  - CPU使用率
  - メモリ使用率
  - ディスク使用率
  
アプリケーション:
  - リクエスト数
  - レスポンスタイム
  - エラー率
  - 同時接続数
```

### アラート閾値

```yaml
緊急（即時対応）:
  - エラー率 > 5%
  - レスポンスタイム > 3秒
  - CPU使用率 > 90%（5分継続）
  
警告（監視強化）:
  - エラー率 > 1%
  - レスポンスタイム > 1秒
  - メモリ使用率 > 80%
```

## 🔍 ログの実装例

### Node.js (winston)

```javascript
const logger = winston.createLogger({
  format: winston.format.json(),
  defaultMeta: { service: 'api' },
  transports: [
    new winston.transports.Console({
      format: winston.format.simple(),
      level: process.env.LOG_LEVEL || 'info'
    })
  ]
});

// 使用例
logger.info('ユーザー登録完了', {
  userId: user.id,
  requestId: req.id,
  duration: Date.now() - startTime
});
```

## 📁 ログ保持期間

```yaml
開発環境: 7日
ステージング: 30日
本番環境:
  - アプリケーションログ: 90日
  - アクセスログ: 1年
  - エラーログ: 1年
  - 監査ログ: 法的要件に準拠
```

## 🎯 ダッシュボード

```yaml
必須ダッシュボード:
  1. システムヘルス（CPU、メモリ、ディスク）
  2. アプリケーションパフォーマンス（レスポンスタイム、スループット）
  3. エラー監視（エラー率、エラー種別）
  4. ビジネスメトリクス（ユーザー数、取引数等）
```