# 🔄 開発手法統合ガイドライン

<module_info>
このモジュールは、様々な開発手法の統合的な活用指針を定義します。
プロジェクトの状況に応じて適切な手法を選択・組み合わせることで、
効率的で品質の高い開発を実現します。
</module_info>

## 📐 基本方針

### 手法の組み合わせ戦略

```yaml
コア手法（必須）:
  - アジャイル（スクラム/カンバン）
  - ドキュメント駆動開発
  - テスト駆動開発（TDD/BDD）

補完手法（状況により選択）:
  - ドメイン駆動設計（DDD）
  - リーン開発
  - 継続的インテグレーション（CI/CD）
```

### フェーズ別適用

```yaml
プロジェクト初期:
  1. ドキュメント駆動（要件・設計の明確化）
  2. リーン（MVP定義、仮説検証）
  3. アジャイル（短期スプリント設定）

開発フェーズ:
  1. TDD（品質確保）
  2. アジャイル（継続的デリバリー）
  3. DDD（複雑なドメインの場合）

成熟フェーズ:
  1. リーン（継続的改善）
  2. ドキュメント駆動（知識の維持）
  3. アジャイル（機能追加・保守）
```

## 📚 ドキュメント駆動開発（Documentation-Driven Development）

### 基本原則

```yaml
"コードより先にドキュメント":
  - API仕様書 → 実装
  - ユーザーストーリー → 機能開発
  - アーキテクチャ図 → システム設計
  
生きたドキュメント:
  - 実装と同期
  - 自動生成活用
  - 継続的更新
```

### 実装例

```typescript
/**
 * ユーザー注文API
 * 
 * @description
 * ユーザーが商品を注文するためのAPIエンドポイント。
 * 在庫確認、価格計算、決済処理を含む一連の注文フローを実行する。
 * 
 * @example
 * ```typescript
 * const order = await orderService.createOrder({
 *   userId: 'user-123',
 *   items: [{ productId: 'prod-456', quantity: 2 }]
 * });
 * ```
 * 
 * @throws {InsufficientStockError} 在庫不足の場合
 * @throws {PaymentFailedError} 決済失敗の場合
 */
export class OrderService {
  async createOrder(request: CreateOrderRequest): Promise<Order> {
    // 実装
  }
}
```

## 🏃 アジャイル開発プラクティス

### スクラム適用（チーム開発時）

```yaml
スプリント設定:
  期間: 1-2週間
  目標: 動作するソフトウェアの定期的デリバリー
  
イベント:
  スプリントプランニング: 週初（1時間）
  デイリースタンドアップ: 毎日（15分）
  スプリントレビュー: 週末（30分）
  レトロスペクティブ: スプリント終了時（30分）

成果物:
  - ユーザーストーリー
  - 動作するソフトウェア
  - バーンダウンチャート
```

### カンバン適用（1人開発時）

```yaml
ボード構成:
  Backlog → To Do → In Progress → Review → Done
  
WIP制限:
  In Progress: 2タスクまで
  Review: 1タスクまで
  
計測指標:
  - リードタイム
  - サイクルタイム
  - スループット
```

## 🧪 テスト駆動開発（TDD/BDD）

### Red-Green-Refactor サイクル

```typescript
// 1. Red: 失敗するテストを書く
describe('OrderService', () => {
  it('有効な注文を作成できる', async () => {
    const orderData = { userId: 'user-123', items: [...] };
    
    const order = await orderService.createOrder(orderData);
    
    expect(order.status).toBe('PENDING');
    expect(order.items).toHaveLength(2);
  });
});

// 2. Green: テストを通す最小限の実装
export class OrderService {
  async createOrder(data: OrderData): Promise<Order> {
    return new Order('PENDING', data.items);
  }
}

// 3. Refactor: コードを改善
export class OrderService {
  async createOrder(data: OrderData): Promise<Order> {
    this.validateOrderData(data);
    const order = Order.create(data);
    await this.orderRepository.save(order);
    return order;
  }
}
```

### BDD（振る舞い駆動開発）

