# 🎨 UXリサーチ・デザインシステム規約

<module_info>
このモジュールは、UXリサーチとデザインシステムの規約を定義します。
ユーザー中心設計により、使いやすく一貫性のある体験を提供します。
</module_info>

## 📐 基本方針

### ユーザー中心設計

```yaml
基本原則:
  - ユーザーニーズ最優先
  - データに基づく意思決定
  - 継続的な検証・改善
  - アクセシビリティの確保

設計プロセス:
  1. ユーザーリサーチ
  2. ペルソナ・ジャーニー設計
  3. 情報アーキテクチャ
  4. プロトタイピング・テスト
  5. 実装・検証
```

### デザインシステム思想

```yaml
一貫性の確保:
  - 統一されたビジュアル言語
  - 再利用可能なコンポーネント
  - 標準化されたパターン
  - 明確なガイドライン

効率性の向上:
  - 開発速度の向上
  - デザイン品質の均一化
  - メンテナンス性の改善
  - チーム間の認識統一
```

## 🔍 UXリサーチ手法

### ユーザーインタビュー

```yaml
準備フェーズ:
  目的設定:
    - 何を知りたいか明確化
    - 仮説の設定
    - 成功指標の定義
  
  参加者選定:
    - ターゲットユーザー層
    - 5-8名程度（質的調査）
    - 多様性の確保

実施フェーズ:
  インタビュー構成:
    - アイスブレイク（5分）
    - 現状理解（15分）
    - 深掘り質問（30分）
    - まとめ・次ステップ（10分）
  
  質問設計:
    - オープンクエスチョン中心
    - 行動の背景を探る
    - 感情・体験を重視
    - 具体的な事例を求める

分析フェーズ:
  - 発言録の作成
  - アフィニティダイアグラム
  - インサイトの抽出
  - ペルソナ・ジャーニーへの反映
```

### ユーザビリティテスト

```yaml
計画段階:
  テスト設計:
    - タスクシナリオ作成
    - 成功基準の設定
    - 測定指標の定義
    - プロトタイプの準備
  
  参加者: 5-8名（量的評価は30名以上）
  環境: リモート/対面の選択

実施段階:
  観察項目:
    - タスク完了率
    - 完了時間
    - エラー発生箇所
    - ユーザーの発言・行動
  
  測定方法:
    - スクリーン録画
    - 思考発話法
    - アイトラッキング（必要に応じて）
    - 事後インタビュー

分析・改善:
  - 問題の優先順位付け
  - 改善案の検討
  - プロトタイプ修正
  - 再テストの実施
```

### LLM活用UXリサーチ

```yaml
ペルソナ生成プロンプト:
  "以下の市場調査データを基に、詳細なユーザーペルソナを3つ作成してください：
   
   [市場調査データ]
   
   各ペルソナについて以下を含めてください：
   - 基本情報（年齢、職業、家族構成）
   - 行動パターン・習慣
   - ニーズ・課題・ペインポイント
   - 技術リテラシー・使用デバイス
   - モチベーション・価値観
   - 典型的な一日の流れ
   
   実在しそうな具体的な人物として描写してください。"

ユーザージャーニー分析プロンプト:
  "以下のペルソナがサービスを利用する際のジャーニーマップを作成してください：
   
   ペルソナ: [ペルソナ詳細]
   サービス: [サービス概要]
   
   ジャーニーの各段階で以下を分析してください：
   - ユーザーの行動・思考・感情
   - タッチポイント・チャネル
   - ペインポイント・課題
   - 改善機会・施策案
   
   データに基づく根拠も併せて提示してください。"
```

## 👥 ペルソナ・ジャーニー設計

### ペルソナ作成

```yaml
基本情報:
  - 名前・年齢・職業
  - 家族構成・居住地
  - 年収・ライフスタイル

行動特性:
  - 技術との関わり方
  - 情報収集方法
  - 意思決定プロセス
  - 時間の使い方

心理特性:
  - 価値観・信念
  - モチベーション
  - 不安・懸念
  - 目標・願望

プロダクト関連:
  - 利用シーン・頻度
  - 期待する価値
  - 障壁・課題
  - 代替手段

ペルソナ例:
  名前: 田中恵美（32歳）
  職業: マーケティングマネージャー
  特徴: 効率重視、データ分析好き、時間に追われがち
  ニーズ: 業務効率化ツール、直感的UI、モバイル対応
  ペインポイント: 複雑な操作、レスポンスの遅さ、学習コスト
```

### カスタマージャーニーマップ

