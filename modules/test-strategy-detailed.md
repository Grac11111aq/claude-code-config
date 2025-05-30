# 🧪 詳細テスト戦略規約

<module_info>
このモジュールは、包括的なテスト戦略を定義します。
品質を保証しながら、効率的なテストを実現します。
</module_info>

## 📐 基本規約

### テストピラミッド

```yaml
単体テスト（70%）:
  - 高速実行
  - 独立性が高い
  - ビジネスロジック中心

統合テスト（20%）:
  - 主要な結合部分
  - DB接続、API呼び出し
  - 重要なユースケース

E2Eテスト（10%）:
  - クリティカルパスのみ
  - ユーザーシナリオ
  - 本番環境に近い条件
```

### カバレッジ目標

```yaml
全体: 80%以上
詳細:
  Domain層: 95%以上
  Application層: 85%以上
  Infrastructure層: 70%以上
  ユーティリティ: 100%
```

## 🎯 単体テスト規約

### テスト構造（AAA パターン）

```typescript
describe('OrderService', () => {
  describe('createOrder', () => {
    it('正常に注文を作成する', async () => {
      // Arrange（準備）
      const mockRepository = createMockRepository();
      const service = new OrderService(mockRepository);
      const orderData = { productId: '123', quantity: 2 };

      // Act（実行）
      const result = await service.createOrder(orderData);

      // Assert（検証）
      expect(result.status).toBe('created');
      expect(mockRepository.save).toHaveBeenCalledWith(
        expect.objectContaining(orderData)
      );
    });
  });
});
```

### モックの原則

```yaml
モック対象:
  - 外部依存（DB、API、ファイルシステム）
  - 時間依存の処理
  - ランダム値
  
モックしない:
  - 純粋なビジネスロジック
  - 値オブジェクト
  - ユーティリティ関数
```

## 🔗 統合テスト規約

### データベーステスト

```typescript
describe('UserRepository（統合）', () => {
  let db: TestDatabase;
  
  beforeEach(async () => {
    db = await TestDatabase.create();
    await db.migrate();
  });
  
  afterEach(async () => {
    await db.cleanup();
  });
  
  it('ユーザーを保存して取得できる', async () => {
    // 実際のDBを使用
    const repository = new UserRepository(db);
    const user = new User('test@example.com', 'Test User');
    
    await repository.save(user);
    const found = await repository.findByEmail('test@example.com');
    
    expect(found).toEqual(user);
  });
});
```

### API統合テスト

```typescript
describe('POST /api/users（統合）', () => {
  it('新規ユーザーを作成する', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({
        email: 'new@example.com',
        name: 'New User'
      });
      
    expect(response.status).toBe(201);
    expect(response.body.data).toMatchObject({
      email: 'new@example.com'
    });
    
    // DBに実際に保存されたか確認
    const user = await db.users.findByEmail('new@example.com');
    expect(user).toBeTruthy();
  });
});
```

## 🌐 E2Eテスト規約

### クリティカルパスの定義

```yaml
必須シナリオ:
  - ユーザー登録 → ログイン → プロファイル表示
  - 商品検索 → カート追加 → 購入完了
  - エラー発生 → エラー表示 → リカバリー
```

### Playwright実装例

```typescript
test('ユーザー登録フロー', async ({ page }) => {
  // ページ遷移
  await page.goto('/register');
  
  // フォーム入力
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'SecurePass123');
  await page.click('button[type="submit"]');
  
  // 成功確認
  await expect(page).toHaveURL('/dashboard');
  await expect(page.locator('h1')).toContainText('ダッシュボード');
});
```

## 📋 Contract Testing

### Consumer-Driven Contract

```typescript
// Consumer側（フロントエンド）
describe('UserAPI Contract', () => {
  it('ユーザー情報取得の契約', async () => {
    const interaction = {
      state: 'ユーザーが存在する',
      uponReceiving: 'ユーザー情報取得リクエスト',
      withRequest: {
        method: 'GET',
        path: '/api/users/123'
      },
      willRespondWith: {
        status: 200,
        body: {
          id: '123',
          email: Matchers.email(),
          name: Matchers.string()
        }
      }
    };
    
    await provider.addInteraction(interaction);
  });
});
```

## 🔄 マイクロサービステスト

### サービス間通信テスト

```yaml
戦略:
  - 各サービスは独立してテスト
  - 外部サービスはモック/スタブ
  - Contract Testingで整合性保証
  - ステージング環境で結合確認
```

### テストダブルの使用

```typescript
// サービススタブ
class UserServiceStub {
  async getUser(id: string) {
    return {
      id,
      email: 'stub@example.com',
      name: 'Stub User'
    };
  }
}

// 使用例
const orderService = new OrderService(
  new UserServiceStub(),
  realOrderRepository
);
```

## 🎭 テストデータ管理

### ファクトリーパターン

```typescript
// テストデータファクトリー
export const UserFactory = {
  build: (attrs: Partial<UserData> = {}): UserData => ({
    id: faker.datatype.uuid(),
    email: faker.internet.email(),
    name: faker.name.fullName(),
    ...attrs
  }),
  
  buildList: (count: number): UserData[] => 
    Array.from({ length: count }, () => UserFactory.build())
};

// 使用例
const users = UserFactory.buildList(5);
```

### シードデータ

```yaml
開発環境:
  - 基本的なマスターデータ
  - デモ用ユーザーアカウント
  - 各状態のサンプルデータ

テスト環境:
  - 最小限のマスターデータ
  - テストケース固有のデータは都度生成
```

## 🚀 パフォーマンステスト

```yaml
基準:
  - API応答: 95%ile < 200ms
  - ページロード: < 3秒
  - 同時接続: 1000ユーザー

ツール:
  - k6（API負荷テスト）
  - Lighthouse（フロントエンド）
```

## 🔒 セキュリティテスト

```yaml
自動化:
  - SQLインジェクション検査
  - XSS脆弱性スキャン
  - 依存関係の脆弱性チェック

手動:
  - ペネトレーションテスト（年1回）
  - セキュリティレビュー（リリース前）
```

## 📊 テストレポート

```yaml
必須情報:
  - カバレッジ率
  - テスト実行時間
  - 失敗したテストの詳細
  - パフォーマンス指標

継続的改善:
  - flaky testの追跡
  - 実行時間の最適化
  - カバレッジの向上
```