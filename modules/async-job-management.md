# ⚡ 非同期処理・ジョブ管理規約

<module_info>
このモジュールは、非同期処理とバックグラウンドジョブの管理規約を定義します。
スケーラブルで信頼性の高い非同期処理を実現します。
</module_info>

## 📐 基本規約

### ジョブの分類

```yaml
即時実行ジョブ:
  - メール送信
  - 通知配信
  - 画像処理
  
定期実行ジョブ:
  - 日次レポート生成
  - データクリーンアップ
  - バックアップ
  
長時間実行ジョブ:
  - バッチインポート
  - 大量データ処理
  - レポート生成
```

### キュー選択

```yaml
デフォルト: Redis（Bull/BullMQ）
理由:
  - シンプルで高速
  - 優先度付きキュー
  - 遅延実行サポート

大規模時: RabbitMQ/Kafka
条件:
  - 複数のワーカープロセス
  - 高度なルーティング
  - メッセージ永続化必須
```

## 🎯 ジョブ設計原則

### 冪等性の確保

```typescript
// 冪等なジョブの実装
export class SendEmailJob implements Job {
  async process(data: EmailJobData): Promise<void> {
    // 既に送信済みかチェック
    const alreadySent = await this.emailLog.exists({
      userId: data.userId,
      templateId: data.templateId,
      date: data.date
    });
    
    if (alreadySent) {
      logger.info('Email already sent', { jobId: data.jobId });
      return;
    }
    
    // メール送信
    await this.emailService.send(data);
    
    // 送信ログ記録
    await this.emailLog.create({
      userId: data.userId,
      templateId: data.templateId,
      date: data.date,
      sentAt: new Date()
    });
  }
}
```

### ジョブの粒度

```yaml
推奨:
  - 1ジョブ = 1つの明確なタスク
  - 実行時間: 1分以内を目標
  - 大きなタスクは分割

例:
  NG: "1000件のユーザーにメール送信"
  OK: "1ユーザーにメール送信" × 1000ジョブ
```

## 📦 実装パターン

### Bull/BullMQでの実装

```typescript
// キューの定義
import { Queue, Worker } from 'bullmq';

// キュー作成
const emailQueue = new Queue('email', {
  connection: redis,
  defaultJobOptions: {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000
    },
    removeOnComplete: true,
    removeOnFail: false
  }
});

// ジョブの追加
await emailQueue.add('send-welcome', {
  userId: user.id,
  email: user.email
}, {
  priority: 1,
  delay: 5000 // 5秒後に実行
});

// ワーカーの定義
const emailWorker = new Worker('email', async (job) => {
  switch (job.name) {
    case 'send-welcome':
      await sendWelcomeEmail(job.data);
      break;
    case 'send-reminder':
      await sendReminderEmail(job.data);
      break;
  }
}, {
  connection: redis,
  concurrency: 5
});

// エラーハンドリング
emailWorker.on('failed', (job, err) => {
  logger.error('Job failed', {
    jobId: job.id,
    jobName: job.name,
    error: err.message,
    attemptsMade: job.attemptsMade
  });
});
```

### スケジュールジョブ

```typescript
// cron式での定期実行
await queue.add(
  'daily-report',
  { date: new Date() },
  {
    repeat: {
      pattern: '0 9 * * *', // 毎日9時
      tz: 'Asia/Tokyo'
    }
  }
);

// 高度なスケジューリング
class ScheduledJobs {
  async initialize() {
    // 日次クリーンアップ
    await this.scheduleJob('cleanup', '0 2 * * *', async () => {
      await this.cleanupOldData();
    });
    
    // 週次レポート
    await this.scheduleJob('weekly-report', '0 10 * * MON', async () => {
      await this.generateWeeklyReport();
    });
  }
}
```

## 🔄 進捗管理

### ジョブの進捗追跡

```typescript
export class LongRunningJob {
  async process(job: Job<ImportData>) {
    const totalItems = job.data.items.length;
    let processed = 0;
    
    for (const item of job.data.items) {
      await this.processItem(item);
      processed++;
      
      // 進捗更新
      await job.updateProgress(Math.floor((processed / totalItems) * 100));
      
      // 定期的にハートビート
      if (processed % 100 === 0) {
        await job.extendLock(30000); // 30秒延長
      }
    }
  }
}
```

### ジョブのモニタリング

```yaml
必須メトリクス:
  - キューの長さ
  - 処理中のジョブ数
  - 成功/失敗率
  - 平均処理時間
  
アラート条件:
  - キュー滞留: 1000件以上
  - 失敗率: 5%以上
  - 処理遅延: 平均5分以上
```

## 🚨 エラーハンドリング

### リトライ戦略

```typescript
const jobOptions = {
  attempts: 5,
  backoff: {
    type: 'exponential',
    delay: 2000,    // 初回2秒
    maxDelay: 60000 // 最大60秒
  },
  // カスタムリトライ条件
  shouldRetry: (err: Error, attemptsMade: number) => {
    // ネットワークエラーはリトライ
    if (err.message.includes('ECONNREFUSED')) {
      return attemptsMade < 10;
    }
    // ビジネスエラーはリトライしない
    if (err instanceof BusinessError) {
      return false;
    }
    return true;
  }
};
```

### Dead Letter Queue

```yaml
設定:
  maxRetries: 5
  DLQ移動条件:
    - リトライ上限到達
    - 処理不能なエラー
    
DLQ処理:
  - エラー分析
  - 手動介入
  - 修正後の再投入
```

## 📊 バッチ処理

### チャンク処理

```typescript
export class BatchProcessor {
  async processBatch(items: any[], chunkSize: number = 100) {
    const chunks = this.chunkArray(items, chunkSize);
    
    for (const [index, chunk] of chunks.entries()) {
      await this.queue.add(`batch-${index}`, {
        items: chunk,
        chunkIndex: index,
        totalChunks: chunks.length
      });
    }
  }
  
  private chunkArray<T>(array: T[], size: number): T[][] {
    return Array.from(
      { length: Math.ceil(array.length / size) },
      (_, i) => array.slice(i * size, (i + 1) * size)
    );
  }
}
```

## 🔐 セキュリティ

```yaml
認証:
  - ジョブデータに認証情報を含めない
  - ユーザーIDのみを渡す
  - 実行時に権限確認

暗号化:
  - 機密データは暗号化して保存
  - TLS通信の使用
  
監査:
  - ジョブの実行履歴記録
  - 誰が何をいつ実行したか
```

## 🧪 テスト

```typescript
describe('EmailJob', () => {
  it('メール送信ジョブが正しく処理される', async () => {
    const job = await queue.add('send-email', {
      userId: '123',
      templateId: 'welcome'
    });
    
    // ジョブの完了を待つ
    const result = await job.waitUntilFinished(queueEvents);
    
    expect(result).toBeDefined();
    expect(emailService.send).toHaveBeenCalledWith(
      expect.objectContaining({
        userId: '123',
        templateId: 'welcome'
      })
    );
  });
});
```