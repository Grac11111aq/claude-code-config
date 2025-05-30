# 🏃 アジャイル開発プラクティス規約

<module_info>
このモジュールは、アジャイル開発の実践規約を定義します。
柔軟で継続的な価値提供により、変化に対応できる開発を実現します。
</module_info>

## 📐 基本原則

### アジャイルマニフェスト準拠

```yaml
価値観:
  - 個人と対話 > プロセスとツール
  - 動くソフトウェア > 包括的ドキュメント
  - 顧客との協調 > 契約交渉
  - 変化への対応 > 計画に従うこと

実践的解釈:
  - ツールは手段、対話が目的
  - 必要十分なドキュメント
  - フィードバック駆動開発
  - 適応的計画
```

### 規模別適用戦略

```yaml
1人開発（個人プロジェクト）:
  手法: カンバン + Personal Scrum
  期間: 1週間イテレーション
  重点: 継続的デリバリー、学習

小チーム（2-5人）:
  手法: スクラム
  期間: 2週間スプリント
  重点: チーム協調、品質

大チーム（6人以上）:
  手法: スケールドアジャイル（SAFe/LeSS）
  期間: 3週間スプリント
  重点: 調整、統合
```

## 🎯 スクラム実装（チーム開発）

### ロールと責務

```yaml
Product Owner（プロダクトオーナー）:
  責務:
    - プロダクトバックログ管理
    - ビジネス価値の最大化
    - ステークホルダーとの調整
  
  1人開発時の代替:
    - ユーザーペルソナ設定
    - 価値仮説の検証
    - 優先順位付け

Scrum Master（スクラムマスター）:
  責務:
    - プロセス改善の促進
    - 障害の除去
    - チーム支援
  
  1人開発時の代替:
    - セルフマネジメント
    - 定期的な振り返り
    - 継続的改善

Development Team（開発チーム）:
  責務:
    - プロダクトインクリメント作成
    - 品質保証
    - 自己組織化
```

### スプリント実装

```yaml
スプリントプランニング:
  時間: 2-4時間（2週間スプリント）
  成果物:
    - スプリントゴール
    - スプリントバックログ
    - 作業見積もり
  
  1人開発での簡素化:
    - 30分の計画セッション
    - 今週の目標設定
    - タスクの優先順位付け

デイリースタンドアップ:
  時間: 15分
  内容:
    - 昨日やったこと
    - 今日やること
    - 障害・課題
  
  1人開発での代替:
    - 朝の5分振り返り
    - 進捗の記録
    - 当日の計画調整

スプリントレビュー:
  時間: 1-2時間
  参加者: ステークホルダー
  内容:
    - デモンストレーション
    - フィードバック収集
    - 次スプリントの調整

スプリントレトロスペクティブ:
  時間: 30-60分
  フォーマット:
    - Keep（継続すること）
    - Problem（問題点）
    - Try（試すこと）
```

## 📋 カンバン実装（1人開発推奨）

### ボード設計

```yaml
基本構成:
  Backlog → To Do → In Progress → Review → Done

高度構成:
  Ideas → Backlog → Ready → In Progress → Review → Testing → Done

カラム定義:
  Ideas: アイデア・要望の収集
  Backlog: 優先順位付け済みタスク
  Ready: 着手準備完了
  In Progress: 開発中（WIP制限: 2）
  Review: レビュー待ち（WIP制限: 1）
  Testing: テスト中
  Done: 完了（1週間で削除）
```

### WIP制限

```yaml
制限設定:
  In Progress: 2タスク（集中力維持）
  Review: 1タスク（ボトルネック防止）
  
効果:
  - マルチタスク防止
  - フロー改善
  - 品質向上
  
違反時対応:
  - 既存タスク完了優先
  - 新規タスク着手停止
  - 原因分析・改善
```

### メトリクス計測

```typescript
// カンバンメトリクス実装例
interface TaskMetrics {
  leadTime: number;      // アイデアから完了まで
  cycleTime: number;     // 着手から完了まで
  throughput: number;    // 一定期間の完了タスク数
  wip: number;          // 仕掛中タスク数
}

class KanbanMetrics {
  calculateLeadTime(task: Task): number {
    return task.completedAt.getTime() - task.createdAt.getTime();
  }
  
  calculateCycleTime(task: Task): number {
    return task.completedAt.getTime() - task.startedAt.getTime();
  }
  
  weeklyThroughput(tasks: Task[]): number {
    const lastWeek = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000);
    return tasks.filter(t => t.completedAt > lastWeek).length;
  }
}
```

## 📝 ユーザーストーリー作成

