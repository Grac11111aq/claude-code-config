# UI/UXに関する一般原則

## 1. 基本方針
<ui_ux_principles>
- ユーザーインターフェース（GUIおよびCLI）の設計においては、常にユーザーにとっての一貫性と分かりやすさを重視する
- GUIでは、統一されたデザインパターンやUIコンポーネントの使用を推奨し、アクセシビリティの確保を必須とする
- CLIでは、直感的で予測可能なコマンド体系、および充実したヘルプメッセージ（使用例を含む）の提供を必須とする
- これらの原則に基づいたUI要素の提案や、アクセシビリティに配慮したコード生成を行う
</ui_ux_principles>

## 2. アクセシビリティ基準

<accessibility_requirements>
**必須基準**: WCAG 2.1 Level AA準拠

**努力目標**: 開発効率や他のUI実装に問題がない場合はWCAG 2.1 Level AAAを目指す

**具体的な実装要件**:

| カテゴリ | Level AA 必須要件 | Level AAA 努力目標 |
|---------|-----------------|------------------|
| **知覚可能** | ・色のコントラスト比 4.5:1以上（通常テキスト）<br>・画像に代替テキスト<br>・動画に字幕 | ・コントラスト比 7:1以上<br>・手話通訳<br>・音声解説 |
| **操作可能** | ・キーボードのみで全機能操作可能<br>・フォーカス表示<br>・時間制限の調整可能 | ・セクション見出しの提供<br>・リンクの目的を明確化 |
| **理解可能** | ・エラーの特定と修正案<br>・一貫したナビゲーション<br>・フォームラベル | ・文脈依存ヘルプ<br>・略語の説明<br>・読解レベル配慮 |
| **堅牢** | ・支援技術との互換性<br>・有効なHTML/ARIA | ・すべての機能で完全互換 |
</accessibility_requirements>

## 3. 非機能要件の全般的な優先順位

<non_functional_priorities>
以下の順序で非機能要件を重視する（下に行くほど要求度が相対的に低い）。

1. **（要件を満たす）パフォーマンス**
2. **アクセシビリティ**（最大限重視）
3. **国際性**（最大限重視）
4. **メンテナンス性**（最大限重視）
5. **運用性**（最大限重視）
6. **開発効率**
7. **セキュリティ**
</non_functional_priorities>