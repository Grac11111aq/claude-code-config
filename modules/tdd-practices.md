# 🧪 テスト駆動開発（TDD）実践規約

<module_info>
このモジュールは、テスト駆動開発の実践規約を定義します。
「テストファースト」の原則により、品質の高いコードと設計を実現します。
</module_info>

## 📐 基本原則

### Red-Green-Refactor サイクル

```yaml
Red（レッド）:
  - 失敗するテストを書く
  - 要件の明確化
  - インターフェース設計
  
Green（グリーン）:
  - テストを通す最小限の実装
  - 動作するコードの作成
  - 品質は後回し
  
Refactor（リファクタ）:
  - コードの改善
  - 重複除去
  - 設計の洗練
```

### TDDの効果

```yaml
品質向上:
  - バグの早期発見
  - 回帰テストの自動化
  - コードカバレッジ向上

設計改善:
  - テスタブルな設計
  - 疎結合の促進
  - インターフェース思考

開発効率:
  - 仕様の明確化
  - デバッグ時間短縮
  - リファクタリング安全性
```

## 🎯 TDD実践パターン

### 単体テストTDD

```typescript
// 1. Red: 失敗するテストを書く
describe('PasswordValidator', () => {
  it('8文字未満のパスワードを無効とする', () => {
    const validator = new PasswordValidator();
    
    const result = validator.validate('1234567');
    
    expect(result.isValid).toBe(false);
    expect(result.errors).toContain('パスワードは8文字以上必要です');
  });
});

// 2. Green: テストを通す最小限の実装
export class PasswordValidator {
  validate(password: string): ValidationResult {
    if (password.length < 8) {
      return {
        isValid: false,
        errors: ['パスワードは8文字以上必要です']
      };
    }
    
    return { isValid: true, errors: [] };
  }
}

// 3. 次のテストケース追加
describe('PasswordValidator', () => {
  it('大文字を含まないパスワードを無効とする', () => {
    const validator = new PasswordValidator();
    
    const result = validator.validate('password123');
    
    expect(result.isValid).toBe(false);
    expect(result.errors).toContain('大文字を含む必要があります');
  });
});

// 4. Refactor: 実装の改善
export class PasswordValidator {
  private readonly rules: ValidationRule[] = [
    new MinLengthRule(8),
    new UpperCaseRule(),
    new NumberRule()
  ];
  
  validate(password: string): ValidationResult {
    const errors = this.rules
      .filter(rule => !rule.isValid(password))
      .map(rule => rule.errorMessage);
    
    return {
      isValid: errors.length === 0,
      errors
    };
  }
}
```

### 統合テストTDD

```typescript
// API エンドポイントのTDD
describe('POST /api/users', () => {
  it('有効なユーザーデータで新規ユーザーを作成する', async () => {
    // Arrange
    const userData = {
      email: 'user@example.com',
      name: '山田太郎',
      password: 'SecurePass123'
    };
    
    // Act
    const response = await request(app)
      .post('/api/users')
      .send(userData);
    
    // Assert
    expect(response.status).toBe(201);
    expect(response.body.data).toMatchObject({
      email: userData.email,
      name: userData.name
    });
    expect(response.body.data.id).toBeDefined();
    expect(response.body.data.password).toBeUndefined(); // パスワードは返さない
    
    // データベース確認
    const savedUser = await userRepository.findByEmail(userData.email);
    expect(savedUser).toBeTruthy();
  });
});
```

## 🎭 BDD（振る舞い駆動開発）

### Gherkin記法

```gherkin
# features/user-registration.feature
Feature: ユーザー登録
  新規ユーザーがアカウントを作成したい

  Background:
    Given アプリケーションが起動している

  Scenario: 有効な情報での登録成功
    Given ユーザーが登録ページにいる
    When 以下の情報を入力する:
      | フィールド | 値 |
      | メールアドレス | user@example.com |
      | 名前 | 山田太郎 |
      | パスワード | SecurePass123 |
    And "登録" ボタンをクリックする
    Then 登録成功メッセージが表示される
    And ウェルカムメールが送信される

  Scenario: 重複メールアドレスでの登録失敗
    Given メールアドレス "existing@example.com" のユーザーが存在する
    When 同じメールアドレスで登録を試みる
    Then "このメールアドレスは既に使用されています" エラーが表示される
    And ユーザーは作成されない
```

### ステップ定義

```typescript
// steps/user-registration.steps.ts
import { Given, When, Then } from '@cucumber/cucumber';

Given('ユーザーが登録ページにいる', async function() {
  await this.page.goto('/register');
});

When('以下の情報を入力する:', async function(dataTable) {
  const data = dataTable.rowsHash();
  
  await this.page.fill('[name="email"]', data['メールアドレス']);
  await this.page.fill('[name="name"]', data['名前']);
  await this.page.fill('[name="password"]', data['パスワード']);
});

When('"登録" ボタンをクリックする', async function() {
  await this.page.click('button[type="submit"]');
});

Then('登録成功メッセージが表示される', async function() {
  const message = await this.page.locator('.success-message');
  await expect(message).toContainText('登録が完了しました');
});
```

## 🔧 テストダブル活用

### モック・スタブ戦略

