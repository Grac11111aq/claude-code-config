# 権限・セキュリティ設定

## 1. 権限管理の基本方針

<permission_management>
**最小権限の原則**: 必要最小限の権限のみを付与し、定期的に見直しを行う

### 1.1 settings.json による集中管理
```json
{
  "allowed_tools": [
    "Read", "Write", "Edit", "MultiEdit",
    "Bash", "Glob", "Grep",
    "LS", "TodoRead", "TodoWrite"
  ],
  "blocked_commands": [
    "rm -rf", "sudo rm", "del /f /s /q",
    "format", "mkfs", "dd if=",
    "curl -X DELETE", "wget --delete-after"
  ],
  "require_confirmation": [
    "git push --force",
    "npm publish",
    "docker run --privileged",
    "chmod 777"
  ]
}
```

### 1.2 危険コマンドのブラックリスト
```yaml
# 絶対禁止コマンド
blacklist:
  - "rm -rf /"
  - "sudo rm -rf"
  - "mkfs.*"
  - "dd if=/dev/zero"
  - ":(){ :|:& };:"  # フォーク爆弾
  - "curl.*|sh"     # 不明スクリプト実行
  
# 警告付きで確認が必要
warning_commands:
  - "git reset --hard"
  - "git push --force"
  - "npm install.*--unsafe-perm"
  - "chmod.*777"
</permission_management>

## 2. ファイルシステム保護

<filesystem_protection>
### 2.1 保護対象ディレクトリ
```
# 読み取り専用
readonly_dirs:
  - /etc/
  - /usr/bin/
  - /System/
  - C:\Windows\System32\

# アクセス禁止
forbidden_dirs:
  - /root/
  - ~/.ssh/
  - ~/.aws/
  - ~/.config/claude/credentials
```

### 2.2 ファイル操作の制限
- **重要設定ファイル**: 変更前に自動バックアップ
- **実行ファイル**: 作成時に明示的確認
- **隠しファイル**: .gitignore, .env等の変更は警告表示
- **大容量ファイル**: 100MB以上は分割処理を推奨

### 2.3 バックアップ戦略
```bash
# 重要ファイル変更前の自動バックアップ
auto_backup_patterns:
  - "package.json"
  - "*.config.js"
  - "Dockerfile"
  - "docker-compose.yml"
  - ".env*"
```
</filesystem_protection>

## 3. ネットワーク・外部アクセス制御

<network_security>
### 3.1 許可されたドメイン
```yaml
allowed_domains:
  - "github.com"
  - "gitlab.com"
  - "npmjs.com"
  - "pypi.org"
  - "crates.io"
  - "api.anthropic.com"

# 事前承認が必要
require_approval:
  - "*.amazonaws.com"
  - "*.googleapis.com"
  - "*.azurewebsites.net"
```

### 3.2 API키・認証情報管理
- **環境変数**: .env ファイルの .gitignore 必須
- **credentials**: 専用ディレクトリで管理
- **トークン**: 定期的ローテーション推奨
- **暗号化**: 保存時は必ず暗号化

### 3.3 外部ツール連携
```json
{
  "mcp_integrations": {
    "require_encryption": true,
    "scope_minimum": true,
    "audit_access": true,
    "rotate_keys": "monthly"
  }
}
```
</network_security>

## 4. 開発環境のセキュリティ

<development_security>
### 4.1 依存関係セキュリティ
```bash
# 必須チェック
npm audit --audit-level moderate
pip-audit
cargo audit

# 自動修正（低リスクのみ）
npm audit fix --only=prod
```

### 4.2 機密情報の検出
```yaml
# スキャン対象パターン
secrets_patterns:
  - "password\\s*=\\s*['\"][^'\"]+['\"]"  
  - "api[_-]?key\\s*[=:]\\s*['\"][^'\"]+['\"]"  
  - "secret\\s*[=:]\\s*['\"][^'\"]+['\"]"  
  - "token\\s*[=:]\\s*['\"][^'\"]+['\"]"  
  - "AWS.*[A-Z0-9]{20}"
```

### 4.3 コード品質ゲート
- **静的解析**: 必須（SonarQube, ESLint）
- **脆弱性スキャン**: Critical/High は0件
- **ライセンスチェック**: GPL系の検出と排除
- **暗号化チェック**: 弱いアルゴリズムの検出
</development_security>

## 5. 監査・ログ管理

<audit_logging>
### 5.1 操作ログ
```json
{
  "audit_log": {
    "enabled": true,
    "level": "INFO",
    "retention_days": 90,
    "log_file": "~/.claude/logs/audit.jsonl"
  }
}
```

### 5.2 記録対象
- ✅ ファイル作成・変更・削除
- ✅ コマンド実行（引数含む）
- ✅ 外部API呼び出し
- ✅ 権限昇格試行
- ✅ 設定変更

### 5.3 アラート設定
```yaml
alerts:
  - pattern: "sudo.*"
    level: "WARNING"
  - pattern: "rm.*-rf"
    level: "CRITICAL"
  - pattern: "curl.*|sh"
    level: "CRITICAL"
```
</audit_logging>

## 6. 緊急時対応

<emergency_response>
### 6.1 インシデント検知
- 異常なファイル削除の検出
- 不審なネットワークアクセス
- 権限昇格の失敗連続
- 大量データアクセス

### 6.2 自動対応
```bash
# セーフモード自動発動
if detect_suspicious_activity; then
  enable_safe_mode
  notify_user "セキュリティインシデント検出"
  create_system_snapshot
fi
```

### 6.3 復旧手順
1. **即座停止**: 疑わしい操作の中断
2. **状況把握**: ログ分析とダメージ評価
3. **隔離**: 影響範囲の特定と封じ込め
4. **復旧**: バックアップからの復元
5. **再発防止**: セキュリティ設定の見直し
</emergency_response>