# Claude Code グローバル開発方針

<your_role>
あなたは、日本語を深く理解し、高品質なコードとドキュメントを生成する経験豊富なソフトウェア開発アシスタントです。
以下の開発DNA・基本方針を、すべてのプロジェクトにおいて厳守してください。
これらは私が多くの開発において要求する必須要件です。
</your_role>

## 📚 ドキュメント構成

この開発方針は、管理しやすさと参照の容易さのため、以下のモジュールに分割されています：

### 🏗️ [基本原則と開発姿勢](modules/core-principles.md)
- 開発体制と基本原則
- 利用環境・前提条件
- アーキテクチャとデータ処理
- 依存関係管理
- 国際化戦略
- ビルド・デプロイメント戦略
- ドキュメンテーション標準
- バージョン管理とGit運用

### 📋 [コーディング標準と品質管理](modules/coding-standards.md)
- 基本原則と命名規則
- エラーハンドリングとロギング
- テスト戦略
- CI/CD & 品質ゲートの絶対基準

### 🤖 [LLM主導開発の技術選定原則](modules/llm-development.md)
- 人間によるレビューの強化
- 厳格な品質保証の組み込み
- 長期的な健全性の確保
- プログラミング言語選定の指針
- フレームワーク・ライブラリ選定の指針
- 品質保証とレビューツールの必須化
- 意思決定の優先順位

### 🎨 [UI/UX設計とアクセシビリティ](modules/ui-ux-guidelines.md)
- 基本方針
- アクセシビリティ基準（WCAG 2.1 Level AA準拠）
- 非機能要件の優先順位

### ⚡ [パフォーマンス目標](modules/performance-targets.md)
- レスポンス指標
- スムーズさ指標
- リソース効率
- 信頼性指標
- ビジネス成果（参考指標）

### ⚙️ [Claude 4 & Max プラン固有設定](modules/claude-model-settings.md)
- モデル選択ポリシー（Sonnet 4 vs Opus 4）
- Extended-Thinking モード利用指針
- プロジェクト・コンテキスト管理
- 緊急停止・制御手順

### 🛡️ [権限・セキュリティ設定](modules/security-permissions.md)
- 権限管理の基本方針
- ファイルシステム保護
- ネットワーク・外部アクセス制御
- 開発環境のセキュリティ
- 監査・ログ管理

### 🔄 [自動PRワークフローの安全装備](modules/workflow-safety.md)
- 自動マージ防止設定
- 緊急停止メカニズム
- PR作成時の安全チェック
- 監視・アラート設定
- チーム・組織での運用

### 🔌 [MCP/Integrations のセキュア運用](modules/mcp-integrations.md)
- 基本セキュリティ方針
- 認証情報管理
- 統合別セキュリティ設定
- 監視・アラート設定
- インシデント対応

### 🗄️ [データベース設計規約](modules/database-guidelines.md)
- 基本規約（PostgreSQLデフォルト）
- 設計規約（命名規則、必須カラム）
- マイグレーション規約
- パフォーマンス規約

### 🌐 [API設計規約](modules/api-design-guidelines.md)
- 基本規約（RESTデフォルト）
- エンドポイント設計
- レスポンス形式
- ページネーション・認証

### 💾 [キャッシング規約](modules/caching-strategy.md)
- 基本規約（レイヤー構成）
- TTL規約
- キャッシュ無効化戦略
- 実装パターン

### ⚙️ [環境・設定管理規約](modules/env-config-management.md)
- 基本規約（環境定義、優先順位）
- シークレット管理
- 命名規約とファイル構成
- 型安全性とバリデーション

### 📊 [ログ・モニタリング規約](modules/logging-monitoring.md)
- 基本規約（ログレベル、形式）
- 構造化ログ
- メトリクスとアラート
- ログ保持期間

### 🍨 [フロントエンドアーキテクチャ規約](modules/frontend-architecture.md)
- 基本規約（Reactデフォルト）
- 状態管理とスタイリング
- コンポーネント設計
- パフォーマンス最適化

### 🔧 [マイクロサービス・アーキテクチャ規約](modules/microservices-architecture.md)
- アーキテクチャ進化戦略（モノリスから段階的に）
- クリーンアーキテクチャ実装
- イベント駆動設計
- オーケストレーション

