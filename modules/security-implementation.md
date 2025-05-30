# 🛡️ セキュリティ詳細実装規約

<module_info>
このモジュールは、セキュリティの詳細実装規約を定義します。
脅威の早期発見と対策により、安全で信頼性の高いシステムを構築します。
</module_info>

## 📐 基本方針

### セキュリティ・バイ・デザイン

```yaml
設計段階からの組み込み:
  - セキュリティ要件の明確化
  - 脅威モデリング実施
  - セキュアコーディング規約
  - 定期的なセキュリティレビュー

多層防御戦略:
  - ネットワークレベル
  - アプリケーションレベル
  - データレベル
  - 物理/環境レベル
```

### リスクベースアプローチ

```yaml
リスク評価マトリクス:
  影響度: 低(1) - 中(2) - 高(3) - 極高(4)
  発生確率: 低(1) - 中(2) - 高(3) - 極高(4)
  総合スコア: 影響度 × 発生確率

対応優先度:
  12-16: 即座に対応（24時間以内）
  8-11: 高優先度（1週間以内）
  4-7: 中優先度（1ヶ月以内）
  1-3: 低優先度（四半期内）
```

## 🔒 アプリケーションセキュリティ

### OWASP Top 10対策

```yaml
A01: アクセス制御の破綻:
  対策:
    - 最小権限の原則
    - セッション管理強化
    - 認可チェックの実装
  
  実装例:
    - JWT トークンのスコープ制限
    - ロールベースアクセス制御
    - リソースレベル認可

A02: 暗号化の失敗:
  対策:
    - 強力な暗号化アルゴリズム使用
    - 適切なキー管理
    - データ分類と保護レベル設定
  
  実装例:
    - AES-256-GCM for data encryption
    - bcrypt for password hashing
    - TLS 1.3 for data in transit

A03: インジェクション:
  対策:
    - パラメータ化クエリ
    - 入力値検証・サニタイズ
    - WAF（Web Application Firewall）
  
  実装例:
    - SQLインジェクション対策
    - XSS対策（CSP実装）
    - コマンドインジェクション対策
```

### セキュアコーディング規約

```typescript
// 入力値検証
export class InputValidator {
  static validateEmail(email: string): boolean {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!email || email.length > 254) return false;
    return emailRegex.test(email);
  }
  
  static sanitizeSQL(input: string): string {
    // パラメータ化クエリ使用必須
    throw new Error('Use parameterized queries instead');
  }
  
  static sanitizeHTML(input: string): string {
    return input
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#x27;');
  }
}

// セキュアなパスワード処理
export class PasswordSecurity {
  private static readonly SALT_ROUNDS = 12;
  
  static async hashPassword(password: string): Promise<string> {
    // 強度チェック
    if (!this.isStrongPassword(password)) {
      throw new WeakPasswordError();
    }
    
    return bcrypt.hash(password, this.SALT_ROUNDS);
  }
  
  static isStrongPassword(password: string): boolean {
    return (
      password.length >= 8 &&
      /[A-Z]/.test(password) &&
      /[a-z]/.test(password) &&
      /[0-9]/.test(password) &&
      /[^A-Za-z0-9]/.test(password)
    );
  }
}

// セキュアなセッション管理
export class SessionManager {
  static generateSecureToken(): string {
    return crypto.randomBytes(32).toString('hex');
  }
  
  static setSecureCookie(res: Response, name: string, value: string): void {
    res.cookie(name, value, {
      httpOnly: true,      // XSS対策
      secure: true,        // HTTPS必須
      sameSite: 'strict',  // CSRF対策
      maxAge: 24 * 60 * 60 * 1000, // 24時間
      path: '/'
    });
  }
}
```

## 🔍 脆弱性スキャン・テスト

### 自動セキュリティテスト

```yaml
SAST（Static Application Security Testing）:
  ツール: SonarQube, Semgrep, CodeQL
  実行タイミング: PR作成時、コミット時
  対象: ソースコード、設定ファイル

DAST（Dynamic Application Security Testing）:
  ツール: OWASP ZAP, Burp Suite
  実行タイミング: デプロイ後
  対象: 実行中のアプリケーション

依存関係スキャン:
  ツール: npm audit, Snyk, Dependabot
  実行タイミング: 日次、PR時
  対象: 使用ライブラリ・パッケージ
```

### CI/CD統合

