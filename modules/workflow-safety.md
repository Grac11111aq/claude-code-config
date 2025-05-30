# 自動PRワークフローの安全装備

## 1. 自動マージ防止設定

<auto_merge_prevention>
**基本原則**: **自動マージ禁止** - 必ずユーザーの明示的承認を経てからマージ

### 1.1 必須承認フロー
```yaml
# .github/workflows/claude-pr.yml
name: Claude Code PR Safety

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  safety-check:
    runs-on: ubuntu-latest
    steps:
      - name: Check approval requirement
        run: |
          if [[ ! "${{ github.event.pull_request.labels }}" =~ "user-approved" ]]; then
            echo "::error::User approval required before merge"
            exit 1
          fi
```

### 1.2 ラベルベース制御
```bash
# マージ前必須ラベル
required_labels:
  - "user-approved"     # ユーザーが内容確認済み
  - "tests-passing"     # テスト通過確認
  - "security-cleared"  # セキュリティチェック完了

# 自動ブロックラベル
blocking_labels:
  - "needs-review"      # レビュー必要
  - "breaking-change"   # 破壊的変更
  - "security-risk"     # セキュリティリスク
```

### 1.3 マージ権限制御
```json
{
  "branch_protection": {
    "required_status_checks": {
      "strict": true,
      "contexts": ["user-approval", "automated-tests"]
    },
    "enforce_admins": true,
    "required_pull_request_reviews": {
      "required_approving_review_count": 1,
      "dismiss_stale_reviews": true
    }
  }
}
```
</auto_merge_prevention>

## 2. 緊急停止メカニズム

<emergency_stop>
### 2.1 即座停止手順
```
🚨 緊急停止方法 🚨

1. Esc キーを2回押す           → 実行中処理の即座停止
2. Ctrl+C                    → コマンド実行の強制中断
3. /stop                     → Claude応答の停止
4. /cancel-pr                → PR作成/更新の中止
5. GitHub UI → "Close PR"    → 作成済みPRの無効化
```

### 2.2 自動停止トリガー
```yaml
# 以下の条件で自動停止
auto_stop_triggers:
  - file_deletion_count: 10    # 10ファイル以上の削除
  - large_file_creation: 50MB  # 大容量ファイル作成
  - system_file_modification   # システムファイル変更
  - credential_exposure        # 認証情報の露出検出
  - infinite_loop_detection    # 無限ループ検出
```

### 2.3 ロールバック機能
```bash
# 変更の自動ロールバック
rollback_commands:
  - "git reset --hard HEAD~1"
  - "git clean -fd"
  - "npm ci"  # package-lock.json復元
  - "git stash pop"  # 退避済み変更の復元
```
</emergency_stop>

## 3. PR作成時の安全チェック

<pr_safety_checks>
### 3.1 事前検証項目
```yaml
pre_pr_checks:
  - syntax_validation      # 構文エラーチェック
  - dependency_audit       # 依存関係脆弱性
  - secret_scanning        # 機密情報検出
  - test_execution         # テスト実行
  - lint_validation        # コード品質チェック
  - license_compliance     # ライセンス適合性
```

### 3.2 PRテンプレート
```markdown
## 🔍 変更内容の確認
- [ ] 変更内容を理解し、意図した動作であることを確認した
- [ ] テストが全て通過することを確認した  
- [ ] セキュリティリスクがないことを確認した
- [ ] 破壊的変更がある場合、適切に文書化した

## 🚨 緊急時対応
問題がある場合は以下の手順で即座に停止：
1. Esc×2 → 実行停止
2. このPRをクローズ
3. `git reset --hard HEAD~1` でロールバック

## 🤖 Claude生成コード
このPRはClaude Codeによって生成されました。
- 生成日時: {{TIMESTAMP}}
- モデル: {{MODEL_NAME}}
- セッションID: {{SESSION_ID}}
```

### 3.3 自動コメント挿入
```javascript
// Claude生成コードの自動識別
function addClaudeGeneratedComment(file) {
  const header = `
  /* 
   * ⚠️ Claude生成コード
   * 生成日時: ${new Date().toISOString()}
   * レビュー必須
   */
  `;
  return header + file.content;
}
```
</pr_safety_checks>

## 4. 監視・アラート設定

<monitoring_alerts>
### 4.1 リアルタイム監視
```yaml
monitoring:
  - pr_creation_rate: 5/hour    # 1時間に5個以上のPR作成で警告
  - file_change_volume: 100     # 100ファイル以上の変更で警告
  - commit_size: 10MB           # 大容量コミットの検出
  - force_push_detection        # 強制プッシュの監視
```

### 4.2 通知設定
```json
{
  "notifications": {
    "slack_webhook": "{{SLACK_WEBHOOK_URL}}",
    "email_alerts": "{{EMAIL_ADDRESS}}",
    "triggers": [
      "large_pr_created",
      "security_risk_detected", 
      "auto_merge_attempted",
      "emergency_stop_triggered"
    ]
  }
}
```

### 4.3 ダッシュボード
```
📊 Claude Code Safety Dashboard

🔴 アクティブアラート: 0
🟡 要注意PR: 2
🟢 正常稼働時間: 99.9%

最近のアクティビティ:
- 14:32 PR #123 作成 (user-approved待ち)
- 14:15 セキュリティスキャン完了
- 14:10 テスト実行: 全て通過
```
</monitoring_alerts>

## 5. チーム・組織での運用

<team_operations>
### 5.1 権限レベル設定
```yaml
user_permissions:
  admin:
    - bypass_approval      # 承認バイパス可能
    - emergency_override   # 緊急時上書き可能
    - settings_modify      # 設定変更可能
  
  developer:
    - create_pr           # PR作成可能
    - approve_own_pr      # 自分のPR承認可能
    - view_logs          # ログ閲覧可能
  
  viewer:
    - view_pr            # PR閲覧のみ
    - view_basic_logs    # 基本ログ閲覧のみ
```

### 5.2 レビュー体制
```
🔄 推奨レビューフロー

1. Claude作成 → PR自動作成
2. 開発者レビュー → 技術的妥当性確認
3. セキュリティチェック → 脆弱性検証
4. ユーザー承認 → 最終承認
5. マージ実行 → 本番反映
```

### 5.3 教育・トレーニング
- 新メンバー向け安全操作ガイド
- 緊急時対応手順の定期訓練
- Claude Code使用方法のベストプラクティス共有
- インシデント事例の振り返り学習
</team_operations>