# Python コメントテンプレート

## 関数・メソッドコメント（docstring）

### 基本テンプレート
```python
def function_name(param1: str, param2: int = 0) -> bool:
    """
    関数の機能を簡潔に説明します。
    複数行になる場合は、詳細な説明を追加できます。
    
    Args:
        param1: パラメータの説明
        param2: オプションパラメータの説明（デフォルト: 0）
    
    Returns:
        返り値の説明
    
    Raises:
        ValueError: 無効な値が渡された場合
        TypeError: 型が不正な場合
    
    Example:
        >>> result = function_name("test", 5)
        >>> print(result)
        True
    """
    # 実装
    pass
```

### 詳細な例
```python
def calculate_sales_tax_included_price(item_price: float, quantity: int, tax_rate: float = 0.1) -> float:
    """
    商品の税込み合計金額を計算します。
    
    消費税は小数点以下切り上げで計算し、最終的な合計金額を返します。
    負の値や無効な値に対してはバリデーションを行います。
    
    Args:
        item_price: 商品の単価（税抜き）。0以上の数値である必要があります。
        quantity: 購入数量。1以上の整数である必要があります。
        tax_rate: 消費税率（デフォルト: 10%）。0.0から1.0の範囲で指定します。
    
    Returns:
        税込みの合計金額。小数点以下は切り上げされます。
    
    Raises:
        ValueError: 価格や数量が負の値、または税率が範囲外の場合
        TypeError: 引数の型が不正な場合
    
    Example:
        >>> calculate_sales_tax_included_price(1000, 2, 0.08)
        2160.0
        
        >>> calculate_sales_tax_included_price(500, 1)
        550.0
    """
    # バリデーション
    if item_price < 0:
        raise ValueError("商品価格は0以上である必要があります")
    if quantity < 1:
        raise ValueError("数量は1以上である必要があります")
    if not (0 <= tax_rate <= 1):
        raise ValueError("税率は0.0から1.0の範囲で指定してください")
    
    # 税抜き小計を計算
    subtotal = item_price * quantity
    
    # 消費税を計算（切り上げ）
    import math
    tax = math.ceil(subtotal * tax_rate)
    
    # 税込み合計金額
    total = subtotal + tax
    
    return total
```

## クラスコメント

### クラステンプレート
```python
class UserManager:
    """
    ユーザー管理を行うためのクラスです。
    
    データベースへの接続とユーザー情報の CRUD 操作を提供します。
    セッション管理と認証機能も含まれています。
    
    Attributes:
        database: データベース接続インスタンス
        session_timeout: セッションのタイムアウト時間（秒）
        _active_sessions: アクティブセッションの辞書
    
    Example:
        >>> db = Database("sqlite:///users.db")
        >>> user_manager = UserManager(db)
        >>> user = user_manager.create_user("太郎", "taro@example.com")
        >>> print(user.name)
        太郎
    """
    
    def __init__(self, database: 'Database', session_timeout: int = 3600):
        """
        UserManager のインスタンスを作成します。
        
        Args:
            database: データベース接続インスタンス
            session_timeout: セッションタイムアウト時間（秒、デフォルト: 3600）
        
        Raises:
            ConnectionError: データベースへの接続に失敗した場合
            TypeError: 無効なデータベースインスタンスが渡された場合
        """
        self.database = database
        self.session_timeout = session_timeout
        self._active_sessions = {}
```

## 非同期関数

### async/await テンプレート
```python
async def fetch_user_data(user_id: str, include_preferences: bool = False) -> dict:
    """
    ユーザーデータを非同期で取得します。
    
    指定されたユーザーIDに基づいて、データベースからユーザー情報を
    非同期で取得します。オプションでユーザー設定も含めることができます。
    
    Args:
        user_id: 取得対象のユーザーID
        include_preferences: ユーザー設定も含めるかどうか（デフォルト: False）
    
    Returns:
        ユーザー情報を含む辞書。ユーザーが見つからない場合は空の辞書。
        
        辞書の構造:
        {
            'id': str,
            'name': str,
            'email': str,
            'created_at': datetime,
            'preferences': dict (include_preferences=True の場合のみ)
        }
    
    Raises:
        ConnectionError: データベース接続エラー
        TimeoutError: リクエストがタイムアウトした場合
        ValueError: 無効なユーザーIDが指定された場合
    
    Example:
        >>> user_data = await fetch_user_data("user123", True)
        >>> print(user_data['name'])
        山田太郎
    """
    # 実装
    pass
```

## データクラス・Pydanticモデル

