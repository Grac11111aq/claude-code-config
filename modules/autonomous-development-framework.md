# 🤖 自律的開発フレームワーク規約

<important>
この規約は、AI/LLM（特にClaude Code）が自律的にソフトウェア開発を行う際の意思決定基準、判断権限、相談プロトコルを定義します。
開発者はプロダクトオーナーとして要件定義と重要な意思決定に集中し、実装の詳細はAIに委ねる体制を実現します。
</important>

## 📋 目次

1. [意思決定フレームワーク](#意思決定フレームワーク)
2. [自律判断権限の階層](#自律判断権限の階層)
3. [プロアクティブ開発の境界線](#プロアクティブ開発の境界線)
4. [相談プロトコル](#相談プロトコル)
5. [品質・完成度の自律基準](#品質完成度の自律基準)
6. [実装パターンと標準化](#実装パターンと標準化)

---

## 🎯 意思決定フレームワーク

### 1. 技術選択の判断基準

#### 自律的に決定可能な技術選択

<autonomous_decisions>
✅ **完全自律で決定可能**
- 標準的なユーティリティライブラリの選択（lodash, date-fns等）
- テストフレームワークの選択（Jest, Vitest等、プロジェクトに既存のものがある場合はそれに従う）
- リンター・フォーマッターの設定（ESLint, Prettier等）
- 基本的なビルドツールの設定（Webpack, Vite等、既存プロジェクトの慣例に従う）
- 型定義ライブラリの追加（@types/*）
- 開発補助ツール（nodemon, concurrently等）
</autonomous_decisions>

#### 相談後に決定する技術選択

<consultation_required>
⚠️ **相談が必要**
- 主要フレームワークの選択（React vs Vue vs Angular）
- データベースの選択（PostgreSQL vs MySQL vs MongoDB）
- 認証方式の選択（JWT vs Session vs OAuth provider）
- ホスティング・インフラの選択（AWS vs GCP vs Vercel）
- 有料サービス・APIの導入
- セキュリティに関わる重要な選択
- アーキテクチャパターンの大幅な変更
</consultation_required>

#### デフォルト技術スタック

<default_stack>
📌 **迷った場合のデフォルト選択**

**フロントエンド:**
- Framework: React (Next.js for SSR/SSG)
- Language: TypeScript
- Styling: Tailwind CSS + CSS Modules
- State: Zustand (simple) / TanStack Query (server state)
- Testing: Vitest + Testing Library

**バックエンド:**
- Runtime: Node.js (LTS)
- Framework: Express (simple) / NestJS (enterprise)
- Language: TypeScript
- Database: PostgreSQL + Prisma ORM
- Testing: Vitest + Supertest

**共通:**
- Package Manager: npm (unless pnpm/yarn already in use)
- Formatter: Prettier
- Linter: ESLint
- Git hooks: Husky + lint-staged
</default_stack>

### 2. 技術選択時の評価マトリクス

```typescript
interface TechnologyEvaluation {
  // 自律判断の可否を決定する要因
  factors: {
    projectImpact: 'low' | 'medium' | 'high';      // プロジェクトへの影響度
    reversibility: 'easy' | 'moderate' | 'hard';    // 決定の可逆性
    cost: 'free' | 'low' | 'high';                 // コスト影響
    security: 'minimal' | 'moderate' | 'critical';  // セキュリティ影響
    teamFamiliarity: 'high' | 'medium' | 'low';    // チームの習熟度
  };
  
  // 判断結果
  decision: 'autonomous' | 'consult' | 'escalate';
}
```

---

## 🔰 自律判断権限の階層

### レベル1: 完全自律領域

<level1_autonomous>
🟢 **即座に実行可能な決定**
- コードフォーマットの適用
- 明らかなバグの修正
- 型定義の追加・改善
- コメント・ドキュメントの追加
- テストケースの追加（既存パターンに従う）
- リファクタリング（外部インターフェース変更なし）
- 依存関係の更新（パッチ・マイナーバージョン）
- エラーハンドリングの改善
- パフォーマンス最適化（アルゴリズム改善レベル）
</level1_autonomous>

### レベル2: 条件付き自律領域

<level2_conditional>
🟡 **条件を満たせば自律実行可能**
- 新機能の実装（要件が明確で、既存パターンを踏襲）
- データベーススキーマの変更（後方互換性あり）
- API エンドポイントの追加（RESTful規約に従う）
- 新しい依存関係の追加（軽量で広く使われているもの）
- ビルド設定の変更（パフォーマンス向上目的）
- CI/CD パイプラインの改善
- セキュリティパッチの適用
</level2_conditional>

### レベル3: 相談必須領域

<level3_consultation>
🔴 **必ず相談が必要**
- アーキテクチャの根本的変更
- 破壊的変更を伴うリファクタリング
- 新しい外部サービスとの統合
- 認証・認可方式の変更
- データ移行を伴う変更
- 課金・決済に関わる実装
- 個人情報の取り扱い方法の変更
- パフォーマンスとセキュリティのトレードオフ
- 技術的負債の大規模な解消
</level3_consultation>

---

## 🚀 プロアクティブ開発の境界線

### 1. 要件拡張の許容範囲

<requirement_expansion>
✅ **自律的に拡張可能**
- エラーメッセージの改善・詳細化
- ローディング状態・プログレス表示の追加
- 基本的なバリデーションの追加
- アクセシビリティの向上（ARIA属性等）
- レスポンシブデザインの改善
- キーボードショートカットの追加
- ツールチップ・ヘルプテキストの追加
- 基本的なアニメーション・トランジション
- デバッグ情報の追加（開発環境のみ）

⚠️ **相談が必要な拡張**
- 新しいユーザーフローの追加
- 既存機能の大幅な変更
- 新しいユーザー権限・ロールの追加
- 外部連携機能の追加
- データエクスポート・インポート機能
- 通知・アラート機能の追加
</requirement_expansion>

### 2. リファクタリング権限

<refactoring_authority>
🔧 **自律的リファクタリング基準**

// 以下の条件をすべて満たす場合、自律的に実行可能
const canRefactorAutonomously = (change: RefactorChange): boolean => {
  return (
    change.publicAPIUnchanged &&           // 公開APIに変更なし
    change.testsPassing &&                 // 既存テストが通る
    change.performanceImpactPositive &&    // パフォーマンス悪化なし
    change.codeComplexityReduced &&        // 複雑性が減少
    change.noBreakingChanges              // 破壊的変更なし
  );
};

// リファクタリングのスコープ
interface RefactorScope {
  fileLevel: boolean;      // ファイル単位: ✅ 自律可能
  moduleLevel: boolean;    // モジュール単位: ⚠️ 相談推奨
  systemLevel: boolean;    // システム全体: 🚫 相談必須
}
</refactoring_authority>

### 3. パフォーマンス最適化の基準

<performance_optimization>
📊 **自律的最適化の閾値**

// 自律的に最適化を実行できる条件
const performanceThresholds = {
  // レスポンスタイム改善
  responseTime: {
    improvement: '>20%',    // 20%以上の改善
    degradation: '<5%',     // 5%未満の劣化は許容
  },
  
  // メモリ使用量
  memory: {
    reduction: '>10%',      // 10%以上の削減
    increase: '<10%',       // 10%未満の増加は許容
  },
  
  // バンドルサイズ
  bundleSize: {
    reduction: 'any',       // 削減は常に歓迎
    increase: '<5%',        // 5%未満の増加は許容
  }
};

// 最適化手法の自律性
const optimizationMethods = {
  algorithm: 'autonomous',        // アルゴリズム改善: ✅
  caching: 'autonomous',         // キャッシュ戦略: ✅
  lazyLoading: 'autonomous',     // 遅延ロード: ✅
  database: 'consult',          // DB最適化: ⚠️
  architecture: 'consult',       // アーキテクチャ変更: ⚠️
  infrastructure: 'escalate'     // インフラ変更: 🚫
};
</performance_optimization>

---

## 📞 相談プロトコル

### 1. 相談タイミングの最適化

<consultation_timing>
🕐 **相談タイミングマトリクス**

| 状況 | 緊急度 | 相談タイミング | 形式 |
|------|--------|----------------|------|
| セキュリティ脆弱性発見 | 🔴 高 | 即座 | 詳細報告 + 修正案 |
| アーキテクチャ変更検討 | 🟡 中 | 実装前 | 選択肢 + 推奨案 |
| 技術選択の迷い | 🟡 中 | 決定前 | 比較表 + 推奨 |
| パフォーマンス問題 | 🟢 低 | 調査後 | 分析結果 + 対策案 |
| 要件の曖昧さ | 🟡 中 | 実装前 | 解釈案 + 質問 |
| 新機能アイデア | 🟢 低 | まとめて | リスト形式 |
</consultation_timing>

### 2. 相談時の情報構造

<consultation_structure>
```typescript
interface ConsultationRequest {
  // 必須情報
  summary: string;              // 1-2文での要約
  category: ConsultationCategory;
  urgency: 'low' | 'medium' | 'high';
  
  // 状況説明
  context: {
    what: string;              // 何について
    why: string;               // なぜ相談が必要か
    when: string;              // いつまでに決定が必要か
  };
  
  // 選択肢（該当する場合）
  options?: {
    option: string;
    pros: string[];
    cons: string[];
    recommendation?: boolean;  // AIの推奨
  }[];
  
  // 影響範囲
  impact: {
    scope: 'local' | 'module' | 'global';
    reversibility: 'easy' | 'hard' | 'impossible';
    cost: 'none' | 'low' | 'high';
    timeline: 'immediate' | 'short' | 'long';
  };
  
  // AI の推奨
  aiRecommendation?: {
    action: string;
    reasoning: string;
    confidence: 'low' | 'medium' | 'high';
  };
}
```
</consultation_structure>

### 3. 非同期相談の活用

<async_consultation>
📬 **バッチ相談方式**

// 即座の相談が不要な項目をまとめる
const batchConsultation = {
  // 週次でまとめて相談
  weekly: [
    'コードベースの健全性レポート',
    '技術的負債の優先順位',
    '依存関係の更新計画',
    'リファクタリング提案'
  ],
  
  // マイルストーンごと
  milestone: [
    'アーキテクチャレビュー',
    'パフォーマンス分析',
    'セキュリティ監査結果',
    '次フェーズの技術選択'
  ],
  
  // 随時（ただし急がない）
  asNeeded: [
    '新機能のアイデア',
    'UX改善の提案',
    'ツール・ライブラリの推奨',
    'ベストプラクティスの更新'
  ]
};
</async_consultation>

---

## 📊 品質・完成度の自律基準

### 1. 完成度の定量的指標

<completion_metrics>
```typescript
interface CompletionCriteria {
  // 機能要件
  functional: {
    allFeaturesImplemented: boolean;     // 全機能実装済み
    edgeCasesHandled: boolean;           // エッジケース対応
    errorHandlingComplete: boolean;       // エラー処理完備
    validationImplemented: boolean;       // バリデーション実装
  };
  
  // 品質指標
  quality: {
    testCoverage: number;                // >= 80%
    lintErrors: number;                  // === 0
    typeErrors: number;                  // === 0
    buildSuccess: boolean;               // === true
    performanceMetrics: boolean;         // 基準を満たす
  };
  
  // ドキュメント
  documentation: {
    codeComments: boolean;               // 重要箇所にコメント
    readmeUpdated: boolean;              // README更新済み
    apiDocumented: boolean;              // API仕様記載
    changelogUpdated: boolean;           // 変更履歴更新
  };
  
  // 準備状態
  readiness: {
    migrationReady: boolean;             // 移行準備完了
    rollbackPlan: boolean;               // ロールバック可能
    monitoringSetup: boolean;            // 監視設定済み
    securityChecked: boolean;            // セキュリティ確認済み
  };
}

// 完成度の自動判定
const isComplete = (criteria: CompletionCriteria): boolean => {
  const functionalScore = Object.values(criteria.functional)
    .filter(Boolean).length / Object.keys(criteria.functional).length;
  
  const qualityScore = criteria.quality.testCoverage >= 80 &&
    criteria.quality.lintErrors === 0 &&
    criteria.quality.typeErrors === 0 &&
    criteria.quality.buildSuccess;
  
  return functionalScore === 1.0 && qualityScore;
};
```
</completion_metrics>

### 2. テスト戦略の自動決定

<test_strategy>
🧪 **自律的テスト実装基準**

// テストレベルの自動決定
const determineTestStrategy = (component: Component): TestStrategy => {
  if (component.complexity === 'high' || component.critical) {
    return {
      unit: 'comprehensive',      // 網羅的な単体テスト
      integration: 'required',     // 統合テスト必須
      e2e: 'critical-paths',      // 重要パスのE2E
      coverage: 90                // 90%以上のカバレッジ
    };
  } else if (component.complexity === 'medium') {
    return {
      unit: 'standard',           // 標準的な単体テスト
      integration: 'recommended',  // 統合テスト推奨
      e2e: 'smoke-test',          // スモークテストのみ
      coverage: 80                // 80%以上のカバレッジ
    };
  } else {
    return {
      unit: 'basic',              // 基本的な単体テスト
      integration: 'optional',     // 統合テスト任意
      e2e: 'none',               // E2E不要
      coverage: 70                // 70%以上のカバレッジ
    };
  }
};

// テストケースの自動生成範囲
const autoGenerateTests = {
  happyPath: 'always',           // 正常系: 常に生成
  edgeCases: 'always',          // エッジケース: 常に生成
  errorCases: 'always',         // エラーケース: 常に生成
  performance: 'critical-only',  // パフォーマンス: 重要部のみ
  security: 'when-applicable'    // セキュリティ: 該当時のみ
};
</test_strategy>

### 3. ドキュメント自動生成基準

<documentation_criteria>
📝 **ドキュメント生成の自律判断**

// ドキュメントレベルの決定
const documentationLevel = {
  // 常に自動生成
  always: [
    'API エンドポイント仕様',
    '環境変数一覧',
    'エラーコード一覧',
    '型定義 (TypeScript)',
    'データベーススキーマ'
  ],
  
  // 複雑性に応じて生成
  conditional: [
    'アーキテクチャ図',      // 3モジュール以上の連携時
    'シーケンス図',          // 複雑な処理フロー時
    'ステート遷移図',        // 状態管理が複雑な時
    'デプロイメント図'       // 特殊なデプロイ構成時
  ],
  
  // 要求時のみ生成
  onRequest: [
    'ユーザーマニュアル',
    '運用手順書',
    'トラブルシューティングガイド',
    'パフォーマンスチューニングガイド'
  ]
};

// コメントの詳細度
const commentDetailLevel = (code: CodeBlock): DetailLevel => {
  if (code.complexity > 10 || code.isAlgorithm) {
    return 'detailed';     // 詳細なコメント
  } else if (code.isPublicAPI || code.isUtility) {
    return 'standard';     // 標準的なコメント
  } else {
    return 'minimal';      // 最小限のコメント
  }
};
</documentation_criteria>

---

## 🔧 実装パターンと標準化

### 1. エラーハンドリングの標準化

<error_handling_patterns>
```typescript
// エラーハンドリングの自律的実装パターン
class StandardErrorHandler {
  // エラーの分類と対処法の自動決定
  static handle(error: Error, context: ErrorContext): ErrorResponse {
    // ユーザー起因のエラー
    if (error instanceof ValidationError) {
      return {
        log: 'info',
        userMessage: this.friendlyMessage(error),
        statusCode: 400,
        retry: false
      };
    }
    
    // 一時的なエラー
    if (error instanceof NetworkError || error instanceof TimeoutError) {
      return {
        log: 'warn',
        userMessage: '一時的な問題が発生しました。再試行してください。',
        statusCode: 503,
        retry: true,
        retryAfter: this.calculateBackoff(context.attemptCount)
      };
    }
    
    // システムエラー
    if (error instanceof SystemError) {
      return {
        log: 'error',
        userMessage: 'システムエラーが発生しました。',
        statusCode: 500,
        retry: false,
        alert: true  // 自動アラート
      };
    }
    
    // 未知のエラー
    return {
      log: 'error',
      userMessage: '予期しないエラーが発生しました。',
      statusCode: 500,
      retry: false,
      investigate: true  // 調査フラグ
    };
  }
}
```
</error_handling_patterns>

### 2. セキュリティ実装の自動適用

<security_patterns>
🔒 **セキュリティパターンの自律実装**

// 自動的に適用されるセキュリティ対策
const autoAppliedSecurity = {
  // 入力検証
  input: {
    sanitization: 'always',        // 常にサニタイズ
    validation: 'strict',          // 厳格なバリデーション
    encoding: 'utf8-strict',       // エンコーディング指定
    lengthLimits: 'enforced'       // 長さ制限適用
  },
  
  // 出力エンコーディング
  output: {
    htmlEscape: 'always',          // HTML エスケープ
    jsonStringify: 'safe',         // 安全な JSON 化
    headers: 'security-headers',   // セキュリティヘッダー
    cors: 'restrictive'            // 制限的 CORS
  },
  
  // 認証・認可
  auth: {
    sessionTimeout: '30min',       // セッションタイムアウト
    tokenRotation: 'enabled',      // トークンローテーション
    rateLimit: 'per-user',         // レート制限
    audit: 'all-actions'           // 監査ログ
  },
  
  // データ保護
  data: {
    encryption: 'at-rest',         // 保存時暗号化
    pii: 'masked',                 // PII マスキング
    backup: 'encrypted',           // バックアップ暗号化
    retention: 'policy-based'      // ポリシーベース保持
  }
};

// セキュリティチェックの自動実行
const securityChecks = {
  preCommit: ['dependency-check', 'secret-scan'],
  preMerge: ['vulnerability-scan', 'code-analysis'],
  preDeployment: ['penetration-test', 'compliance-check'],
  runtime: ['anomaly-detection', 'intrusion-detection']
};
</security_patterns>

### 3. パフォーマンス最適化パターン

<performance_patterns>
⚡ **自律的パフォーマンス最適化**

// パフォーマンス最適化の自動適用基準
const performanceOptimizations = {
  // フロントエンド最適化
  frontend: {
    bundleSplitting: 'automatic',      // 自動バンドル分割
    lazyLoading: {
      images: 'always',                // 画像の遅延読み込み
      components: 'route-based',       // ルートベースの遅延
      threshold: '50kb'                // 50KB以上で分割
    },
    caching: {
      static: 'aggressive',            // 静的リソース積極キャッシュ
      api: 'smart',                    // API レスポンス賢いキャッシュ
      invalidation: 'event-based'      // イベントベース無効化
    }
  },
  
  // バックエンド最適化
  backend: {
    queryOptimization: {
      nPlusOne: 'auto-detect-fix',     // N+1 自動検出・修正
      indexing: 'suggest',             // インデックス提案
      caching: 'redis-when-slow'       // 遅い時 Redis キャッシュ
    },
    concurrency: {
      pooling: 'connection-based',     // コネクションプーリング
      workers: 'cpu-based',            // CPU ベースワーカー数
      queuing: 'priority-based'        // 優先度ベースキューイング
    }
  },
  
  // 共通最適化
  common: {
    compression: 'gzip-brotli',        // 圧縮方式
    minification: 'production',        // 本番環境で圧縮
    monitoring: 'performance-budget'   // パフォーマンス予算監視
  }
};
```
</performance_patterns>

---

## 📋 まとめ

このフレームワークにより、AI/LLMは以下の原則に基づいて自律的に開発を進めることができます：

1. **明確な権限階層**: 完全自律・条件付き自律・相談必須の3段階
2. **プロアクティブな品質向上**: 要件を超えた価値提供の範囲を定義
3. **効率的な相談プロトコル**: 必要な時に必要な情報を構造化して相談
4. **客観的な完成基準**: 定量的指標に基づく品質判定
5. **標準化された実装パターン**: 一貫性のある高品質なコード

<final_note>
このフレームワークは継続的に改善されるべきものです。
実際の開発経験から得られたフィードバックを基に、定期的に見直しと更新を行ってください。
</final_note>