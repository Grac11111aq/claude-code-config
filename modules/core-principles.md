# 基本原則と開発姿勢

## 1. 基本原則

<core_principles>
<principle priority="highest">
**開発体制**: 原則として1人での開発を想定する
</principle>

<principle priority="high">
**開発効率の重視**: 開発環境での外部ツールの利用は開発効率を重視し積極的に行う（ただし、OSSとして公開する場合、その配布に支障がない範囲とする）
</principle>

<principle priority="high">
**パフォーマンスと開発効率のバランス**:
- 長期的にはパフォーマンスの高さを優先する
- ただし、技術選定においてパフォーマンスに有意な差が生じない場合は開発効率を優先する（究極のパフォーマンスではなく、必要十分なパフォーマンスを目指す）
</principle>

<principle>
**技術選定における新旧技術のバランス**:
- 主要な機能やコアコンポーネントには実績のある安定した技術を優先する
- ただし、開発効率を著しく向上させる新しいツールや、補助的な機能については、リスクを慎重に評価した上で、比較的新しい技術や実験的な技術の採用も検討する
</principle>

<principle>
**ライセンス選好**: GPLのようなコピーレフト効果の強いライセンスは避ける
</principle>

<principle>
**セキュリティ方針**:
- 標準的な範囲のセキュリティを基本とする（OWASP Top 10準拠）
- ただし、将来的な変更が困難な根幹部分については、開発効率を優先しつつも可能な限り高いセキュリティレベルを目指す
</principle>

<principle>
**拡張性への志向**:
- プラグイン機構のような拡張性の導入を積極的に検討する
- スケーラブルなアーキテクチャ（例：マイクロサービスアーキテクチャ的な実装）を好む
</principle>

<principle>
**開発プロセス・LLMとの連携**:
- コア機能以外の詳細は、実装段階でLLMに伝える
- 根幹的で後々の変更が難しい部分は、LLMと対話的に決定する
- LLMには、新たに採用した技術スタックの報告のみを求める。選定理由などは必要に応じて別途追求する
</principle>
</core_principles>

## 2. 利用環境・前提条件

<environment_requirements>
すべてのプロジェクトで想定する標準的な動作環境：

- **対応OS**: デスクトップOS全般（優先順：1. Windows, 2. Linux, 3. macOS）
- **想定マシンスペック**: Microsoft Officeのような一般的なオフィスツールが動作する程度
  - CPU: 2コア以上
  - メモリ: 4GB以上
  - ストレージ: 1GB以上の空き容量
- **インターネット接続**: 基本的に常時接続を想定
- **扱うデータサイズ**: 基本的に小さいものを想定（単一ファイル100MB以下）
- **データ保存場所（初期）**: ローカルのみ
</environment_requirements>

## 3. アーキテクチャとデータ処理

### 3.1 非同期処理
<async_processing>
- **標準パターン**: async/await を基本とする
- **タイムアウト**: デフォルト30秒（ユーザーが待てる限界）
- **UIブロッキング回避**: 100ms以上かかる処理は必ず非同期化
</async_processing>

### 3.2 状態管理
<state_management>
- **基本原則**: 単方向データフローを採用し、状態変更を予測可能にする
- **状態の分類**:
  - ローカル状態: 単一コンポーネント内で完結するデータ
  - 共有状態: 複数コンポーネントで使用するデータ
  - 永続化状態: アプリケーション再起動後も保持すべきデータ
- **楽観的更新**: ユーザー体験を優先し、エラー時はロールバック
</state_management>

### 3.3 API設計
<api_design>
- **標準形式**: REST + JSON
- **バージョニング**: URLパス方式（例: /api/v1/resources/）
- **レスポンス形式**:
```json
{
  "success": true,
  "data": { /* 実際のデータ */ },
  "error": null,
  "metadata": { "timestamp": "2024-01-01T00:00:00Z" }
}
```
</api_design>

## 4. 依存関係管理

<dependency_management>
- **依存関係の最小化**: コア機能に対する外部依存を最小限に抑える
- **バージョン固定**: 本番環境では厳密なバージョン固定（lock file使用）
- **セキュリティ監視**: Dependabotなどの自動監視ツールを導入
- **ライセンス監査**: 依存関係のライセンス互換性を定期的に確認
</dependency_management>

