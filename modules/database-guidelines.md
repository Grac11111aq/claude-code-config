# 🗄️ データベース設計規約

<module_info>
このモジュールは、データベース設計の規約を定義します。
「設定より規約」の原則に基づき、デフォルトの選択を明確にし、
特別な理由がある場合のみ代替案を検討します。
</module_info>

## 📐 基本規約

### データベース選定

```yaml
デフォルト: PostgreSQL
代替案:
  SQLite: プロトタイプ、組み込みアプリケーションのみ
  MongoDB: PostgreSQL JSONBで対応不可能な場合のみ
```

### ID戦略

```yaml
デフォルト: BIGSERIAL（内部システム）
外部公開時: UUID必須
```

### 削除戦略

```yaml
デフォルト: 論理削除（deleted_at）
物理削除: 法的要件がある場合のみ
```

## 📐 設計規約

### 命名規則

```yaml
テーブル: snake_case、複数形
カラム: snake_case
外部キー: {単数形}_id
インデックス: idx_{テーブル名}_{カラム名}
```

### 必須カラム

```sql
-- すべてのテーブルに必須
created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP

-- 論理削除時
deleted_at TIMESTAMP WITH TIME ZONE
```

## 🔄 マイグレーション規約

```yaml
ファイル名: {YYYYMMDDHHMMSS}_{動詞}_{対象}.sql
必須要件: ロールバック可能
```

## 📊 パフォーマンス規約

```yaml
自動インデックス: 外部キー、deleted_at
追加検討: WHERE/JOIN/ORDER BYで頻繁に使用されるカラム
```