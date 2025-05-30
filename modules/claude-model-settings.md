# Claude 4 & Max プラン固有設定

## 1. モデル選択ポリシー

<model_selection_policy>
**推奨モデル**: `claude-sonnet-4` (フォールバック: `claude-opus-4`)

**使い分けガイドライン**:

| タスクタイプ | 推奨モデル | 理由 |
|------------|----------|------|
| **通常のコーディング** | Sonnet 4 | コスト効率とパフォーマンスのバランス |
| **複雑なアーキテクチャ設計** | Opus 4 | 長期的思考と深い分析が必要 |
| **大量ファイル生成** | Sonnet 4 | 十分な品質でコスト抑制 |
| **リファクタリング大規模** | Opus 4 | 全体を俯瞰した判断が必要 |
| **デバッグ・問題解決** | Sonnet 4 → Opus 4 | 段階的エスカレーション |

**コスト指標**:
- Sonnet 4: 約1/10のコスト、十分な精度
- Opus 4: 最高品質、長時間実行に強い
</model_selection_policy>

## 2. Extended-Thinking モード利用指針

<extended_thinking_guidelines>
**Max プラン限定機能**: Extended-Thinking は推論を優先してツール実行を抑制

**有効化基準**:
- ✅ 長大ファイル生成（1000行以上）
- ✅ 複雑なアルゴリズム設計
- ✅ アーキテクチャの根本的見直し
- ✅ 大規模リファクタリングの計画

**無効化基準**:
- ❌ 単純なバグ修正
- ❌ 小規模なコード変更
- ❌ ファイル読み込み中心のタスク
- ❌ 繰り返し作業

**制御方法**:
```
# Extended-Thinking有効化
/thinking on

# 通常モードに戻す
/thinking off
```

**注意点**:
- 推論時間が長くなるため、急ぎの作業には不向き
- ツール実行回数が制限されるため、探索的作業には向かない
</extended_thinking_guidelines>

## 3. プロジェクト・コンテキスト管理

<project_context_management>
**Max プランのプロジェクト機能活用**:

### 3.1 プロジェクト設定
```json
{
  "project_name": "{{PROJECT_NAME}}",
  "preferred_model": "claude-sonnet-4",
  "fallback_model": "claude-opus-4",
  "context_window": "200k",
  "auto_context_refresh": true
}
```

### 3.2 コンテキスト最適化
- **重要ファイル**: 自動でコンテキストに含める
- **履歴保持**: 直近20回の会話を保持
- **メモリ管理**: 不要な古いコンテキストを自動削除

### 3.3 セッション管理
- セッションごとにモデル選択を保持
- タスク履歴の自動記録
- プロジェクト固有の設定継承
</project_context_management>

## 4. 緊急停止・制御手順

<emergency_controls>
**即座停止方法**:
- **Esc キー2回**: 実行中タスクの強制停止
- **Ctrl+C**: コマンド実行の中断
- **/stop**: Claude の応答停止

**セーフモード**:
```
/safe-mode on
```
- ファイル変更を確認モードに
- 危険なコマンドを自動ブロック
- 全ての変更前に明示的承認を要求

**リセット手順**:
```
/reset
/clear-context
/restart-session
```
</emergency_controls>

## 5. 品質・安全性設定

<quality_safety_settings>
### 5.1 自動レビュー
- **静的解析**: コード生成後に自動実行
- **セキュリティチェック**: 危険なパターンを検出
- **ベストプラクティス検証**: 言語固有のルールを適用

### 5.2 確認プロンプト
```
# 重要な変更前に確認
confirm_before_execution: true

# ファイル削除・移動時の確認
confirm_destructive_operations: true

# 本番環境への操作確認
confirm_production_changes: true
```

### 5.3 ログ・監査
- 全てのファイル変更をログ記録
- コマンド実行履歴の保持
- エラー・警告の詳細記録
</quality_safety_settings>