```gherkin
# features/order.feature
Feature: 注文処理
  ユーザーとして商品を注文したい

  Scenario: 正常な注文処理
    Given ユーザー "user-123" が存在する
    And 商品 "product-456" の在庫が 10個 ある
    When ユーザーが 2個 の商品を注文する
    Then 注文が正常に作成される
    And 在庫が 8個 に減る
```

## 📈 リーン開発手法

### Build-Measure-Learn サイクル

```yaml
Build（構築）:
  - MVP（最小実行可能製品）の開発
  - 最小限の機能で仮説を検証
  - 高速な実装とデプロイ

Measure（計測）:
  - ユーザー行動の分析
  - KPIの測定
  - A/Bテストの実施

Learn（学習）:
  - データからの洞察抽出
  - 仮説の検証・修正
  - 次のイテレーションの計画
```

### 実装例

```typescript
// MVP機能の実装例
export class MinimalOrderService {
  // 最小限の注文機能のみ
  async createOrder(userId: string, productId: string): Promise<Order> {
    // 複雑な在庫管理、価格計算は後回し
    const order = new Order(userId, productId);
    await this.simpleOrderRepository.save(order);
    
    // メトリクス収集
    this.analytics.track('order_created', {
      userId,
      productId,
      timestamp: new Date()
    });
    
    return order;
  }
}
```

## 🏗️ ドメイン駆動設計（DDD）統合

### 他手法との組み合わせ

```yaml
DDD + アジャイル:
  - ユーザーストーリーをドメインイベントに対応
  - スプリント単位でバウンデッドコンテキストを実装
  - ドメインエキスパートをプロダクトオーナーに

DDD + TDD:
  - ドメインモデルのテストから開始
  - ユビキタス言語をテスト名に反映
  - アグリゲート境界をテストで検証

DDD + ドキュメント駆動:
  - ドメインモデル図を先に作成
  - ユビキタス言語の用語集を維持
  - イベントストーミングの結果を文書化
```

## 🔄 継続的改善（カイゼン）

### レトロスペクティブ実践

```yaml
1週間ごとの振り返り:
  What went well? （良かったこと）
  What could be improved? （改善点）
  What will we try next? （次に試すこと）

改善アクション:
  - 小さな改善を継続的に実施
  - 実験的アプローチで検証
  - 効果測定と振り返り
```

## 📊 メトリクス主導改善

### 開発効率メトリクス

```yaml
技術メトリクス:
  - デプロイ頻度
  - リードタイム
  - 変更失敗率
  - 復旧時間

ビジネスメトリクス:
  - ユーザー満足度
  - 機能使用率
  - バグ報告数
  - 開発速度
```

## 🎯 プロジェクト別適用戦略

### 小規模プロジェクト（1-2人、3ヶ月以内）

```yaml
採用手法:
  - カンバン（シンプルなタスク管理）
  - ドキュメント駆動（最小限）
  - TDD（コア機能のみ）
  - リーン（MVP重視）

省略可能:
  - 複雑なスクラムプロセス
  - 重厚なDDD実装
  - 過度なドキュメント
```

### 中規模プロジェクト（3-5人、6ヶ月-1年）

```yaml
採用手法:
  - スクラム（2週間スプリント）
  - ドキュメント駆動（構造化）
  - TDD/BDD（包括的）
  - DDD（複雑なドメインの場合）
  - CI/CD（自動化）
```

### 大規模プロジェクト（6人以上、1年以上）

```yaml
採用手法:
  - スケールドアジャイル（SAFe等）
  - 包括的ドキュメント駆動
  - TDD/BDD/DDD（フル活用）
  - リーン（継続的改善）
  - マイクロサービス対応
```

## 📋 実践チェックリスト

```yaml
プロジェクト開始時:
  - [ ] 適用する開発手法の選定
  - [ ] チーム規模に応じたプロセス調整
  - [ ] ドキュメント構造の定義
  - [ ] テスト戦略の策定

開発中:
  - [ ] 定期的なレトロスペクティブ
  - [ ] メトリクスの収集・分析
  - [ ] ドキュメントの継続更新
  - [ ] テストファーストの実践

プロジェクト完了時:
  - [ ] 学習した知見の文書化
  - [ ] 次プロジェクトへの改善提案
  - [ ] ツール・プロセスの評価
```