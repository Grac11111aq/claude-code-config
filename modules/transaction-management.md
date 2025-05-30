# 💰 トランザクション管理規約

<module_info>
このモジュールは、トランザクション管理の規約を定義します。
分散システムでの結果整合性とデータ整合性を実現します。
</module_info>

## 📐 基本規約

### トランザクション境界

```yaml
原則:
  - 1ユースケース = 1トランザクション
  - Application層で管理
  - Domain層はトランザクションを意識しない

実装場所:
  モノリス: データベーストランザクション
  分散システム: Sagaパターン
```

## 🔄 ローカルトランザクション

### 実装パターン

```typescript
// Application層でのトランザクション管理
export class CreateOrderUseCase {
  async execute(input: CreateOrderInput): Promise<CreateOrderOutput> {
    return await this.db.transaction(async (trx) => {
      // 在庫確認・更新
      const product = await this.productRepo.findById(input.productId, trx);
      product.decreaseStock(input.quantity);
      await this.productRepo.save(product, trx);
      
      // 注文作成
      const order = new Order(input);
      await this.orderRepo.save(order, trx);
      
      // イベント発行（トランザクション内）
      await this.eventStore.save(
        new OrderCreatedEvent(order.id),
        trx
      );
      
      return { orderId: order.id };
    });
  }
}
```

### アイソレーションレベル

```yaml
デフォルト: READ COMMITTED
使い分け:
  READ UNCOMMITTED: 使用禁止
  READ COMMITTED: 通常のCRUD操作
  REPEATABLE READ: 在庫管理等の重要な更新
  SERIALIZABLE: 金銭処理等の最重要処理
```

## 🌐 分散トランザクション（Sagaパターン）

### Choreography Saga（推奨）

```yaml
特徴:
  - 各サービスが自律的に動作
  - イベント駆動で連携
  - 単一障害点なし

実装:
  1. 各サービスがイベントを発行
  2. 他サービスがイベントを購読・処理
  3. 失敗時は補償イベントを発行
```

```typescript
// 注文サービス
class OrderService {
  async createOrder(data: OrderData) {
    const order = new Order(data);
    await this.orderRepo.save(order);
    
    // イベント発行
    await this.eventBus.publish(
      new OrderCreatedEvent({
        orderId: order.id,
        userId: data.userId,
        items: data.items,
        totalAmount: order.totalAmount
      })
    );
  }
  
  // 補償トランザクション
  @EventHandler(PaymentFailedEvent)
  async handlePaymentFailed(event: PaymentFailedEvent) {
    await this.cancelOrder(event.orderId);
    await this.eventBus.publish(
      new OrderCancelledEvent(event.orderId)
    );
  }
}

// 在庫サービス
class InventoryService {
  @EventHandler(OrderCreatedEvent)
  async handleOrderCreated(event: OrderCreatedEvent) {
    try {
      await this.reserveItems(event.items);
      await this.eventBus.publish(
        new ItemsReservedEvent(event.orderId)
      );
    } catch (error) {
      await this.eventBus.publish(
        new ItemsReservationFailedEvent(event.orderId)
      );
    }
  }
}
```

### Orchestration Saga（複雑な場合）

```typescript
// Sagaオーケストレーター
class OrderSaga {
  private steps = [
    { service: 'inventory', action: 'reserve', compensate: 'release' },
    { service: 'payment', action: 'charge', compensate: 'refund' },
    { service: 'shipping', action: 'schedule', compensate: 'cancel' }
  ];
  
  async execute(orderId: string) {
    const executedSteps = [];
    
    try {
      for (const step of this.steps) {
        await this.executeStep(step, orderId);
        executedSteps.push(step);
      }
    } catch (error) {
      // 補償トランザクション実行
      for (const step of executedSteps.reverse()) {
        await this.compensateStep(step, orderId);
      }
      throw error;
    }
  }
}
```

## 🔐 楽観的ロック

```typescript
// バージョン番号による楽観的ロック
export class Product {
  constructor(
    private id: string,
    private stock: number,
    private version: number
  ) {}
  
  decreaseStock(quantity: number): void {
    if (this.stock < quantity) {
      throw new InsufficientStockError();
    }
    this.stock -= quantity;
    this.version++; // バージョン更新
  }
}

// リポジトリでの実装
class ProductRepository {
  async save(product: Product): Promise<void> {
    const result = await this.db.query(
      `UPDATE products 
       SET stock = $1, version = $2 
       WHERE id = $3 AND version = $4`,
      [product.stock, product.version, product.id, product.version - 1]
    );
    
    if (result.rowCount === 0) {
      throw new OptimisticLockError('Product was modified');
    }
  }
}
```

## 📊 イベントソーシング

```yaml
適用基準:
  - 完全な監査証跡が必要
  - 時系列での状態復元が必要
  - イベント駆動が中心的

実装:
  - イベントストアに全ての変更を記録
  - 現在の状態はイベントから再構築
  - スナップショットで性能最適化
```

```typescript
// イベントストア実装
class EventStore {
  async save(event: DomainEvent): Promise<void> {
    await this.db.insert('events', {
      aggregate_id: event.aggregateId,
      event_type: event.type,
      payload: JSON.stringify(event.payload),
      timestamp: event.timestamp,
      version: event.version
    });
  }
  
  async getEvents(aggregateId: string): Promise<DomainEvent[]> {
    const rows = await this.db.query(
      'SELECT * FROM events WHERE aggregate_id = $1 ORDER BY version',
      [aggregateId]
    );
    return rows.map(row => this.deserialize(row));
  }
}
```

## ⚡ パフォーマンス考慮

```yaml
バッチ処理:
  - 大量データは分割処理
  - 進捗の永続化
  - 再開可能な設計

接続プール:
  最小: 10
  最大: 100
  タイムアウト: 30秒
```

## 🚨 デッドロック対策

```yaml
予防策:
  - リソースの取得順序を統一
  - タイムアウト設定
  - 最小限のロック範囲

検出と対処:
  - デッドロック検出ログ
  - 自動リトライ（最大3回）
  - エクスポネンシャルバックオフ
```

## 📋 トランザクションログ

```yaml
記録内容:
  - トランザクションID
  - 開始・終了時刻
  - 影響を受けたエンティティ
  - 成功/失敗/補償の状態
  
保持期間:
  開発: 7日
  本番: 90日（監査要件による）
```