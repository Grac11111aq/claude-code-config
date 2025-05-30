# 🏛️ クリーンアーキテクチャ実装規約

<module_info>
このモジュールは、クリーンアーキテクチャの実装規約を定義します。
ビジネスロジックの独立性と、変更への柔軟性を確保します。
</module_info>

## 📐 基本原則

### 依存性のルール

```yaml
依存の方向: 外側 → 内側のみ
レイヤー:
  1. Domain（最内層）: 純粋なビジネスロジック
  2. Application: ユースケースの実装
  3. Infrastructure（最外層）: 技術的詳細
  
禁止事項:
  - 内側のレイヤーが外側を知ること
  - フレームワーク固有のコードがDomain層に入ること
```

## 🎯 レイヤー詳細

### Domain層

```typescript
// domain/entities/User.ts
export class User {
  constructor(
    private readonly id: string,
    private email: string,
    private name: string
  ) {
    this.validate();
  }

  private validate(): void {
    if (!this.email.includes('@')) {
      throw new InvalidEmailError();
    }
  }

  changeEmail(newEmail: string): void {
    // ビジネスルールの実装
    this.email = newEmail;
    this.validate();
  }
}

// domain/repositories/UserRepository.ts
export interface UserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

### Application層

```typescript
// application/usecases/CreateUserUseCase.ts
export class CreateUserUseCase {
  constructor(
    private userRepository: UserRepository,
    private eventPublisher: EventPublisher
  ) {}

  async execute(input: CreateUserInput): Promise<CreateUserOutput> {
    // ビジネスルールの実行
    const user = new User(
      generateId(),
      input.email,
      input.name
    );

    // 永続化
    await this.userRepository.save(user);

    // イベント発行
    await this.eventPublisher.publish(
      new UserCreatedEvent(user.id)
    );

    return { userId: user.id };
  }
}
```

### Infrastructure層

```typescript
// infrastructure/database/PostgresUserRepository.ts
export class PostgresUserRepository implements UserRepository {
  async findById(id: string): Promise<User | null> {
    const data = await this.db.query(
      'SELECT * FROM users WHERE id = $1',
      [id]
    );
    
    return data ? this.toDomain(data) : null;
  }

  private toDomain(data: any): User {
    return new User(data.id, data.email, data.name);
  }
}
```

## 📦 ディレクトリ構造

```bash
src/
├── domain/           # ビジネスロジック
│   ├── entities/     # エンティティ
│   ├── valueObjects/ # 値オブジェクト
│   ├── events/       # ドメインイベント
│   ├── exceptions/   # ドメイン例外
│   └── repositories/ # リポジトリインターフェース
│
├── application/      # アプリケーションロジック
│   ├── useCases/     # ユースケース
│   ├── dto/          # データ転送オブジェクト
│   └── services/     # アプリケーションサービス
│
└── infrastructure/   # 技術的実装
    ├── api/          # コントローラー
    ├── database/     # データベース実装
    ├── messaging/    # メッセージング
    └── config/       # 設定
```

## 🔄 データフロー

```yaml
リクエストフロー:
  1. Controller（Infrastructure）がリクエスト受信
  2. UseCase（Application）を呼び出し
  3. Domain層でビジネスロジック実行
  4. Repository（Infrastructure）でデータ永続化
  5. レスポンス返却

データ変換:
  - Infrastructure → Application: DTO
  - Application → Domain: Domain Objects
  - Domain → Application: Domain Objects
  - Application → Infrastructure: DTO
```

## 🎨 実装パターン

### Factory パターン

```typescript
// domain/factories/UserFactory.ts
export class UserFactory {
  static createFromRequest(data: any): User {
    // 複雑な生成ロジック
    return new User(
      generateId(),
      data.email,
      data.name
    );
  }
}
```

### Repository パターン

```typescript
// 抽象化されたインターフェース
interface Repository<T> {
  findById(id: string): Promise<T | null>;
  save(entity: T): Promise<void>;
  delete(id: string): Promise<void>;
}
```

## ⚡ ベストプラクティス

```yaml
Domain層:
  - 純粋関数を心がける
  - ビジネスルールを集約
  - 技術的な詳細を含めない

Application層:
  - 薄く保つ（オーケストレーションのみ）
  - トランザクション境界の管理
  - 横断的関心事の処理

Infrastructure層:
  - 技術的な詳細をカプセル化
  - 外部サービスとの連携
  - 設定管理
```

## 🧪 テスト戦略

```yaml
Domain層:
  - 単体テストで100%カバレッジ目標
  - モックなしでテスト可能

Application層:
  - モックを使用した統合テスト
  - ユースケースシナリオのテスト

Infrastructure層:
  - 実際のDBを使用した統合テスト
  - 外部サービスのモック
```

## ⚠️ アンチパターン

```yaml
避けるべきこと:
  - Domain層でのフレームワーク依存
  - 貧血ドメインモデル
  - ユースケースの肥大化
  - レイヤー間の循環参照

注意点:
  - 過度な抽象化は避ける
  - YAGNI原則を忘れない
  - 段階的にクリーンにする
```