## 5. 国際化（i18n）戦略

<i18n_strategy>
- **デフォルト言語**: 日本語
- **実装方式**: キーベース + JSON形式
- **ファイル構造**:
```
locales/
  ├── ja.json  # 日本語（デフォルト）
  └── en.json  # 英語（将来対応）
```

### 5.1 キー命名規則とPrefix体系
```json
{
  "ui.button.save": "保存",
  "ui.button.cancel": "キャンセル",
  "ui.menu.file": "ファイル",
  "ui.dialog.confirm": "確認",
  
  "cli.command.help": "ヘルプ",
  "cli.option.verbose": "詳細表示",
  "cli.error.invalid_option": "無効なオプション",
  
  "error.file_not_found": "ファイルが見つかりません",
  "error.network_timeout": "ネットワークタイムアウト",
  "error.permission_denied": "アクセス権限がありません",
  
  "message.success.save": "正常に保存されました",
  "message.info.processing": "処理中...",
  "message.warning.unsaved": "未保存の変更があります",
  
  "validation.required": "この項目は必須です",
  "validation.email.invalid": "有効なメールアドレスを入力してください"
}
```

**Prefix 規則**:
| Prefix | 用途 | 例 |
|--------|------|-----|
| `ui.` | ユーザーインターフェース要素 | ボタン、メニュー、ラベル |
| `cli.` | コマンドラインインターフェース | コマンド、オプション、ヘルプ |
| `error.` | エラーメッセージ | システムエラー、バリデーションエラー |
| `message.` | 通知・メッセージ | 成功、警告、情報 |
| `validation.` | フォーム検証メッセージ | 入力チェック、フォーマット |
| `dialog.` | ダイアログ・モーダル | 確認ダイアログ、設定画面 |
| `tooltip.` | ツールチップ・ヘルプ | 説明文、ガイダンス |

**階層化ルール**:
- 第1レベル: 機能領域（ui, cli, error等）
- 第2レベル: 具体的なコンポーネント（button, menu等）
- 第3レベル: 具体的なアクション・状態（save, cancel等）

**管理ツールとの連携**:
- Extract/Update: `prefix.*` パターンで一括抽出
- Validation: 未翻訳キーの自動検出
- Import/Export: CSV、XLIFF形式での変換対応
</i18n_strategy>

## 6. ビルド・デプロイメント戦略

<build_deployment>
- **ビルド最適化**: バランス型（開発効率と実行効率の両立）
- **環境別設定**: 環境変数による管理
- **ビルド目標**:
  - 起動時間: 1秒以内
  - バンドルサイズ: 100MB以内（デスクトップアプリ）
- **ソースマップ**: 開発環境では有効、本番環境では無効
</build_deployment>

## 7. ドキュメンテーション標準

### 7.1 必須ドキュメント
<documentation_requirements>
1. **README.md**: プロジェクト概要、セットアップ、基本的な使い方
2. **コード内ドキュメント**: 複雑なロジックや設計判断の説明
3. **API仕様書**: 公開APIの詳細仕様（OpenAPI/Swagger形式推奨）
</documentation_requirements>

### 7.2 ドキュメント形式
- **図表**: Mermaid（GitHubで直接レンダリング可能）
- **API仕様**: OpenAPI 3.0
- **ユーザーマニュアル**: Markdown + 静的サイトジェネレータ

## 8. バージョン管理とGit運用

### 8.1 基本運用
<git_workflow>
- **バージョン管理システムとしてGitを利用する。**
- **ブランチ戦略**: GitHub Flowを基本とする（main + feature branches）
</git_workflow>

### 8.2 コミットメッセージ規約
**Conventional Commits準拠**:
```
<type>(<scope>): <subject>

<body>

<footer>
```

**typeの定義**:
| type | 用途 |
|------|------|
| feat | 新機能追加 |
| fix | バグ修正 |
| docs | ドキュメントのみの変更 |
| style | コードの意味に影響しない変更（空白、フォーマット等） |
| refactor | バグ修正や機能追加を伴わないコード変更 |
| test | テストの追加・修正 |
| chore | ビルドプロセスやツールの変更 |