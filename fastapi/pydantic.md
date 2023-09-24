# pydanticについて
## Pydanticとは
Pythonの型アノテーションを利用して、実行時における型ヒントを提供したり、データのバリデーション時のエラー設定を簡単に提供してくれるライブラリ。<br />


SQLAlchemyのデータベースモデルを定義する際に役立つとのこと。


### モデル
定義例は以下のとおり。


```
from datetime import datetime
from typing import List
from pydantic import BaseModel

class User(BaseModel):
  id: int
  name: str
  friendIds: List[int] = []
  create_at: datetime

```

BaseModelを継承してモデルの型を定義することが可能。

### BaseModelとは
PydanticのBaseModelを継承したクラスでデータを定義することでクラス変数に定義した型ヒントとインスタンス作成時に指定された値の型を比較し、正しい型なのかどうかをバリデーションしてくれる。<br />
型ヒント以外にも、独自のバリデーションルールを定義することが可能<br />
以下実装例<br />


```
from pydantic import BaseModel


class Service(BaseModel):
    service_id: int
    name: str

    # 独自のバリデーションルール
    @validator("name")
    def name_has_service(cls, v):
        if "Service" not in v:
            raise ValueError("name must have 'Service'")
        return v

Service(service_id=1, name="Service A")
# > OK

Service(service_id="a", name="Service A")
# > ValidationError

Service(service_id=1, name="A")
# > ValidationError

```


### 型
型とは、Pythonの型ヒントを使って記述する。一般的なものは以下のとおり。


| 型         | 説明         | JSON schema type               |
|:-----------|:-------------|:-------------------------------|
| None       | Noneそのもの | null                           |
| bool       | 真偽値       | boolean                        |
| int        | 整数         | number                         |
| float      | 浮動小数点   | number                         |
| str        | 文字列       | string                         |
| List       | リスト       | array                          |
| Tuple      | タプル       | array                          |
| Set        | セット       | array(uniqueitems=trueとなる)  |
| Dict       | 辞書         | object                         |

また、次のものはdatetimeモジュールをimportして型に指定できる。<br />

| 型                  | 説明            |
|:--------------------|:----------------|
| datetime.date       | 日付            |
| datetime.time       | 時刻            |
| datetime.datetime   | タイムスタンプ  |
| datetime.timedelta  | 時間間隔        |


- 型ヒントを使うために、List[str]のように要素の型も指定可能
- また、`Union`や`Optional`も使用できる。
    - `Union`の場合、JSON Schemaでは`oneOf`指定になる。
    - `Optional`の場合、JSON Schemaでは`required`が指定されない。（任意の値となる。）


## 必須チェックとデフォルト値
プロパティの必須チェックには次の４パターンの類型がある。

### 厳密な必須チェック（プロパティが存在し、値が`None`ではない）
```
from pydantic import BaseModel

class Hoge(BaseModel):
    hoge: int

```

と書くと次のバリデーションによりエラーになる可能性がある。

- `hoge`が存在しない
- `hoge`が存在しても、その値が`None`である。
- `hoge`が整数ではない。

### 緩い必須チェック（プロパティが存在すれば、値自体`None`でもいい）

```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    hoge: Optional[int] = Field(...)

```
と書くと、以下のエラーになる可能性がある。

- `hoge` が存在しない。
- `hoge` が整数ではない。


### 必須チェックなし（プロパティが存在しなくてもいい）

```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    hoge: Optional[int]

```
と書けば、次のエラーになる可能性がある。

- `hoge` が整数ではない。

### 値が必要なオプション（プロパティは存在しなくてもいいが、存在するなら`null`ではダメなもの）

以下のように冗長な書き方が必要になる。
```
from pydantic import BaseModel, Field, validator

class Hoge(BaseModel):
    hoge: Optional[int]

    @validator("hoge")                # hogeのバリデーションの登録 
    def validate_hoge(cls, value):    # 関数名はなんでもいい。第1引数はcls固定で使用しない。第2引数はvalueでhogeに設定した値
        if value is None:             # Noneであれば例外を投げる
            raise TypeError("none is not an allowed value") 
        return value                  # Noneでない場合はそのままvalueを返す

```

上記のように書けば、以下のエラーになる可能性がある。
- `hoge` が`None`
- `hoge` が整数ではない

## デフォルト値
```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    hoge: int = 1
    # または
    fuga: int = Field(2)

```
と書くことによりデフォルト値を設定できる。<br />
デフォルト値がある場合は、値を明示的に設定しなくてもデフォルト値が設定されていることになるので必須チェックにはかからない。<br />


`Field()`の第一引数にはデフォルト値を設定するが、デフォルト値が不要な場合は、`...`(Ellopsis)を書く。


## 形式チェック

### 文字列の長さ


