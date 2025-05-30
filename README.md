# Claude Code グローバル設定

このリポジトリは、Claude Codeの全プロジェクト共通設定を管理します。

## 📁 ディレクトリ構成

```
.claude/
├── CLAUDE.md              # メイン設定ファイル（自動読み込み）
├── modules/              # 各種規約・ガイドラインのモジュール
│   ├── core-principles.md
│   ├── coding-standards.md
│   ├── autonomous-development-framework.md  # 🆕 自律的開発フレームワーク
│   └── ...
├── templates/            # コメントテンプレート集
├── doc/                 # 調査レポート・参考資料
└── README.md            # このファイル
```

## 🚀 使い方

1. このリポジトリをクローン
```bash
git clone https://github.com/Grac11111aq/claude-code-config.git ~/.claude
```

2. Claude Codeが自動的に`~/.claude/CLAUDE.md`を読み込みます

## 📋 主要モジュール

### 🤖 自律的開発フレームワーク
- [modules/autonomous-development-framework.md](modules/autonomous-development-framework.md)
- AI/LLMが自律的に判断できる範囲と相談が必要な事項を定義
- 技術選択、リファクタリング、品質基準などの判断基準を明確化

### 🏗️ 基本原則と開発姿勢
- [modules/core-principles.md](modules/core-principles.md)
- 開発における基本的な考え方と姿勢

### 📋 コーディング標準
- [modules/coding-standards.md](modules/coding-standards.md)
- コーディング規約と品質管理基準

## 🔄 カスタマイズ

プロジェクト固有の設定は、各プロジェクトのルートディレクトリに`CLAUDE.md`を作成してください。

## 📝 ライセンス

このリポジトリは個人的な開発方針を記載したものです。