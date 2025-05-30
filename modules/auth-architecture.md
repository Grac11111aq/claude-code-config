# 🔐 認証・認可アーキテクチャ規約

<module_info>
このモジュールは、認証・認可の統一的なアーキテクチャを定義します。
マイクロサービス環境でも一貫したセキュリティを実現します。
</module_info>

## 📐 基本規約

### 認証方式

```yaml
デフォルト: JWT (JSON Web Token)
理由:
  - ステートレス
  - マイクロサービスに適合
  - 標準的で広くサポート

代替案:
  セッションベース: モノリス初期段階
  OAuth2: 外部連携が必要な場合
```

### トークン戦略

```yaml
アクセストークン:
  有効期限: 15分
  用途: API アクセス
  保存場所: メモリ（フロントエンド）

リフレッシュトークン:
  有効期限: 7日
  用途: アクセストークン更新
  保存場所: HttpOnly Cookie（セキュア）
```

## 🏗️ JWT構造

### ペイロード標準

```json
{
  "sub": "user-uuid",
  "email": "user@example.com",
  "roles": ["user", "admin"],
  "permissions": ["read:users", "write:orders"],
  "iat": 1234567890,
  "exp": 1234568790,
  "jti": "unique-token-id"
}
```

### 署名アルゴリズム

```yaml
推奨: RS256（非対称鍵）
理由:
  - 公開鍵での検証が可能
  - マイクロサービス間で秘密鍵共有不要
  - セキュリティが高い

開発環境: HS256（対称鍵）も可
```

## 🎯 認証フロー

### ログインフロー

```mermaid
1. POST /auth/login (email, password)
2. 認証サービスで検証
3. JWT生成（アクセス + リフレッシュ）
4. レスポンス:
   - アクセストークン: Body
   - リフレッシュトークン: HttpOnly Cookie
```

### トークンリフレッシュ

```yaml
エンドポイント: POST /auth/refresh
Cookie: refresh_token
レスポンス: 新しいアクセストークン
```

## 🛡️ 認可設計

### RBAC（Role-Based Access Control）

```yaml
基本構造:
  User → Roles → Permissions

実装例:
  roles:
    - admin: すべての権限
    - manager: 読み書き権限
    - viewer: 読み取りのみ
    
  permissions:
    - users:read
    - users:write
    - orders:*
```

### 権限チェック実装

```typescript
// ミドルウェア例
export const requirePermission = (permission: string) => {
  return (req: Request, res: Response, next: NextFunction) => {
    const user = req.user;
    
    if (!user || !user.permissions.includes(permission)) {
      return res.status(403).json({
        error: {
          code: 'AUTH_FORBIDDEN',
          message: '権限がありません'
        }
      });
    }
    
    next();
  };
};

// 使用例
router.delete('/users/:id', 
  authenticate,
  requirePermission('users:delete'),
  deleteUser
);
```

## 🔄 マイクロサービス間認証

### サービス間通信

```yaml
方式: mTLS + サービストークン
実装:
  - 各サービスに証明書配布
  - 内部通信用JWTトークン
  - API Gatewayでの統一検証
```

### トークン伝播

```typescript
// サービス間リクエスト
const callUserService = async (userId: string, token: string) => {
  return axios.get(`http://user-service/users/${userId}`, {
    headers: {
      'Authorization': `Bearer ${token}`,
      'X-Service-Name': 'order-service',
      'X-Request-ID': generateRequestId()
    }
  });
};
```

## 🚪 API Gateway統合

```yaml
責務:
  - トークン検証
  - レート制限
  - 権限チェック
  - トークン情報をヘッダーに追加

実装:
  location /api/ {
    # トークン検証
    access_by_lua_block {
      local token = ngx.var.http_authorization
      local claims = verify_jwt(token)
      
      # ヘッダーに追加
      ngx.req.set_header("X-User-ID", claims.sub)
      ngx.req.set_header("X-User-Roles", claims.roles)
    }
    
    proxy_pass http://backend;
  }
```

## 🔑 パスワード管理

```yaml
ハッシュアルゴリズム: bcrypt
コスト係数: 12（本番環境）
検証:
  - 最小8文字
  - 大文字・小文字・数字を含む
  - 一般的なパスワードを禁止
```

## 🌐 ソーシャルログイン

```yaml
対応プロバイダー:
  - Google (デフォルト)
  - GitHub
  - Microsoft

実装:
  - OAuth2フロー
  - プロバイダートークンをJWTに変換
  - 初回ログイン時にユーザー作成
```

## 📱 多要素認証（MFA）

```yaml
方式:
  - TOTP (Time-based One-Time Password)
  - SMS（非推奨）
  - WebAuthn（将来対応）

実装:
  - QRコード生成
  - バックアップコード提供
  - デバイス記憶オプション
```

## 🧪 セキュリティテスト

```typescript
describe('認証テスト', () => {
  it('無効なトークンでアクセス拒否', async () => {
    const response = await request(app)
      .get('/api/users')
      .set('Authorization', 'Bearer invalid-token');
      
    expect(response.status).toBe(401);
  });
  
  it('権限不足で403エラー', async () => {
    const token = generateToken({ roles: ['viewer'] });
    
    const response = await request(app)
      .delete('/api/users/123')
      .set('Authorization', `Bearer ${token}`);
      
    expect(response.status).toBe(403);
  });
});
```

## ⚠️ セキュリティ考慮事項

```yaml
必須対策:
  - HTTPS通信のみ
  - CORS設定の厳格化
  - Rate Limiting実装
  - トークンブラックリスト機能
  - 監査ログの記録

避けるべきこと:
  - トークンのローカルストレージ保存
  - 機密情報のJWT格納
  - 長すぎる有効期限
  - 平文パスワード送信
```