### 🏛️ [クリーンアーキテクチャ実装規約](modules/clean-architecture.md)
- 基本原則（依存性のルール）
- レイヤー詳細（Domain/Application/Infrastructure）
- ディレクトリ構造とデータフロー
- 実装パターンとベストプラクティス

### 😨 [エラー設計・例外処理規約](modules/error-exception-design.md)
- エラー分類とコード体系
- リトライ戦略とサーキットブレーカー
- ドメインエラーの実装
- 分散システムでのエラー伝播

### 🔐 [認証・認可アーキテクチャ規約](modules/auth-architecture.md)
- JWTベース認証
- RBACによる認可設計
- マイクロサービス間認証
- セキュリティ考慮事項

### 🧪 [詳細テスト戦略規約](modules/test-strategy-detailed.md)
- テストピラミッドとカバレッジ目標
- 各テストレベルの実装方法
- Contract Testingとマイクロサービステスト
- テストデータ管理

### 💰 [トランザクション管理規約](modules/transaction-management.md)
- ローカルトランザクション
- 分散トランザクション（Sagaパターン）
- 楽観的ロックとイベントソーシング
- デッドロック対策

### ⚡ [非同期処理・ジョブ管理規約](modules/async-job-management.md)
- ジョブの分類とキュー選択
- 冪等性とジョブ設計原則
- スケジュールジョブと進捗管理
- エラーハンドリングとDead Letter Queue

### 🔍 [可観測性設計規約](modules/observability-design.md)
- 3つの柱（メトリクス・ログ・トレース）
- Prometheus/Grafana実装
- OpenTelemetryと分散トレーシング
- SLI/SLO定義とアラート設計

### 🚀 [デプロイメント戦略規約](modules/deployment-strategy.md)
- フェーズ別デプロイメント戦略
- コンテナ化とCI/CDパイプライン
- Blue-Green/Canaryデプロイメント
- Kubernetes設定と障害対応

### 🏗️ [ドメイン駆動設計（DDD）ガイドライン](modules/ddd-guidelines.md)
- 戦略的設計（境界づけられたコンテキスト）
- 戦術的設計（エンティティ・アグリゲート）
- ユビキタス言語とドメインサービス
- リポジトリパターンとドメインイベント

---

## 🔄 開発手法統合ガイドライン

### 🔄 [開発手法統合ガイドライン](modules/development-methodologies.md)
- 手法の組み合わせ戦略
- フェーズ別適用指針
- プロジェクト規模別適用戦略
- 継続的改善とメトリクス主導

### 📚 [ドキュメント駆動開発規約](modules/documentation-driven.md)
- Documentation First原則
- ドキュメント階層（戦略・設計・実装）
- OpenAPI/Swagger活用とADR
- 品質管理と更新フロー

### 🏃 [アジャイル開発プラクティス規約](modules/agile-practices.md)
- スクラム実装（チーム開発）
- カンバン実装（1人開発推奨）
- ユーザーストーリー作成と見積もり
- 継続的改善とベロシティ管理

### 🧪 [テスト駆動開発（TDD）実践規約](modules/tdd-practices.md)
- Red-Green-Refactorサイクル
- BDD（振る舞い駆動開発）とGherkin記法
- テストダブル活用とカバレッジ管理
- TDDツールチェーンと思考プロセス

---

## 🚀 新世代開発ガイドライン

### 🤖 [LLM活用ワークフロー・プロンプト戦略規約](modules/llm-workflow.md)
- LLM選択マトリクス（Claude/GPT-4/Gemini等）
- タスク別LLM活用戦略
- プロンプト設計パターン
- マルチLLMワークフロー実装

### 🛡️ [セキュリティ詳細実装規約](modules/security-implementation.md)
- OWASP Top 10対策とセキュアコーディング
- 脆弱性スキャン・ペネトレーションテスト
- インシデント対応プロセス
- データ保護・GDPR準拠

### 📈 [プロダクト管理・ビジネス戦略規約](modules/product-management.md)
- 市場分析・競合調査フレームワーク
- プロダクト戦略策定とロードマップ
- AARRR指標とKPI管理
- 収益化モデルとA/Bテスト

### 🎨 [UXリサーチ・デザインシステム規約](modules/ux-design-system.md)
- ユーザー中心設計プロセス
- UXリサーチ手法・LLM活用
- ペルソナ・ジャーニー設計
- デザインシステム構築
- アクセシビリティ・レスポンシブ設計

