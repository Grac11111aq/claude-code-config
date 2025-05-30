# MCP/Integrations のセキュア運用指針

## 1. 基本セキュリティ方針

<mcp_security_policy>
**最小権限の原則**: MCP統合では必要最小限のスコープとアクセス権限のみを付与する

### 1.1 統合前チェックリスト
- [ ] Integration scopeの最小化確認
- [ ] 必要なアクセス権限の明確化
- [ ] 機密データの取り扱い範囲確認
- [ ] ログ・監査設定の有効化
- [ ] 定期的なアクセスキーローテーション設定
</mcp_security_policy>

## 2. 認証情報管理

<credential_management>
### 2.1 .mcp.json の除外徹底
```bash
# .gitignore に必須設定
echo ".mcp.json" >> .gitignore
echo "*.mcp.json" >> .gitignore
echo ".claude/mcp-settings.json" >> .gitignore

# 既存リポジトリでの確認
git rm --cached .mcp.json 2>/dev/null || true
git status --ignored
```

### 2.2 Pre-commit フック設定
```bash
#!/bin/bash
# .git/hooks/pre-commit

# MCP設定ファイルの検出
if git diff --cached --name-only | grep -E "\.(mcp|claude).*\.json$"; then
    echo "🚨 エラー: MCP設定ファイルがコミットに含まれています"
    echo "以下のファイルを .gitignore に追加してください:"
    git diff --cached --name-only | grep -E "\.(mcp|claude).*\.json$"
    exit 1
fi

# 機密情報パターンの検出
if git diff --cached | grep -iE "(api[_-]?key|secret|token|password).*[:=].*['\"][^'\"]{8,}['\"]" then
    echo "🚨 警告: 機密情報らしき文字列が検出されました"
    echo "コミット前に内容を確認してください"
    exit 1
fi

echo "✅ セキュリティチェック完了"
```

### 2.3 Secrets Manager の活用
```json
{
  "mcp_config": {
    "use_secrets_manager": true,
    "provider": "environment",
    "key_rotation": {
      "enabled": true,
      "interval_days": 30,
      "notification": true
    }
  }
}
```

**環境変数での管理**:
```bash
# 推奨: 環境変数での管理
export NOTION_API_KEY="secret_..."
export GITHUB_TOKEN="ghp_..."
export SLACK_WEBHOOK_URL="https://hooks.slack.com/..."

# .mcp.json は環境変数を参照
{
  "notion": {
    "api_key": "${NOTION_API_KEY}",
    "scope": "read_only"
  }
}
```
</credential_management>

## 3. 統合別セキュリティ設定

<integration_security>
### 3.1 Notion 統合
```json
{
  "notion": {
    "scope": "read_only",  // 読み取り専用に制限
    "allowed_databases": [
      "specific_database_id_only"  // 特定DBのみアクセス
    ],
    "forbidden_properties": [
      "private_notes",
      "confidential_data"
    ],
    "rate_limit": {
      "requests_per_minute": 30,
      "burst_limit": 10
    }
  }
}
```

**最小権限設定手順**:
1. Notion側で統合作成時に「Read content」のみ選択
2. 必要なページ・データベースのみアクセス許可
3. 機密情報を含むページは除外
4. 定期的なアクセスログ確認

### 3.2 GitHub 統合
```json
{
  "github": {
    "scope": "repo:read,metadata:read",  // 最小限のスコープ
    "allowed_repos": [
      "organization/specific-repo"  // 特定リポジトリのみ
    ],
    "forbidden_operations": [
      "delete",
      "force_push",
      "admin"
    ],
    "webhook_verification": true
  }
}
```

### 3.3 Slack 統合
```json
{
  "slack": {
    "scope": "chat:write,files:read",
    "allowed_channels": [
      "#development",
      "#notifications"
    ],
    "forbidden_channels": [
      "#private",
      "#executive"
    ],
    "message_filtering": {
      "no_mentions": true,
      "no_sensitive_data": true
    }
  }
}
```
</integration_security>

## 4. 監視・アラート設定

<monitoring_alerts>
### 4.1 アクセス監視
```yaml
monitoring:
  api_calls:
    - endpoint: "notion.api"
      threshold: 100/hour
      alert_level: "WARNING"
    
    - endpoint: "github.api" 
      threshold: 50/hour
      alert_level: "INFO"
  
  data_access:
    - pattern: "confidential"
      action: "BLOCK"
      alert: "CRITICAL"
    
    - pattern: "personal_info"
      action: "LOG"
      alert: "WARNING"

  failed_auth:
    - threshold: 3
      timeframe: "5min"
      action: "TEMP_BLOCK"
```