```yaml
# .github/workflows/security.yml
name: Security Scan

on: [push, pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      # 依存関係スキャン
      - name: Run npm audit
        run: npm audit --audit-level moderate
      
      # SAST
      - name: Run Semgrep
        uses: returntocorp/semgrep-action@v1
        with:
          config: auto
      
      # シークレット検出
      - name: Run GitLeaks
        uses: zricethezav/gitleaks-action@master
      
      # 脆弱性データベースチェック
      - name: Run Trivy
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
```

### ペネトレーションテスト

```yaml
実施頻度:
  自動: 日次（基本スキャン）
  手動: 月次（詳細テスト）
  第三者: 年次（包括的監査）

テスト項目:
  認証・認可: 不正アクセス試行
  入力検証: インジェクション攻撃
  セッション管理: ハイジャック・固定化
  暗号化: 弱い暗号化の検出
  設定: セキュリティ設定の確認
```

## 🚨 インシデント対応

### インシデント分類

```yaml
Level 1（重大）:
  定義: システム停止、データ漏洩の可能性
  対応時間: 30分以内
  対応者: セキュリティチーム + 経営陣
  
Level 2（高）:
  定義: 部分的な機能停止、アクセス異常
  対応時間: 2時間以内
  対応者: セキュリティチーム + 開発チーム
  
Level 3（中）:
  定義: パフォーマンス劣化、軽微な異常
  対応時間: 4時間以内
  対応者: 開発チーム
  
Level 4（低）:
  定義: 報告レベル、ログ記録
  対応時間: 営業時間内
  対応者: 担当者
```

### 対応プロセス

```yaml
即座の対応（30分以内）:
  1. インシデント確認・分類
  2. 影響範囲の特定
  3. 初期封じ込め実施
  4. 関係者への連絡

詳細調査（2-4時間）:
  1. 根本原因の特定
  2. 被害状況の詳細調査
  3. 証拠保全
  4. 追加対策の実施

事後対応（1週間以内）:
  1. インシデントレポート作成
  2. 再発防止策の策定
  3. プロセス・システム改善
  4. ステークホルダー報告
```

### 実装例

```typescript
// インシデント検知システム
export class SecurityMonitor {
  private alertThresholds = {
    failedLogins: 5,        // 5回連続失敗
    suspiciousRequests: 10, // 10回/分の異常リクエスト
    dataAccess: 100         // 100件/分の大量アクセス
  };
  
  async detectAnomalies(): Promise<SecurityIncident[]> {
    const incidents: SecurityIncident[] = [];
    
    // 失敗ログイン検知
    const failedLogins = await this.countFailedLogins();
    if (failedLogins.count > this.alertThresholds.failedLogins) {
      incidents.push(new SecurityIncident('BRUTE_FORCE', failedLogins));
    }
    
    // 異常アクセス検知
    const suspiciousIPs = await this.detectSuspiciousIPs();
    if (suspiciousIPs.length > 0) {
      incidents.push(new SecurityIncident('SUSPICIOUS_ACCESS', suspiciousIPs));
    }
    
    return incidents;
  }
  
  async respondToIncident(incident: SecurityIncident): Promise<void> {
    // 自動対応
    switch (incident.type) {
      case 'BRUTE_FORCE':
        await this.blockIP(incident.sourceIP);
        break;
      case 'SUSPICIOUS_ACCESS':
        await this.enableEnhancedMonitoring(incident.sourceIP);
        break;
    }
    
    // アラート送信
    await this.sendAlert(incident);
  }
}
```

## 🔐 データ保護・プライバシー

### 個人情報保護（GDPR準拠）

```yaml
データ分類:
  個人識別情報（PII）:
    - 氏名、住所、電話番号
    - メールアドレス、アカウント情報
    - 暗号化必須、アクセス制限
  
  機密情報:
    - パスワード、APIキー
    - 決済情報、財務データ
    - 暗号化必須、最小権限

処理方針:
  収集時: 目的明示、同意取得
  保存時: 暗号化、アクセス制御
  利用時: 目的外利用禁止
  削除時: 確実な削除、ログ記録
```

### データ暗号化

