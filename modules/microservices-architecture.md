# 🔧 マイクロサービス・アーキテクチャ規約

<module_info>
このモジュールは、マイクロサービスアーキテクチャの規約を定義します。
段階的な成長を前提とし、モノリスから始めて必要に応じて分割する戦略を採用します。
</module_info>

## 📐 基本規約

### アーキテクチャ進化戦略

```yaml
フェーズ1（MVP）: モジュラーモノリス
  - クリーンアーキテクチャで内部構造化
  - 将来の分割を見据えた境界設計
  
フェーズ2（成長期）: 部分的マイクロサービス化
  - ボトルネックとなる機能から分離
  - イベント駆動の導入
  
フェーズ3（成熟期）: フルマイクロサービス
  - 独立したデプロイサイクル
  - 完全なイベント駆動アーキテクチャ
```

### サービス分割基準

```yaml
分割トリガー:
  - 異なるスケーリング要件
  - 独立したデプロイサイクルの必要性
  - チーム（将来的に）の責任境界
  - 技術スタックの相違

分割単位:
  - ビジネスケイパビリティ（推奨）
  - データの整合性境界
  - 1サービス = 1データベース原則
```

## 🏗️ クリーンアーキテクチャ実装

### レイヤー構成

```yaml
各マイクロサービス内:
  domain/       # ビジネスロジック（最内層）
    ├── entities/     # エンティティ
    ├── usecases/     # ユースケース
    └── repositories/ # リポジトリインターフェース
    
  application/  # アプリケーション層
    ├── services/     # アプリケーションサービス
    └── dto/          # データ変換オブジェクト
    
  infrastructure/ # インフラ層（最外層）
    ├── api/          # REST/GraphQL エンドポイント
    ├── database/     # データベース実装
    ├── messaging/    # メッセージング実装
    └── external/     # 外部サービス連携
    
  interfaces/   # インターフェース定義
    └── events/       # イベントスキーマ
```

### 依存関係の方向

```
外側 → 内側への依存のみ許可
infrastructure → application → domain
（domainは何にも依存しない）
```

## 📨 イベント駆動設計

### イベントパターン

```yaml
イベントタイプ:
  Domain Events: ビジネスイベント
  Integration Events: サービス間連携
  System Events: システムイベント

命名規則:
  形式: {Entity}{Action}Event
  例: UserCreatedEvent, OrderCompletedEvent
```

### イベントスキーマ

```json
{
  "eventId": "uuid",
  "eventType": "UserCreated",
  "timestamp": "2024-01-01T00:00:00Z",
  "aggregateId": "user-123",
  "payload": {
    // イベント固有のデータ
  },
  "metadata": {
    "correlationId": "request-123",
    "userId": "admin-456"
  }
}
```

### メッセージングパターン

```yaml
パターン選択:
  Request-Response: 同期的な処理
  Publish-Subscribe: 非同期通知
  Event Sourcing: 監査要件が高い場合

実装選択:
  開発初期: インメモリイベントバス
  本番: RabbitMQ or Kafka（要件による）
```

## 🎯 オーケストレーション

### API Gateway パターン

```yaml
責務:
  - ルーティング
  - 認証・認可
  - レート制限
  - ログ集約
  
実装:
  シンプル: nginx + Lua
  高機能: Kong, Envoy
```

### サービスメッシュ（将来的オプション）

```yaml
導入タイミング:
  - サービス数が10を超える
  - 複雑なトラフィック制御が必要
  - 詳細な可観測性が必要
```

## 📊 実装例

### モジュラーモノリスの構造

```typescript
// 初期フェーズ：モノリス内でのモジュール分離
src/
├── modules/
│   ├── user/
│   │   ├── domain/
│   │   ├── application/
│   │   └── infrastructure/
│   ├── order/
│   │   ├── domain/
│   │   ├── application/
│   │   └── infrastructure/
│   └── shared/
│       ├── events/
│       └── interfaces/
└── main.ts
```

### サービス間通信

```typescript
// イベント発行
class UserService {
  async createUser(data: CreateUserDto): Promise<User> {
    const user = await this.userRepository.save(data);
    
    // イベント発行
    await this.eventBus.publish(new UserCreatedEvent({
      userId: user.id,
      email: user.email,
      timestamp: new Date()
    }));
    
    return user;
  }
}

// イベント購読
class NotificationService {
  @EventHandler(UserCreatedEvent)
  async handleUserCreated(event: UserCreatedEvent): Promise<void> {
    await this.sendWelcomeEmail(event.email);
  }
}
```

## 🚀 段階的移行戦略

```yaml
ステップ1: モノリス内での境界明確化
ステップ2: 共有データベースの分離準備
ステップ3: 最初のサービス切り出し（通常は認証サービス）
ステップ4: イベントバスの導入
ステップ5: 段階的なサービス分離
```

## ⚠️ 注意事項

```yaml
避けるべきこと:
  - 早すぎる最適化（過度な分割）
  - 分散モノリス（密結合なマイクロサービス）
  - 同期的な連鎖呼び出し
  
推奨事項:
  - まずモノリスで動作確認
  - 明確な境界を持つサービスから分離
  - イベント駆動で疎結合を維持
```