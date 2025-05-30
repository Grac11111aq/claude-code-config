# JavaScript/TypeScript コメントテンプレート

## 関数・メソッドコメント

### 基本テンプレート
```javascript
/**
 * 関数の機能を簡潔に説明します。
 * 複数行になる場合は、詳細な説明を追加できます。
 * 
 * @param {型} paramName - パラメータの説明
 * @param {型} [optionalParam] - オプションパラメータの説明
 * @returns {Promise<型>|型} 返り値の説明
 * @throws {Error} エラーが発生する条件
 * 
 * @example
 * // 使用例
 * const result = await functionName(param1, param2);
 */
function functionName(paramName, optionalParam) {
  // 実装
}
```

### 非同期関数テンプレート
```javascript
/**
 * 指定されたユーザーIDに基づいて、データベースからユーザー情報を非同期で取得します。
 * ユーザーが見つからない場合はnullを返します。
 * 
 * @param {string} userId - 取得対象のユーザーのID。空文字列やnullであってはならない。
 * @param {Object} [options] - 追加オプション
 * @param {boolean} [options.includeInactive=false] - 非アクティブユーザーも含めるか
 * @returns {Promise<UserObject|null>} ユーザー情報を含むオブジェクト、またはnull。
 * @throws {Error} データベース接続エラーや予期せぬエラーが発生した場合。
 * 
 * @example
 * const user = await getUserById('12345');
 * if (user) {
 *   console.log(`ユーザー名: ${user.name}`);
 * }
 */
async function getUserById(userId, options = {}) {
  // 実装
}
```

## クラスコメント

### クラステンプレート
```javascript
/**
 * ユーザー管理を行うためのクラスです。
 * データベースへの接続とユーザー情報の CRUD 操作を提供します。
 * 
 * @class UserManager
 * @example
 * const userManager = new UserManager(database);
 * const user = await userManager.createUser({ name: '太郎', email: 'taro@example.com' });
 */
class UserManager {
  /**
   * UserManager のインスタンスを作成します。
   * 
   * @param {Database} database - データベース接続インスタンス
   * @throws {Error} 無効なデータベースインスタンスが渡された場合
   */
  constructor(database) {
    // 実装
  }
}
```

## TypeScript 固有テンプレート

### インターフェース・型定義
```typescript
/**
 * ユーザー情報を表すインターフェースです。
 * 
 * @interface User
 */
interface User {
  /** ユーザーの一意識別子 */
  id: string;
  
  /** ユーザーの表示名 */
  name: string;
  
  /** 
   * ユーザーのメールアドレス
   * @example "user@example.com"
   */
  email: string;
  
  /** 
   * アカウント作成日時
   * @default 現在の日時
   */
  createdAt: Date;
}
```

### ジェネリック関数
```typescript
/**
 * 配列の要素をフィルタリングし、新しい配列を返します。
 * 
 * @template T - 配列要素の型
 * @param {T[]} items - フィルタリング対象の配列
 * @param {(item: T) => boolean} predicate - フィルタリング条件関数
 * @returns {T[]} 条件に一致する要素のみを含む新しい配列
 * 
 * @example
 * const numbers = [1, 2, 3, 4, 5];
 * const evenNumbers = filterArray(numbers, n => n % 2 === 0);
 * // 結果: [2, 4]
 */
function filterArray<T>(items: T[], predicate: (item: T) => boolean): T[] {
  // 実装
}
```

## React コンポーネント

### 関数コンポーネント
```typescript
/**
 * ユーザー情報を表示するコンポーネントです。
 * ユーザーの基本情報とアバター画像を表示します。
 * 
 * @component
 * @param {Object} props - コンポーネントのプロパティ
 * @param {User} props.user - 表示するユーザー情報
 * @param {boolean} [props.showEmail=false] - メールアドレスを表示するか
 * @param {() => void} [props.onEdit] - 編集ボタンクリック時のコールバック
 * @returns {JSX.Element} ユーザー情報コンポーネント
 * 
 * @example
 * <UserProfile 
 *   user={userData} 
 *   showEmail={true}
 *   onEdit={() => handleEdit(userData.id)}
 * />
 */
const UserProfile: React.FC<UserProfileProps> = ({ 
  user, 
  showEmail = false, 
  onEdit 
}) => {
  // 実装
};
```

## インラインコメント

### ロジック説明
```javascript
function calculateTax(price, taxRate) {
  // 消費税を計算（小数点以下切り上げ）
  const tax = Math.ceil(price * taxRate);
  
  // 税込み価格を計算
  const totalPrice = price + tax;
  
  // TODO: 軽減税率への対応を追加予定
  // FIXME: 負の価格に対するバリデーションが必要
  
  return {
    basePrice: price,
    tax: tax,
    totalPrice: totalPrice
  };
}
```

### 複雑なアルゴリズム
```javascript
/**
 * クイックソートアルゴリズムを使用して配列をソートします。
 */
function quickSort(array) {
  // ベースケース: 配列の長さが1以下の場合はそのまま返す
  if (array.length <= 1) {
    return array;
  }
  
  // ピボット要素を選択（今回は中央の要素）
  const pivotIndex = Math.floor(array.length / 2);
  const pivot = array[pivotIndex];
  
  // ピボットより小さい要素、等しい要素、大きい要素に分割
  const left = [];
  const middle = [];
  const right = [];
  
  for (const element of array) {
    if (element < pivot) {
      left.push(element);
    } else if (element === pivot) {
      middle.push(element);
    } else {
      right.push(element);
    }
  }
  
  // 再帰的にソートして結合
  return [...quickSort(left), ...middle, ...quickSort(right)];
}
```