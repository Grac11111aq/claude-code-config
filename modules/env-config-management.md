# ⚙️ 環境・設定管理規約

<module_info>
このモジュールは、環境変数と設定管理の規約を定義します。
シンプルで安全な設定管理により、環境差異によるトラブルを防ぎます。
</module_info>

## 📐 基本規約

### 環境の定義

```yaml
標準環境:
  development: ローカル開発
  staging: 本番相当のテスト環境
  production: 本番環境
```

### 設定の優先順位

```yaml
1. 環境変数（最優先）
2. .env.{environment}ファイル
3. .envファイル（共通設定）
4. デフォルト値（コード内）
```

## 🔐 シークレット管理

```yaml
禁止事項:
  - シークレットのハードコーディング
  - .envファイルのコミット（.env.exampleのみ可）
  
推奨方法:
  開発: .env.localファイル（gitignore）
  本番: 環境変数または専用のシークレット管理サービス
```

## 📝 命名規約

```yaml
形式: CATEGORY_SUBCATEGORY_NAME
例:
  DATABASE_HOST
  DATABASE_PORT
  API_KEY_OPENAI
  FEATURE_FLAG_NEW_UI
```

## 🗂️ ファイル構成

```bash
project/
├── .env.example        # コミット可（値は例示）
├── .env               # 共通設定（gitignore）
├── .env.development   # 開発環境（gitignore）
├── .env.staging       # ステージング（gitignore）
└── .env.production    # 本番（gitignore）
```

### .env.example

```bash
# データベース設定
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=myapp_dev
DATABASE_USER=postgres
DATABASE_PASSWORD=your_password_here

# API設定
API_PORT=3000
API_BASE_URL=http://localhost:3000

# 外部サービス
API_KEY_OPENAI=your_api_key_here

# フィーチャーフラグ
FEATURE_FLAG_NEW_UI=false
```

## 🏷️ 型安全性

### TypeScript例

```typescript
// config/env.ts
const env = {
  database: {
    host: process.env.DATABASE_HOST || 'localhost',
    port: parseInt(process.env.DATABASE_PORT || '5432'),
    name: process.env.DATABASE_NAME || 'myapp_dev',
  },
  api: {
    port: parseInt(process.env.API_PORT || '3000'),
    baseUrl: process.env.API_BASE_URL || 'http://localhost:3000',
  },
  features: {
    newUI: process.env.FEATURE_FLAG_NEW_UI === 'true',
  },
} as const;

export default env;
```

## 🚨 バリデーション

```yaml
起動時チェック:
  - 必須環境変数の存在確認
  - 型・形式の検証
  - 依存関係の整合性
  
エラー時:
  - 明確なエラーメッセージ
  - 不足している環境変数のリスト表示
  - 起動を中止
```

## 📋 ドキュメント化

```yaml
必須項目（.env.example内）:
  - 各変数の説明コメント
  - 必須/任意の明記
  - 有効な値の範囲
  - デフォルト値
```