```typescript
// 依存関係の分離テスト
describe('OrderService', () => {
  let orderService: OrderService;
  let mockUserRepository: jest.Mocked<UserRepository>;
  let mockPaymentService: jest.Mocked<PaymentService>;
  let mockEmailService: jest.Mocked<EmailService>;
  
  beforeEach(() => {
    mockUserRepository = {
      findById: jest.fn(),
      save: jest.fn()
    } as any;
    
    mockPaymentService = {
      charge: jest.fn()
    } as any;
    
    mockEmailService = {
      sendOrderConfirmation: jest.fn()
    } as any;
    
    orderService = new OrderService(
      mockUserRepository,
      mockPaymentService,
      mockEmailService
    );
  });
  
  it('注文処理で決済と通知が実行される', async () => {
    // Arrange
    const user = new User('user-123', 'user@example.com');
    const orderData = { userId: 'user-123', amount: 10000 };
    
    mockUserRepository.findById.mockResolvedValue(user);
    mockPaymentService.charge.mockResolvedValue({ success: true });
    
    // Act
    await orderService.processOrder(orderData);
    
    // Assert
    expect(mockPaymentService.charge).toHaveBeenCalledWith({
      amount: 10000,
      user: user
    });
    expect(mockEmailService.sendOrderConfirmation).toHaveBeenCalledWith(
      expect.objectContaining({ userId: 'user-123' })
    );
  });
});
```

### テストデータビルダー

```typescript
// テストデータの生成パターン
export class UserTestDataBuilder {
  private data: Partial<User> = {
    id: 'test-user-id',
    email: 'test@example.com',
    name: 'テストユーザー',
    status: 'active'
  };
  
  withId(id: string): UserTestDataBuilder {
    this.data.id = id;
    return this;
  }
  
  withEmail(email: string): UserTestDataBuilder {
    this.data.email = email;
    return this;
  }
  
  withInactiveStatus(): UserTestDataBuilder {
    this.data.status = 'inactive';
    return this;
  }
  
  build(): User {
    return new User(this.data as any);
  }
}

// 使用例
describe('UserService', () => {
  it('非アクティブユーザーはログインできない', () => {
    const inactiveUser = new UserTestDataBuilder()
      .withEmail('inactive@example.com')
      .withInactiveStatus()
      .build();
    
    expect(() => userService.login(inactiveUser))
      .toThrow('ユーザーアカウントが無効です');
  });
});
```

## 📊 テストカバレッジ管理

### 段階的カバレッジ目標

```yaml
フェーズ1（初期）:
  目標: 50%
  重点: コアビジネスロジック
  対象: Domain層、主要ユースケース

フェーズ2（成長）:
  目標: 70%
  重点: 統合テスト追加
  対象: Application層、API層

フェーズ3（成熟）:
  目標: 85%
  重点: エッジケース
  対象: Infrastructure層、エラーケース
```

### カバレッジ計測

```typescript
// jest.config.js
module.exports = {
  collectCoverage: true,
  coverageDirectory: 'coverage',
  coverageReporters: ['text', 'lcov', 'html'],
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/test/**'
  ],
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 80,
      lines: 75,
      statements: 75
    },
    './src/domain/': {
      branches: 90,
      functions: 95,
      lines: 90,
      statements: 90
    }
  }
};
```

## 🚀 TDDツールチェーン

### 自動テスト実行

```json
// package.json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:ci": "jest --ci --coverage --watchAll=false"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "@testing-library/react": "^13.0.0",
    "@testing-library/jest-dom": "^5.16.0",
    "supertest": "^6.3.0"
  }
}
```

### CI統合

```yaml
# .github/workflows/test.yml
name: Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      - run: npm run test:ci
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
```

## 🧠 TDD思考プロセス

### 要件からテストケースへ

```yaml
1. 要件分析:
   - ユーザーストーリーの理解
   - 受け入れ条件の明確化
   - エッジケースの特定

2. テストケース設計:
   - ハッピーパス
   - エラーケース
   - 境界値テスト

3. テスト優先順位:
   - ビジネス価値の高い順
   - リスクの高い順
   - 実装の複雑さ考慮
```

### デバッグとTDD

```typescript
// テスト失敗時のデバッグアプローチ
describe('OrderCalculator', () => {
  it('税込み価格を正しく計算する', () => {
    const calculator = new OrderCalculator();
    
    // デバッグ情報の出力
    console.log('テストデータ:', { price: 1000, tax: 0.1 });
    
    const result = calculator.calculateWithTax(1000, 0.1);
    
    // 中間値の確認
    console.log('計算結果:', result);
    console.log('期待値:', 1100);
    
    expect(result).toBe(1100);
  });
});

// 実装でのデバッグポイント
export class OrderCalculator {
  calculateWithTax(price: number, taxRate: number): number {
    const tax = price * taxRate;
    const total = price + tax;
    
    // デバッグログ
    if (process.env.NODE_ENV === 'test') {
      console.log(`price: ${price}, tax: ${tax}, total: ${total}`);
    }
    
    return total;
  }
}
```

## 📋 TDD実践チェックリスト

```yaml
テスト作成前:
  - [ ] 要件は明確か
  - [ ] テストケースは網羅的か
  - [ ] テストは独立性を保っているか

実装中:
  - [ ] 最小限の実装で止めているか
  - [ ] テストが通ることを確認したか
  - [ ] リファクタリングで品質向上したか

完了時:
  - [ ] すべてのテストが通るか
  - [ ] カバレッジ目標を達成したか
  - [ ] テストは保守しやすいか
```