```yaml
段階別設計:
  認知段階:
    - きっかけ・動機
    - 情報源・チャネル
    - 第一印象・興味度
  
  検討段階:
    - 比較・評価軸
    - 情報収集行動
    - 意思決定要因
  
  利用段階:
    - 初回体験・オンボーディング
    - 継続利用・習慣化
    - 満足度・ロイヤルティ
  
  推奨段階:
    - 共有・口コミ行動
    - 拡散・紹介意欲
    - コミュニティ参加

各段階での分析項目:
  - タッチポイント
  - ユーザーの行動・思考・感情
  - ペインポイント・課題
  - 改善機会・施策
```

## 🏗️ 情報アーキテクチャ

### サイトマップ設計

```yaml
階層構造:
  第1階層: 主要機能・カテゴリ
  第2階層: サブ機能・詳細カテゴリ
  第3階層: 具体的なページ・コンテンツ

設計原則:
  - 論理的なグルーピング
  - 直感的なラベリング
  - 適切な階層の深さ（3階層まで推奨）
  - クロスリンクの考慮

カードソーティング:
  - オープンカードソーティング（自由分類）
  - クローズドカードソーティング（既定分類）
  - ハイブリッド（部分的に既定）
  - オンラインツール活用（OptimalSort等）
```

### ナビゲーション設計

```yaml
グローバルナビゲーション:
  - 主要機能への常時アクセス
  - 5±2の法則（7項目以内）
  - 明確で簡潔なラベル
  - 現在位置の明示

ローカルナビゲーション:
  - セクション内の詳細項目
  - 階層構造の表現
  - パンくずナビの設置

検索機能:
  - 予測検索・オートコンプリート
  - ファセット検索（フィルタリング）
  - 検索結果の最適化
  - 検索ログの分析・改善
```

## 🎨 デザインシステム構築

### デザイントークン

```yaml
色彩システム:
  Primary Colors:
    - primary-50: #f0f9ff
    - primary-500: #3b82f6  # メインブランドカラー
    - primary-900: #1e3a8a
  
  Semantic Colors:
    - success: #10b981
    - warning: #f59e0b
    - error: #ef4444
    - info: #3b82f6
  
  Neutral Colors:
    - gray-50: #f9fafb
    - gray-500: #6b7280
    - gray-900: #111827

タイポグラフィ:
  Font Family:
    - primary: 'Inter', 'Hiragino Sans', 'Yu Gothic', sans-serif
    - monospace: 'JetBrains Mono', 'Consolas', monospace
  
  Font Sizes:
    - xs: 12px
    - sm: 14px
    - base: 16px
    - lg: 18px
    - xl: 20px
    - 2xl: 24px
    - 3xl: 30px

間隔・サイズ:
  Spacing Scale:
    - 4px, 8px, 12px, 16px, 20px, 24px, 32px, 40px, 48px, 64px
  
  Border Radius:
    - sm: 4px
    - md: 8px
    - lg: 12px
    - full: 9999px
```

### コンポーネントライブラリ

```typescript
// ボタンコンポーネント例
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'ghost' | 'danger';
  size: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  loading?: boolean;
  icon?: React.ReactNode;
  children: React.ReactNode;
  onClick?: () => void;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  disabled = false,
  loading = false,
  icon,
  children,
  onClick
}) => {
  const baseClasses = 'inline-flex items-center justify-center font-medium rounded-md transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2';
  
  const variantClasses = {
    primary: 'bg-primary-500 text-white hover:bg-primary-600 focus:ring-primary-500',
    secondary: 'bg-gray-100 text-gray-900 hover:bg-gray-200 focus:ring-gray-500',
    ghost: 'bg-transparent text-gray-700 hover:bg-gray-100 focus:ring-gray-500',
    danger: 'bg-red-500 text-white hover:bg-red-600 focus:ring-red-500'
  };
  
  const sizeClasses = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg'
  };
  
  return (
    <button
      className={`${baseClasses} ${variantClasses[variant]} ${sizeClasses[size]} ${disabled ? 'opacity-50 cursor-not-allowed' : ''}`}
      disabled={disabled || loading}
      onClick={onClick}
    >
      {loading && <Spinner className="mr-2" />}
      {icon && <span className="mr-2">{icon}</span>}
      {children}
    </button>
  );
};
```

### スタイルガイド

```yaml
書式・ガイドライン:
  Voice & Tone:
    - フレンドリー・親しみやすい
    - 専門的だが親近感のある
    - 簡潔・明確
    - ポジティブ・建設的
  
  ライティング:
    - 敬語の適切な使用
    - 専門用語の説明
    - アクション指向の文章
    - エラーメッセージの配慮
  
  アイコン:
    - 一貫したスタイル（線幅・角の丸み）
    - 意味の明確性
    - 文化的配慮
    - サイズ展開（16px, 24px, 32px）

実装ガイド:
  CSS Variables:
    :root {
      --color-primary: #3b82f6;
      --color-success: #10b981;
      --font-family-sans: 'Inter', sans-serif;
      --spacing-4: 1rem;
      --border-radius-md: 0.5rem;
    }
  
  Component Usage:
    <!-- 良い例 -->
    <Button variant="primary" size="md">
      保存する
    </Button>
    
    <!-- 悪い例 -->
    <button style="background: blue; padding: 10px;">
      保存する
    </button>
```