```typescript
// データ暗号化ユーティリティ
export class DataEncryption {
  private static readonly ALGORITHM = 'aes-256-gcm';
  private static readonly KEY_LENGTH = 32;
  
  static encrypt(data: string, key: Buffer): EncryptedData {
    const iv = crypto.randomBytes(16);
    const cipher = crypto.createCipher(this.ALGORITHM, key);
    cipher.setAAD(Buffer.from('additional data'));
    
    let encrypted = cipher.update(data, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    
    const authTag = cipher.getAuthTag();
    
    return {
      encrypted,
      iv: iv.toString('hex'),
      authTag: authTag.toString('hex')
    };
  }
  
  static decrypt(encryptedData: EncryptedData, key: Buffer): string {
    const decipher = crypto.createDecipher(this.ALGORITHM, key);
    decipher.setAAD(Buffer.from('additional data'));
    decipher.setAuthTag(Buffer.from(encryptedData.authTag, 'hex'));
    
    let decrypted = decipher.update(encryptedData.encrypted, 'hex', 'utf8');
    decrypted += decipher.final('utf8');
    
    return decrypted;
  }
}

// PII マスキング
export class PIIMasking {
  static maskEmail(email: string): string {
    const [local, domain] = email.split('@');
    const maskedLocal = local.substring(0, 2) + '*'.repeat(local.length - 2);
    return `${maskedLocal}@${domain}`;
  }
  
  static maskCreditCard(cardNumber: string): string {
    return cardNumber.replace(/\d(?=\d{4})/g, '*');
  }
}
```

## 🏗️ インフラセキュリティ

### ネットワークセキュリティ

```yaml
ファイアウォール設定:
  Inbound Rules:
    - HTTP/HTTPS (80/443): 全世界からアクセス許可
    - SSH (22): 管理用IPアドレスのみ
    - その他: 全て拒否
  
  Outbound Rules:
    - HTTPS (443): 外部API通信用
    - DNS (53): 名前解決用
    - その他: 必要最小限

WAF（Web Application Firewall）:
  - SQLインジェクション検知・ブロック
  - XSS攻撃検知・ブロック
  - DDoS攻撃軽減
  - IP ベースアクセス制御
```

### コンテナセキュリティ

```dockerfile
# セキュアなDockerfile例
FROM node:18-alpine AS builder

# セキュリティアップデート
RUN apk update && apk upgrade

# 非rootユーザー作成
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001

# 最小限のファイルのみコピー
COPY --chown=nextjs:nodejs package*.json ./
RUN npm ci --only=production && npm cache clean --force

FROM node:18-alpine AS runtime

# セキュリティ設定
RUN apk update && apk upgrade && \
    apk add --no-cache dumb-init && \
    addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001

# アプリケーションファイル
COPY --from=builder --chown=nextjs:nodejs /app ./
USER nextjs

# プロセス管理
ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "server.js"]

# ヘルスチェック
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```

## 📊 セキュリティメトリクス

### 監視指標

```yaml
技術指標:
  - 脆弱性件数（重要度別）
  - パッチ適用率
  - セキュリティインシデント件数
  - 平均対応時間（MTTR）

プロセス指標:
  - セキュリティテスト実行率
  - 教育・訓練実施率
  - ポリシー遵守率
  - 監査結果

ビジネス指標:
  - システム可用性
  - データ整合性
  - 法的コンプライアンス適合率
  - 顧客信頼度
```

### レポーティング

```typescript
// セキュリティダッシュボード
export class SecurityDashboard {
  async generateWeeklyReport(): Promise<SecurityReport> {
    return {
      period: this.getWeekPeriod(),
      vulnerabilities: await this.getVulnerabilityStats(),
      incidents: await this.getIncidentStats(),
      compliance: await this.getComplianceStatus(),
      recommendations: await this.generateRecommendations()
    };
  }
  
  private async getVulnerabilityStats(): Promise<VulnerabilityStats> {
    return {
      total: await this.countVulnerabilities(),
      bySeverity: await this.countBySeverity(),
      byCategory: await this.countByCategory(),
      resolved: await this.countResolved(),
      avgResolutionTime: await this.calculateAvgResolutionTime()
    };
  }
}
```

## 📋 セキュリティチェックリスト

```yaml
開発時:
  - [ ] セキュリティ要件の明確化
  - [ ] 脅威モデリングの実施
  - [ ] セキュアコーディング規約遵守
  - [ ] セキュリティテストの実行

デプロイ時:
  - [ ] セキュリティ設定の確認
  - [ ] 脆弱性スキャンの実行
  - [ ] アクセス権限の確認
  - [ ] 監視設定の有効化

運用時:
  - [ ] 定期的な脆弱性アセスメント
  - [ ] セキュリティパッチの適用
  - [ ] インシデント対応訓練
  - [ ] セキュリティ教育の実施
```