### dataclass テンプレート
```python
from dataclasses import dataclass
from datetime import datetime
from typing import Optional

@dataclass
class User:
    """
    ユーザー情報を表すデータクラスです。
    
    Attributes:
        id: ユーザーの一意識別子
        name: ユーザーの表示名
        email: ユーザーのメールアドレス
        created_at: アカウント作成日時
        last_login: 最終ログイン日時（未ログインの場合はNone）
        is_active: アカウントがアクティブかどうか
    
    Example:
        >>> user = User(
        ...     id="user123",
        ...     name="山田太郎", 
        ...     email="yamada@example.com",
        ...     created_at=datetime.now()
        ... )
        >>> print(user.name)
        山田太郎
    """
    id: str
    name: str
    email: str
    created_at: datetime
    last_login: Optional[datetime] = None
    is_active: bool = True
    
    def __post_init__(self):
        """
        インスタンス作成後の初期化処理を行います。
        
        メールアドレスの形式チェックやその他のバリデーションを実行します。
        """
        # バリデーション処理
        if "@" not in self.email:
            raise ValueError("無効なメールアドレス形式です")
```

## インラインコメント

### 複雑なロジック
```python
def analyze_sales_data(sales_data: list) -> dict:
    """売上データを分析して統計情報を返します。"""
    
    # データの前処理：無効な値を除外
    valid_sales = [sale for sale in sales_data if sale['amount'] > 0]
    
    # 月別売上の計算
    monthly_sales = {}
    for sale in valid_sales:
        # 売上日から年月を抽出
        year_month = sale['date'].strftime('%Y-%m')
        
        # 月別売上に加算（初回は0から開始）
        monthly_sales[year_month] = monthly_sales.get(year_month, 0) + sale['amount']
    
    # 統計値の計算
    total_amount = sum(sale['amount'] for sale in valid_sales)
    average_amount = total_amount / len(valid_sales) if valid_sales else 0
    
    # TODO: 季節性分析機能を追加予定
    # FIXME: 通貨換算処理でのエラーハンドリングが必要
    
    return {
        'total_sales': total_amount,
        'average_sale': average_amount,
        'monthly_breakdown': monthly_sales,
        'transaction_count': len(valid_sales)
    }
```

### 型ヒント付きコメント
```python
from typing import List, Dict, Optional, Union, Callable

def process_data(
    data: List[Dict[str, Union[int, str]]], 
    filter_func: Optional[Callable[[dict], bool]] = None,
    transform_func: Optional[Callable[[dict], dict]] = None
) -> List[dict]:
    """
    データリストを処理します。
    
    Args:
        data: 処理対象のデータリスト
        filter_func: フィルタリング関数（オプション）
        transform_func: 変換関数（オプション）
    
    Returns:
        処理済みのデータリスト
    """
    # フィルタリング処理
    if filter_func:
        # カスタムフィルタが指定されている場合
        filtered_data = [item for item in data if filter_func(item)]
    else:
        # フィルタなしの場合は全データを使用
        filtered_data = data
    
    # 変換処理
    if transform_func:
        # カスタム変換が指定されている場合
        result = [transform_func(item) for item in filtered_data]
    else:
        # 変換なしの場合はそのまま返す
        result = filtered_data
    
    return result
```

## 例外処理とログ

### 包括的エラーハンドリング
```python
import logging
from typing import Optional

logger = logging.getLogger(__name__)

def safe_divide(dividend: float, divisor: float) -> Optional[float]:
    """
    安全な除算を実行します。
    
    ゼロ除算エラーやその他の例外を適切にハンドリングし、
    ログに記録します。
    
    Args:
        dividend: 被除数
        divisor: 除数
    
    Returns:
        除算結果。エラーの場合はNone。
    
    Example:
        >>> result = safe_divide(10, 2)
        >>> print(result)
        5.0
        
        >>> result = safe_divide(10, 0)
        >>> print(result)
        None
    """
    try:
        # 基本的なバリデーション
        if not isinstance(dividend, (int, float)) or not isinstance(divisor, (int, float)):
            raise TypeError("数値型の引数が必要です")
        
        # ゼロ除算チェック
        if divisor == 0:
            logger.warning(f"ゼロ除算が発生しました: {dividend} / {divisor}")
            return None
        
        # 除算実行
        result = dividend / divisor
        logger.debug(f"除算成功: {dividend} / {divisor} = {result}")
        
        return result
        
    except TypeError as e:
        # 型エラーの場合
        logger.error(f"型エラー: {e}")
        return None
        
    except Exception as e:
        # その他の予期しないエラー
        logger.error(f"予期しないエラー: {e}")
        return None
```