# 🤖 LLM活用ワークフロー・プロンプト戦略規約

<module_info>
このモジュールは、複数のLLMを戦略的に活用するワークフローを定義します。
適切なLLM選択、プロンプト設計、結果統合により、高品質な意思決定を支援します。
</module_info>

## 📐 基本方針

### LLM活用の原則

```yaml
補完的活用:
  - Claude Code: 実装・コード生成
  - 他LLM: 調査・分析・セカンドオピニオン
  - 人間: 最終判断・戦略決定

品質保証:
  - 複数LLMでのクロスチェック
  - 構造化されたプロンプト設計
  - 結果の検証・統合プロセス
```

### LLM選択マトリクス

```yaml
Claude (Sonnet/Opus):
  得意分野:
    - コード生成・レビュー
    - 技術文書作成
    - 論理的推論
    - 日本語処理
  
  適用場面:
    - アーキテクチャ設計
    - コードリファクタリング
    - 技術選定の検討

GPT-4 (OpenAI):
  得意分野:
    - 創造的思考
    - ビジネス戦略
    - マーケティング分析
    - 幅広い知識統合
  
  適用場面:
    - プロダクト企画
    - 競合分析
    - ユーザー調査設計

Gemini (Google):
  得意分野:
    - 情報検索・統合
    - データ分析
    - 最新情報の活用
    - 多言語対応
  
  適用場面:
    - 市場調査
    - トレンド分析
    - 国際化検討

専門AI:
  Perplexity: リアルタイム調査
  GitHub Copilot: コード補完
  Claude Code: 統合開発支援
```

## 🎯 タスク別LLM活用戦略

### 技術調査・選定

```yaml
Step 1: 初期調査 (Perplexity + Gemini)
目的: 最新情報・トレンドの把握
プロンプト例:
  "2024年のフロントエンドフレームワーク選択において、
   React、Vue、Svelteの現在の採用動向、
   パフォーマンス比較、エコシステムの状況を調査してください。
   特に企業導入事例と学習コストの観点を重視してください。"

Step 2: 技術分析 (Claude)
目的: 技術的詳細の分析
プロンプト例:
  "以下の調査結果を基に、
   1人開発者が新規プロジェクトで選択すべき
   フロントエンドフレームワークを
   保守性、開発効率、将来性の観点から分析し、
   具体的な選択基準を提示してください。"

Step 3: ビジネス影響分析 (GPT-4)
目的: ビジネス観点での評価
プロンプト例:
  "技術選択がプロダクトの成功に与える影響を、
   開発コスト、リクルーティング、市場投入速度、
   長期保守性の観点から分析してください。"
```

### プロダクト企画・戦略

```yaml
Step 1: 市場分析 (Gemini + Perplexity)
プロンプト:
  "以下の市場セグメントにおける：
   - 現在の市場規模と成長率
   - 主要プレイヤーと競合状況
   - 未充足ニーズの特定
   - 技術トレンドの影響
   を調査し、データソース付きで報告してください。"

Step 2: 戦略立案 (GPT-4)
プロンプト:
  "市場分析結果を基に、1人開発者が参入可能な
   ニッチ市場を特定し、以下を含む戦略を提案してください：
   - ターゲットユーザーペルソナ
   - 最小実行可能製品（MVP）の定義
   - 差別化戦略
   - Go-to-Market戦略"

Step 3: 技術実現性検証 (Claude)
プロンプト:
  "提案された戦略の技術実現性を評価し、
   必要な技術スタック、開発期間、
   技術的リスクを分析してください。
   既存の技術規約との整合性も考慮してください。"
```

### 問題解決・トラブルシューティング

```yaml
Step 1: 問題の構造化 (Claude)
プロンプト:
  "以下の問題を5W1Hフレームワークで構造化し、
   根本原因の仮説を3つ提示してください：
   [問題の詳細]"

Step 2: 解決策のブレインストーミング (GPT-4)
プロンプト:
  "構造化された問題に対して、
   - 短期的な対処法（1週間以内）
   - 中期的な改善策（1-3ヶ月）
   - 長期的な根本解決（3ヶ月以上）
   の観点から、創造的な解決策を10個提案してください。"

Step 3: 実装プラン作成 (Claude)
プロンプト:
  "提案された解決策を技術的実現性、
   コスト、リスクで評価し、
   優先順位付きの実装プランを作成してください。"
```

## 📝 プロンプト設計パターン

### 構造化プロンプトテンプレート

```yaml
基本構造:
  1. コンテキスト設定
  2. タスクの明確化
  3. 制約条件
  4. 出力形式指定
  5. 品質基準

例:
  """
  # コンテキスト
  あなたは経験豊富な[専門分野]の専門家です。
  
  # タスク
  以下について分析・推奨を行ってください：
  [具体的なタスク内容]
  
  # 制約条件
  - 予算: [制約]
  - 期間: [制約]
  - 技術スタック: [制約]
  
  # 出力形式
  1. 要約（3行以内）
  2. 詳細分析
  3. 推奨事項（優先順位付き）
  4. リスク評価
  5. 次のステップ
  
  # 品質基準
  - 具体的で実行可能な内容
  - データ・根拠の明示
  - 複数の選択肢を提示
  """
```

### チェーン・オブ・ソート（CoT）活用