### 4.2 異常検出
```javascript
// 異常パターンの自動検出
const anomalyDetection = {
  // 大量データアクセス
  bulk_access: {
    threshold: 1000,  // 1000件以上のデータアクセス
    timeframe: '1hour',
    action: 'alert_and_throttle'
  },
  
  // 深夜アクセス
  off_hours_access: {
    hours: [22, 6],  // 22時〜6時
    action: 'log_and_notify'
  },
  
  // 新しいIPからのアクセス
  new_ip_access: {
    action: 'verify_and_log'
  }
};
```

### 4.3 リアルタイム通知
```bash
# Webhook通知設定
curl -X POST "https://hooks.slack.com/..." \
  -H "Content-Type: application/json" \
  -d '{
    "text": "🚨 MCP統合異常検出",
    "attachments": [{
      "color": "danger",
      "fields": [{
        "title": "検出内容",
        "value": "Notion API 大量アクセス (1500件/時)",
        "short": false
      }]
    }]
  }'
```
</monitoring_alerts>

## 5. インシデント対応

<incident_response>
### 5.1 緊急時対応手順
```bash
#!/bin/bash
# emergency_disconnect.sh

echo "🚨 緊急時MCP統合停止スクリプト"

# 1. 全MCP接続の即座停止
pkill -f "mcp-server"

# 2. 設定ファイルの一時移動
mv .mcp.json .mcp.json.disabled

# 3. アクセストークンの一時無効化
export NOTION_API_KEY=""
export GITHUB_TOKEN=""

# 4. ログ保存
cp ~/.claude/logs/*.log ./incident_logs/

echo "✅ MCP統合を緊急停止しました"
echo "📋 次の手順:"
echo "1. ログを確認してインシデント原因を特定"
echo "2. 影響範囲を評価"
echo "3. 必要に応じてAPI キーをローテーション"
echo "4. 設定見直し後に接続を再開"
```

### 5.2 復旧手順
```bash
#!/bin/bash
# recovery_procedure.sh

echo "🔄 MCP統合復旧手順"

# 1. インシデント分析完了の確認
read -p "インシデント分析は完了しましたか? (y/N): " confirmed
if [[ $confirmed != "y" ]]; then
    echo "❌ 分析完了後に実行してください"
    exit 1
fi

# 2. 新しいAPIキーの設定確認
if [[ -z "$NOTION_API_KEY" ]]; then
    echo "❌ 新しいAPIキーが設定されていません"
    exit 1
fi

# 3. 設定ファイルの復元（最小権限で）
cp .mcp.json.template .mcp.json

# 4. 段階的接続復旧
echo "📡 Notion接続テスト..."
# 接続テスト実行

echo "✅ 復旧完了"
```

### 5.3 事後対応
- **インシデントレポート作成**: 原因、影響、対策を文書化
- **設定見直し**: より厳格なアクセス制限の検討
- **監視強化**: 検出ルールの追加・調整
- **教育**: チームメンバーへのセキュリティ意識向上
</incident_response>

## 6. 定期メンテナンス

<maintenance>
### 6.1 月次セキュリティ点検
```bash
#!/bin/bash
# monthly_security_check.sh

echo "🔍 月次セキュリティ点検"

# アクセスキーのローテーション確認
echo "1. APIキーの最終更新日確認..."

# アクセスログの監査
echo "2. 異常なアクセスパターンの確認..."

# 権限設定の見直し
echo "3. アクセス権限の最小化確認..."

# 統合設定の更新確認
echo "4. MCP設定の最新化確認..."

echo "✅ 月次点検完了"
```

### 6.2 キーローテーション
```json
{
  "key_rotation_schedule": {
    "notion_api_key": "monthly",
    "github_token": "quarterly", 
    "slack_webhook": "monthly",
    "notification": {
      "advance_days": 7,
      "reminder_days": [7, 3, 1]
    }
  }
}
```

### 6.3 アクセス権限監査
- **四半期レビュー**: 統合の必要性と権限範囲の再評価
- **年次監査**: 全統合の包括的セキュリティ審査
- **コンプライアンス確認**: 社内規定・法規制への適合性確認
</maintenance>