## ♿ アクセシビリティ設計

### WCAG 2.1 AA準拠

```yaml
知覚可能性:
  - 代替テキストの提供
  - 色以外の情報伝達手段
  - 十分なコントラスト比（4.5:1以上）
  - テキストの拡大対応（200%まで）

操作可能性:
  - キーボード操作対応
  - 点滅・自動再生の制御
  - 適切なフォーカス管理
  - 十分なクリック領域（44px以上）

理解可能性:
  - 明確で一貫したナビゲーション
  - エラーの特定・修正支援
  - 予測可能な動作
  - 読みやすいテキスト

堅牢性:
  - セマンティックHTML
  - ARIA属性の適切な使用
  - スクリーンリーダー対応
  - 多様なブラウザ・デバイス対応
```

### 実装例

```typescript
// アクセシブルなフォームコンポーネント
interface FormFieldProps {
  label: string;
  required?: boolean;
  error?: string;
  helpText?: string;
  children: React.ReactNode;
}

export const FormField: React.FC<FormFieldProps> = ({
  label,
  required = false,
  error,
  helpText,
  children
}) => {
  const fieldId = useId();
  const errorId = error ? `${fieldId}-error` : undefined;
  const helpId = helpText ? `${fieldId}-help` : undefined;
  
  return (
    <div className="form-field">
      <label 
        htmlFor={fieldId}
        className="block text-sm font-medium text-gray-700"
      >
        {label}
        {required && <span aria-label="必須" className="text-red-500 ml-1">*</span>}
      </label>
      
      {React.cloneElement(children as React.ReactElement, {
        id: fieldId,
        'aria-describedby': [helpId, errorId].filter(Boolean).join(' '),
        'aria-invalid': !!error
      })}
      
      {helpText && (
        <p id={helpId} className="mt-1 text-sm text-gray-500">
          {helpText}
        </p>
      )}
      
      {error && (
        <p id={errorId} className="mt-1 text-sm text-red-600" role="alert">
          {error}
        </p>
      )}
    </div>
  );
};
```

## 📱 レスポンシブ・モバイル設計

### ブレークポイント戦略

```yaml
デバイス分類:
  Mobile: 320px - 767px
  Tablet: 768px - 1023px
  Desktop: 1024px - 1439px
  Large Desktop: 1440px以上

設計アプローチ:
  Mobile First: 小さい画面から設計開始
  Progressive Enhancement: 段階的な機能強化
  Touch Friendly: タッチ操作に最適化
  Performance First: モバイルパフォーマンス優先

実装例:
  .container {
    padding: 1rem;
    max-width: 100%;
  }
  
  @media (min-width: 768px) {
    .container {
      padding: 2rem;
      max-width: 1200px;
      margin: 0 auto;
    }
  }
```

## 📊 デザインメトリクス・評価

### UXメトリクス

```yaml
タスク効率:
  - タスク完了率
  - タスク完了時間
  - クリック数・ステップ数
  - エラー発生率

ユーザビリティ:
  - System Usability Scale (SUS)
  - Net Promoter Score (NPS)
  - Customer Satisfaction (CSAT)
  - Customer Effort Score (CES)

エンゲージメント:
  - セッション時間
  - ページビュー/セッション
  - 直帰率
  - リピート率

ビジネス成果:
  - コンバージョン率
  - 離脱率
  - カスタマーサポート問い合わせ数
  - ユーザー満足度向上率
```

## 📋 UX/デザインチェックリスト

```yaml
リサーチ段階:
  - [ ] ユーザーインタビュー実施
  - [ ] ペルソナ・ジャーニー作成
  - [ ] 競合分析・ベンチマーク
  - [ ] 情報アーキテクチャ設計

設計段階:
  - [ ] ワイヤーフレーム作成
  - [ ] プロトタイプ作成・テスト
  - [ ] デザインシステム準拠
  - [ ] アクセシビリティ考慮

実装段階:
  - [ ] デザインレビュー実施
  - [ ] ユーザビリティテスト
  - [ ] 各デバイスでの動作確認
  - [ ] パフォーマンス測定

運用段階:
  - [ ] ユーザー行動分析
  - [ ] A/Bテスト実施
  - [ ] フィードバック収集・分析
  - [ ] 継続的改善実施
```