```yaml
段階的推論プロンプト:
  "この問題を以下のステップで段階的に分析してください：
   
   Step 1: 現状分析
   - 何が起きているか
   - なぜ問題なのか
   
   Step 2: 原因分析
   - 直接的原因
   - 根本的原因
   
   Step 3: 影響分析
   - 短期的影響
   - 長期的影響
   
   Step 4: 解決策検討
   - 複数の選択肢
   - 各選択肢の評価
   
   Step 5: 推奨案
   - 最適解の選択理由
   - 実装計画"
```

### ロールプレイング活用

```yaml
専門家ペルソナ:
  "以下の3つの視点から分析してください：
   
   視点1: CTOとして
   - 技術的実現性
   - 開発コスト
   - 保守性
   
   視点2: プロダクトマネージャーとして
   - ユーザー価値
   - 市場適合性
   - 競合優位性
   
   視点3: ビジネスオーナーとして
   - ROI・収益性
   - リスク評価
   - 成長可能性"
```

## 🔄 マルチLLMワークフロー実装

### 調査・分析パイプライン

```typescript
interface LLMRequest {
  model: 'claude' | 'gpt4' | 'gemini' | 'perplexity';
  prompt: string;
  context?: any;
  temperature?: number;
}

interface LLMResponse {
  model: string;
  response: string;
  confidence: number;
  sources?: string[];
}

class MultiLLMWorkflow {
  async researchPipeline(topic: string): Promise<ResearchResult> {
    // Step 1: 並行調査
    const investigations = await Promise.all([
      this.query({
        model: 'perplexity',
        prompt: `${topic}の最新動向を調査。データソース付きで報告`,
        temperature: 0.3
      }),
      this.query({
        model: 'gemini',
        prompt: `${topic}に関する包括的な市場分析`,
        temperature: 0.5
      })
    ]);
    
    // Step 2: 統合分析
    const analysis = await this.query({
      model: 'claude',
      prompt: `以下の調査結果を統合分析:
        ${investigations.map(r => r.response).join('\n---\n')}`,
      temperature: 0.2
    });
    
    // Step 3: 戦略提案
    const strategy = await this.query({
      model: 'gpt4',
      prompt: `分析結果を基に実行可能な戦略を提案:
        ${analysis.response}`,
      temperature: 0.7
    });
    
    return {
      investigations,
      analysis,
      strategy,
      confidence: this.calculateConfidence([analysis, strategy])
    };
  }
  
  async technicalDecision(requirement: string): Promise<TechnicalDecision> {
    // 技術的実現性をClaude、ビジネス影響をGPT-4で分析
    const [technical, business] = await Promise.all([
      this.query({
        model: 'claude',
        prompt: `技術要件の実現可能性分析: ${requirement}`
      }),
      this.query({
        model: 'gpt4',
        prompt: `ビジネス影響とROI分析: ${requirement}`
      })
    ]);
    
    // 統合判断
    const decision = await this.query({
      model: 'claude',
      prompt: `技術・ビジネス両面から最終判断:
        技術分析: ${technical.response}
        ビジネス分析: ${business.response}`
    });
    
    return { technical, business, decision };
  }
}
```

### セカンドオピニオンシステム

```typescript
class SecondOpinionSystem {
  async validateDecision(
    primaryDecision: string,
    context: string
  ): Promise<ValidationResult> {
    
    // 複数モデルでの検証
    const validations = await Promise.all([
      this.query({
        model: 'gpt4',
        prompt: `以下の判断について妥当性を検証:
          判断: ${primaryDecision}
          文脈: ${context}
          
          考慮すべき代替案や見落としているリスクはありますか？`
      }),
      this.query({
        model: 'gemini',
        prompt: `異なる視点からの検証:
          ${primaryDecision}
          
          この判断の弱点や改善点を指摘してください。`
      })
    ]);
    
    // 合意度の計算
    const consensus = this.calculateConsensus(validations);
    
    return {
      originalDecision: primaryDecision,
      validations,
      consensus,
      recommendation: consensus > 0.7 ? 'proceed' : 'reconsider'
    };
  }
}
```

## 📊 品質管理・評価

### LLM回答の信頼性評価

```yaml
評価基準:
  一貫性: 複数回実行での結果の安定性
  論理性: 推論過程の妥当性
  具体性: 実行可能な詳細度
  正確性: 事実確認可能な情報の正確さ
  
評価プロセス:
  1. 複数LLMでの並行実行
  2. 結果の比較・差異分析
  3. 外部情報源での事実確認
  4. 信頼度スコアの算出
```

### 継続的改善

```yaml
プロンプト最適化:
  - A/Bテストによる効果測定
  - 成功パターンの蓄積
  - 失敗事例の分析・改善

ワークフロー改善:
  - 実行時間の最適化
  - コストパフォーマンス分析
  - 新しいLLMサービスの評価
```

## 🔒 セキュリティ・コンプライアンス

```yaml
情報管理:
  機密情報の除外: プロンプトに含めない
  データ匿名化: 個人情報の除去
  アクセス制御: APIキーの適切な管理

監査:
  実行ログの保持: 意思決定の追跡可能性
  結果の保存: 学習・改善のためのデータ蓄積
  コスト監視: API使用量・コストの追跡
```

## 📋 実践チェックリスト

```yaml
事前準備:
  - [ ] 適切なLLM選択
  - [ ] プロンプト設計・レビュー
  - [ ] 期待する出力形式の定義

実行中:
  - [ ] 複数LLMでの並行実行
  - [ ] 中間結果の検証
  - [ ] 必要に応じた軌道修正

事後評価:
  - [ ] 結果の統合・分析
  - [ ] 信頼性の評価
  - [ ] 学習・改善点の記録
```