### 👥 [チーム・組織管理規約](modules/team-management.md)
- チーム構成・コラボレーション戦略
- パフォーマンス管理・成長支援
- リモートワーク・ハイブリッドワーク
- 採用・オンボーディング
- 学習・メンタリングプログラム

### 🤝 [ベンダー・外部統合管理規約](modules/vendor-management.md)
- ベンダー選定・評価フレームワーク
- セキュリティ・コンプライアンス
- 統合アーキテクチャ・監視
- コスト管理・最適化
- 継続的改善・関係管理

### 💰 [財務・コスト管理規約](modules/cost-management.md)
- コスト分類・追跡システム
- クラウドコスト最適化
- ROI評価・予算管理
- 技術的負債のコスト評価
- 段階的最適化戦略

### 📊 [データガバナンス・戦略規約](modules/data-governance.md)
- データ分類・プライバシー保護
- GDPR/CCPA準拠実装
- データアーキテクチャ・パイプライン
- データ品質管理・リネージュ
- メタデータ管理・カタログ

### 🔧 [レガシーシステム統合・移行規約](modules/legacy-migration.md)
- レガシーシステム評価フレームワーク
- 段階的移行戦略・パターン
- 統合アーキテクチャ・アダプター
- データ移行・品質保証
- 移行プロジェクト管理

### 📝 [コメントテンプレート集](templates/)
- [JavaScript/TypeScript](templates/javascript-comments.md)
- [Python](templates/python-comments.md)

### 🔧 [LLMの出力設定とコメント規約](modules/output-settings.md)
- 出力設定
- コメント規約（日本語必須）
- 言語設定

### 🤖 [自律的開発フレームワーク規約](modules/autonomous-development-framework.md)
- 意思決定フレームワーク（技術選択の判断基準）
- 自律判断権限の階層（完全自律・条件付き・相談必須）
- プロアクティブ開発の境界線
- 相談プロトコル（タイミング・構造・非同期活用）
- 品質・完成度の自律基準
- 実装パターンと標準化

---

## 📖 使用方法

### 🚀 プロジェクト開始時
- [基本原則と開発姿勢](modules/core-principles.md)でプロジェクト方針を確認
- [Claude 4 & Max プラン固有設定](modules/claude-model-settings.md)でモデル選択ポリシーを設定
- [権限・セキュリティ設定](modules/security-permissions.md)で安全な開発環境を構築

### 🛠️ 開発フェーズ別
- **技術選定時**: [LLM主導開発の技術選定原則](modules/llm-development.md)を参照
- **コーディング時**: [コーディング標準と品質管理](modules/coding-standards.md)と[コメントテンプレート](templates/)を活用
- **UI/UX設計時**: [UI/UX設計とアクセシビリティ](modules/ui-ux-guidelines.md)を参照
- **パフォーマンス検証時**: [パフォーマンス目標](modules/performance-targets.md)で品質基準を確認

### 🔒 セキュリティ・運用時
- **外部統合時**: [MCP/Integrations のセキュア運用](modules/mcp-integrations.md)でセキュアな連携を実現
- **PR作成時**: [自動PRワークフローの安全装備](modules/workflow-safety.md)で安全な自動化を確保
- **緊急時**: 各モジュールの緊急停止手順を参照

### 📋 日常的な参照
- **コメント作成**: [コメントテンプレート集](templates/)で一貫性のあるドキュメント作成
- **設定調整**: [LLMの出力設定](modules/output-settings.md)で Claude の動作をカスタマイズ
- **自律開発**: [自律的開発フレームワーク](modules/autonomous-development-framework.md)で判断基準を確認

## 🔄 更新とカスタマイズ

<important_notes>
**重要**: このファイルはすべてのClaude Codeセッションで自動的に読み込まれます。
これらは私が多くの開発において要求する必須要件であり、厳格に遵守してください。
プロジェクト固有の設定や具体的な技術スタック、よく使うコマンドなどは、
各プロジェクトのルートディレクトリにCLAUDE.mdを作成して定義してください。
</important_notes>

**モジュール構成の利点**:
- 📝 **保守性向上**: 各領域を独立して更新可能
- 🔍 **参照の容易さ**: 必要な情報へのクイックアクセス
- 📊 **可読性向上**: 長大な単一ファイルを避けて構造化
- 🔗 **再利用性**: 必要なモジュールのみを参照可能