### INVEST原則

```yaml
Independent（独立性）:
  - 他のストーリーに依存しない
  - 任意の順序で開発可能
  - 明確な境界

Negotiable（交渉可能）:
  - 詳細は対話で決定
  - 要件の調整可能
  - 柔軟な解釈

Valuable（価値）:
  - ユーザーに価値を提供
  - ビジネス目標に貢献
  - 測定可能な成果

Estimable（見積可能）:
  - 作業量が見積もれる
  - 明確で理解しやすい
  - 複雑すぎない

Small（小さい）:
  - 1スプリント内で完了
  - 2-3日の作業量
  - 分割可能

Testable（テスト可能）:
  - 受け入れ条件が明確
  - 完了判定が可能
  - 検証方法が定義済み
```

### ストーリー記述形式

```yaml
基本形式:
  "As a [ユーザー], I want [機能] so that [価値]"

具体例:
  "ユーザーとして、注文履歴を確認したい。なぜなら過去の購入を把握できるから。"

受け入れ条件（Given-When-Then）:
  Given: 前提条件
  When: 操作・イベント
  Then: 期待結果

例:
  Given: ログイン済みユーザー
  When: マイページで「注文履歴」をクリック
  Then: 過去30日分の注文一覧が表示される
```

## 📊 見積もり・計画

### ストーリーポイント

```yaml
フィボナッチ数列使用: 1, 2, 3, 5, 8, 13, 21
  1: 非常に簡単（30分-1時間）
  2: 簡単（2-4時間）
  3: 普通（1日）
  5: やや複雑（2-3日）
  8: 複雑（1週間）
  13: 非常に複雑（分割検討）
  21: 分割必須

相対見積もり:
  - 過去のタスクと比較
  - チーム全体で合意
  - 学習効果を考慮
```

### プランニングポーカー

```yaml
手順:
  1. ストーリー説明
  2. 質疑応答
  3. 個別見積もり
  4. 同時公開
  5. 議論・調整
  6. 合意形成

1人開発での代替:
  - 過去データとの比較
  - 3点見積法（楽観・悲観・最頻値）
  - バッファ込みの計画
```

## 🔄 継続的改善

### レトロスペクティブ手法

```yaml
Start-Stop-Continue:
  Start: 新しく始めること
  Stop: やめること
  Continue: 続けること

Mad-Sad-Glad:
  Mad: 怒り・フラストレーション
  Sad: 悲しみ・失望
  Glad: 喜び・満足

4Ls:
  Liked: 良かったこと
  Learned: 学んだこと
  Lacked: 不足していたこと
  Longed for: 望んでいたこと
```

### 改善アクション管理

```typescript
interface ImprovementAction {
  id: string;
  description: string;
  owner: string;
  dueDate: Date;
  status: 'planned' | 'in_progress' | 'completed' | 'cancelled';
  impact: 'high' | 'medium' | 'low';
}

class RetroManager {
  private actions: ImprovementAction[] = [];
  
  addAction(description: string, impact: string): void {
    const action: ImprovementAction = {
      id: generateId(),
      description,
      owner: 'team',
      dueDate: new Date(Date.now() + 14 * 24 * 60 * 60 * 1000), // 2週間後
      status: 'planned',
      impact: impact as any
    };
    
    this.actions.push(action);
  }
  
  reviewActions(): ImprovementAction[] {
    return this.actions.filter(a => a.status !== 'completed');
  }
}
```

## 📈 ベロシティ管理

### 計測・予測

```yaml
ベロシティ計算:
  - スプリントごとの完了ストーリーポイント
  - 過去3-5スプリントの平均
  - トレンド分析

予測精度向上:
  - チーム安定性の考慮
  - 学習曲線の考慮
  - 外部要因の調整
  
1人開発での簡素化:
  - 週単位での計測
  - 時間ベースの記録
  - 生産性パターンの把握
```

## 🎭 アジャイルコーチング

### セルフコーチング（1人開発）

```yaml
日次:
  - 朝の計画（5分）
  - 夕の振り返り（5分）
  - 進捗の記録

週次:
  - 週目標の設定
  - 成果の評価
  - 改善点の特定

月次:
  - プロセスの見直し
  - ツールの評価
  - スキル向上計画
```

### チームコーチング

```yaml
観察ポイント:
  - チーム内コミュニケーション
  - 意思決定プロセス
  - 学習・改善姿勢

介入タイミング:
  - パフォーマンス低下時
  - 合意形成困難時
  - モチベーション低下時

支援方法:
  - ファシリテーション
  - トレーニング
  - メンタリング
```