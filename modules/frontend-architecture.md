# 🎨 フロントエンドアーキテクチャ規約

<module_info>
このモジュールは、フロントエンド開発の規約を定義します。
保守性と開発効率を重視した、実用的なアーキテクチャを推進します。
</module_info>

## 📐 基本規約

### フレームワーク選定

```yaml
デフォルト: React（最も広く使われ、LLMもよく知っている）
代替案:
  Vue.js: よりシンプルさを求める場合
  Vanilla JS: 極小規模または特殊要件
```

### プロジェクト構造

```bash
src/
├── components/     # UIコンポーネント
│   ├── common/    # 共通コンポーネント
│   └── features/  # 機能別コンポーネント
├── hooks/         # カスタムフック
├── services/      # API通信
├── utils/         # ユーティリティ
├── types/         # 型定義
└── styles/        # グローバルスタイル
```

## 🎛️ 状態管理

```yaml
ローカル状態: useState/useReducer
グローバル状態:
  小規模: Context API
  中規模: Zustand（シンプルで学習コストが低い）
  大規模: Redux Toolkit（必要性が明確な場合のみ）
```

## 🎨 スタイリング

```yaml
デフォルト: CSS Modules（シンプルで確実）
代替案:
  Tailwind CSS: ユーティリティファースト開発
  styled-components: CSS-in-JS が必要な場合
  
命名規約: BEM準拠
```

## 📦 コンポーネント設計

### 基本原則

```yaml
- 単一責任の原則
- Props の明確な型定義
- 適切なメモ化（React.memo、useMemo、useCallback）
- アクセシビリティの考慮
```

### コンポーネント例

```typescript
// components/common/Button.tsx
interface ButtonProps {
  variant?: 'primary' | 'secondary';
  size?: 'small' | 'medium' | 'large';
  disabled?: boolean;
  onClick?: () => void;
  children: React.ReactNode;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'medium',
  disabled = false,
  onClick,
  children,
}) => {
  return (
    <button
      className={`btn btn--${variant} btn--${size}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
};
```

## 🚀 パフォーマンス最適化

```yaml
必須対応:
  - コード分割（React.lazy）
  - 画像の遅延読み込み
  - 適切なメモ化
  
計測ツール:
  - React DevTools Profiler
  - Lighthouse
  - Web Vitals
```

## 📱 レスポンシブデザイン

```yaml
ブレークポイント:
  mobile: 0-767px
  tablet: 768px-1023px
  desktop: 1024px以上
  
モバイルファースト開発を推奨
```

## 🧪 テスト戦略

```yaml
単体テスト: Jest + React Testing Library
統合テスト: 重要なユーザーフローのみ
E2Eテスト: Playwright（クリティカルパスのみ）

カバレッジ目標:
  ユーティリティ: 90%以上
  コンポーネント: 70%以上
  全体: 60%以上
```

## 🔧 ビルド設定

```yaml
開発サーバー: Vite（高速）
本番ビルド:
  - Tree Shaking
  - コード圧縮
  - ソースマップ（エラー追跡用）
  
バンドルサイズ目標:
  初期ロード: 200KB以下
  遅延ロード: チャンクごとに50KB以下
```