```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    foo: str = Field(..., min_length=2, max_length=10)

```
と書けば、文字列が2文字以上10文字以下に当てはまらない場合、エラーとなる。<br />
また、デフォルト値の設定が`...`となっているので、値を設定しない場合は必須エラーとなる。


### リストの要素数

```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    foo: List[str] = Field(..., min_items=2, max_items=10)

```
と書けば、要素数が2個から10個以外の場合、エラーとなる。


### 正規表現

```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    foo: str = Field(..., regex=r"[0-9]{2,3}")

```

と書けば、`regex=r"[0-9]{2,3}"`で指定した正規表現にマッチしない場合、エラーとなる。


## 値チェック
### 数値の範囲


```
from pydantic import BaseModel, Field

class Hoge(BaseModel):
    hoge: int = Field(..., ge=2, le=10)

```
と書けば、値が2から10の範囲外の場合、エラーとなる。<br />
次の４種類が使用できる。

- `ge`: `>=` greater than or equal 以上
- `gt`: `>` greater than 超過
- `le`: `<=` less than or equel 以下
- `lt`: `<` less than 未満

### コード定義

```
from enum import Enum
from pydantic import BaseModel, Field

class HogeCode(Enum):
    AAA = 1
    BBB = 2
    CCC = 3

class Hoge(BaseModel):
    foo: HogeCode

```
と書けば、値が1, 2, 3意外の値の場合エラーとなる。

## 複雑なバリデーション
`validator`を実装する。Validatorsに関するリンクは[こちら](https://docs.pydantic.dev/latest/usage/validators/)

```
from pydantic import BaseModel, Field, validator

class Hoge(BaseModel):
    hoge: Optional[int]

    @validator(プロパティ名) 
    def validate_hoge(cls, value, values):
        if ここでチェック:
            raise ValueError("何かのメッセージ") 
        return value

```
- プロパティ名を**@validator**デコレーターに指定するとそのプロパティがチェックされる。
- **@validator**デコレーターに`each_item=True`を設定すると、チェックしたい`List`や`Dict`などのコレクションの場合、要素一つひとつについてバリデートできる。
- validatorの関数名は任意のものでよい。
- validatorの関数の第1、第2引数は固定、第3引数以降は任意だが、`values`という名前の引数を定義すると、そのプロパティより前にバリデートされたプロパティの辞書が入ってくる。
    - これを使用して相関チェックできる
- `raise`できる例外クラスは`TypeError`か`ValueError`。
    - `raise`せずに`assert`を使って`AssertionError`を投げても問題ない。

## Pydanticでは実装できないバリデーション
データベースの内容との相関チェックはPydantic単体ではバリデーションできない。<br />
そのため、パスオペレーション関数（`@app.get(...)`とかつけた関数）の先頭でチェックする。<br />


例えば、リクエストボディが次のような形式だとして`qux`にエラーがあった場合は次のように返却する。

```
{
    "foo": {
        "bar": 123,
        "baz": {
            "qux": "errrrrrr"  // これがエラーとする
        }
    }
}

```


```
from fastapi.exceptions import RequestValidationError
from pydantic.error_wrappers import ErrorWrapper

# ...中略...

if エラー判定:
    raise RequestValidationError(
        [ErrorWrapper(ValueError("エラーメッセージをどうぞ"), ("body", "foo", "baz", "qux"))]
    )

```

`RequestValidationError`を利用することで他のバリデーションエラーと同じ形式で出力される。<br />


エラーレスポンスは次のようになる。

- `422 Unprocessable Entity`

```
{
  "detail": [
    {
      "loc": [
        "body",
        "foo",
        "bar",
        "qux"
      ],
      "msg": "エラーメッセージをどうぞ",
      "type": "value_error"
    }
  ]
}

```

## 例外クラスと`type`についての詳細
- `ErrorWrapper`に指定できる例外クラスは`@validator`で`raise`できるものと同じである。
- `ValueError`の場合、レスポンスに出力される`type`は`value_error`となる。
- `TypeError`の場合、レスポンスに出力される`type`は`type_error`となる。
- `ValueError`のサブクラスを実装し、クラス変数として`code="piyopiyo"`を定義すると、レスポンスに出力される`type`は`value_error.piyopiyo`となる。



## Pydantic v2でモデルのバリデーションが高速化されているらしい。

Pydantic v2では、バリデーション部分の実装をRustで書き直し、pydantic-coreという別のパッケージに分離されている。これにより、v1と比較して、4~50倍バリデーション処理が高速化されたと[公式ページ](https://docs.pydantic.dev/latest/blog/pydantic-v2/#performance)で言及されている。<br />


# 参考文献


[Qiita記事](https://qiita.com/uenosy/items/2f6b1aa258018d3db76c)


[Safie Engineers' Blog!](https://engineers.safie.link/entry/2023/07/05/fastapi-pydantic-v2)
