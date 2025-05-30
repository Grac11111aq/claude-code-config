# 🌐 API設計規約

<module_info>
このモジュールは、API設計の規約を定義します。
「設定より規約」の原則に基づき、シンプルで実用的な設計を推進します。
</module_info>

## 📐 基本規約

### APIスタイル

```yaml
デフォルト: REST
GraphQL検討: クライアントが多様で、過度なカスタマイズが必要な場合のみ
```

### エンドポイント設計

```yaml
規約:
  - 名詞の複数形を使用: /users, /products
  - ネストは2階層まで: /users/{id}/posts
  - 動詞はHTTPメソッドで表現
  
HTTPメソッド:
  GET: 取得
  POST: 作成
  PUT: 完全更新
  PATCH: 部分更新
  DELETE: 削除
```

## 🔄 バージョニング

```yaml
デフォルト: パスベース（/v1/users）
廃止ポリシー: 新版公開後6ヶ月は旧版維持
```

## 📄 レスポンス形式

### 成功レスポンス

```json
{
  "data": {
    // 実際のデータ
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z"
  }
}
```

### エラーレスポンス

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "検証エラーが発生しました",
    "details": [
      {
        "field": "email",
        "message": "有効なメールアドレスを入力してください"
      }
    ]
  }
}
```

## 📑 ページネーション

```yaml
デフォルト方式: オフセットベース
パラメータ:
  - page: ページ番号（1から開始）
  - per_page: 1ページあたりの件数（デフォルト: 20、最大: 100）
  
レスポンスヘッダー:
  - X-Total-Count: 総件数
  - Link: ページネーションリンク（RFC 5988準拠）
```

## 🔐 認証・認可

```yaml
デフォルト: Bearer Token（Authorization ヘッダー）
形式: Authorization: Bearer {token}
```

## 📋 HTTPステータスコード

```yaml
成功:
  200: OK（取得・更新成功）
  201: Created（作成成功）
  204: No Content（削除成功）

エラー:
  400: Bad Request（リクエスト不正）
  401: Unauthorized（認証エラー）
  403: Forbidden（認可エラー）
  404: Not Found（リソース不在）
  422: Unprocessable Entity（検証エラー）
  500: Internal Server Error（サーバーエラー）
```

## 🌍 国際化

```yaml
言語指定: Accept-Language ヘッダー
デフォルト: ja
形式: Accept-Language: ja, en;q=0.9
```

## ⚡ パフォーマンス

```yaml
圧縮: gzip必須
キャッシュ: ETag + Cache-Control
タイムアウト: 30秒
```