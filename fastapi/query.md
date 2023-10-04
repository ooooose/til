# クエリパラメータについて
FastAPIではパラメータの追加情報とバリデーションを追加することができる。<br />

```python:test.py

from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/")
async def read_items(q: Union[str, None] = None):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```
上記コードでは、`q`という値でクエリパラメータを受け取る。<br />
`q`は、`Optional[str]`型で、`None`を許容する`str`型を意味しており、デフォルトは`None`である。そのため、FastAPIは`q`自体必須ではないと理解する。<br />

## バリデーションの追加
`q`はオプショナルだが、もし値が渡された場合には、**50文字を超えないこと**を矯正してみる。<br />


### `Query`のインポート
そのためには、まず`fastapi`から`Query`をインポートする。<br />


```python:/test.py

from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Union[str, None] = Query(default=None, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```
### デフォルト値として`Query`を使用
パラメータのデフォルト値として使用し、パラメータ`max_length`を50に設定する。<br />

```python:/test.py
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Union[str, None] = Query(default=None, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```

デフォルト値の`None`を`Query(default=None)`に置き換える必要があるので、`Query`の最初の引数はデフォルト値を定義するのと同義。<br />


```python:test.py
q: Optional[str] = Query(default=None)

```
上記は以下と同じようにパラメータをオプションにする。<br />

```python
q: Optional[str] = None

```

さらに多くのパラメータを`Query`に渡すことが可能である。この場合、文字列に適用される`max_length`パラメータを指定する。<br />

```python
q: Union[str, None] = Query(default=None, max_length=50)
```

これにより、データを検証し、データが有効でない場合は明確なエラーを表示し、OpenAPIスキーマの**path operation**にパラメータを記載する。<br />


## バリデーションをさらに追加する
パラメータ`min_length`も追加することが可能。<br />


```python
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Union[str, None] = Query(default=None, min_length=3, max_length=50)
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```

## 正規表現の追加
パラメータが一致するべき正規表現を定義することができる。<br />

```python
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Union[str, None] = Query(
        default=None, min_length=3, max_length=50, pattern="^fixedquery$"
    )
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```
この特定の正規表現は受け取ったパラメータの値をチェックする。<br />

- `^`は、これ以降の文字で始まり、これより以前には文字はないことを意味する。
- `$`で終わる場合、`fixedquery`以降には文字はこない。

## デフォルト値

第一引数に`None`を渡して、デフォルト値として使用するのと同じように、他の値を渡すこともできる。<br />


クエリパラメータの`q`の`min_length`を`3`とし、デフォルト値を`fixedquery`としてみる。<br />

```python
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: str = Query(default="fixedquery", min_length=3)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```

## 必須にする
これ以上バリデーションやメタデータを宣言する必要のない場合は、デフォルト値を指定しないだけでクエリパラメータ`q`を必須にすることができる。<br />

- パターン①

```python
q: str
```

- パターン②

```python
q: Union[str, None] = None
```

- パターン③

```python
q: Union[str, None] = Query(default=None, min_length=3)
```

そのため、`Query`を使用して必須の値を宣言する必要がある場合は、第一引数に`...`を使用することができる。<br />

```python
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: str = Query(min_length=3)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```

## クエリパラメータのリスト/複数の値
クエリパラメータを明示的に`Query`で宣言した場合、値のリストを受け取るように宣言したり、複数の値を受け取るように宣言したりすることができる。<br />

例えば、URL内に複数回出現するクエリパラメータ`q`を宣言するには以下のように記述する。<br />

```python
from typing import List, Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Union[List[str], None] = Query(default=None)):
    query_items = {"q": q}
    return query_items
```


この場合のURLは以下のようになる。<br />

```
http://localhost:8000/items/?q=foo&q=bar

```

複数のクエリパラメータの値`q`（`foo`と`bar`）を**path operation**関数内で関数パラメータ`q`としてPythonの`list`を受け取ることになる。<br />


そのため、このURLのレスポンスは以下のようになる。<br />

```json
{
  "q": [
    "foo",
    "bar"
  ]
}

```
SwaggerUIでも複数の`q`が設定できるようになっている。<br />

[![Image from Gyazo](https://i.gyazo.com/4766d62b978ffaf5adf84ddd1472c85a.png)](https://gyazo.com/4766d62b978ffaf5adf84ddd1472c85a)<br />


## デフォルト値を持つクエリパラメータのリスト/複数の値
また、値が指定されない場合はデフォルトの`list`を定義することもできる。<br />


```python

from typing import List

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: List[str] = Query(default=["foo", "bar"])):
    query_items = {"q": q}
    return query_items
```
以下のURLを開いても、`q`の値がデフォルトで`["foo", "bar"]`と設定されていることを意味する。<br />


```
http://localhost:8000/items/

```


レスポンスは以下のとおり。<br />

```json
{
  "q": [
    "foo",
    "bar"
  ]
}

```

### `list`を使う

`List[str]`の代わりに直接`list`を使うこともできる。

```python
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: list = Query(default=[])):
    query_items = {"q": q}
    return query_items

```
この場合、FastAPIはリストの内容をチェックしないことに注意。<br />

例えば`List[int]`はリストの内容が整数であるかどうかをチェックするが、`list`だけではそうならない。<br />


## より多くのメタデータを宣言する
パラメータに関する情報をさらに追加することができる。<br />

その情報は、生成されたOpenAPIに含まれ、ドキュメントのユーザーインターフェースや外部のツールで使用される。<br />

例えば、`title`を追加できる。<br />

```python
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Union[str, None] = Query(default=None, title="Query string", min_length=3)
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```

`description`も追加可能。<br />


```python
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Union[str, None] = Query(
        default=None,
        title="Query string",
        description="Query string for the items to search in the database that have a good match",
        min_length=3,
    )
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```
おそらく、`title`や`description`はメタデータとして受け取る予約語になっている？<br />



## エイリアスパラメータ
パラメータに`item-query`を指定するとする。<br />


例えば、以下のようなURLでリクエストを送るとする。<br />

```
http://127.0.0.1:8000/items/?item-query=foobaritems

```

しかし、`item-query`は有効なPythonの変数名ではなく、通常`item_query`を使用する。<br />
どうしても`item-query`と正確に一致している必要があるとした場合、`alias`を宣言することができる。エイリアスはパラメータ野値を見つけるのに使用される。<br />



```python
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Union[str, None] = Query(default=None, alias="item-query")):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```
要は、`q`のエイリアスとして`item-query`としてもクエリパラメータを受け取ることができるといった設定かも。<br />


## 非推奨パラメータ

あるパラメータを使っているクライアントに対して、ドキュメントとして**非推奨**と明記しておきたいとする。<br />


その場合、`Query`にパラメータ`deprecated=True`を渡す。<br />


```python
from typing import Union

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Union[str, None] = Query(
        default=None,
        alias="item-query",
        title="Query string",
        description="Query string for the items to search in the database that have a good match",
        min_length=3,
        max_length=50,
        pattern="^fixedquery$",
        deprecated=True,
    )
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results

```

SwaggerUIは以下のようになり、クエリを非推奨として表示する。<br />


[![Image from Gyazo](https://i.gyazo.com/3d0b3d66698b7b45e8c3f130a59076eb.png)](https://gyazo.com/3d0b3d66698b7b45e8c3f130a59076eb)<br />


## まとめ

パラメータに追加のバリデーションとメタデータを宣言することができる。<br />


- `alias`
- `title`
- `description`
- `deprecated`

文字列のためのバリデーション<br />

- `min_length`
- `max_length`
- `regex`

この例では、`str`の値のバリデーションを宣言する方法を見てきた。<br />
`int`や他の型のバリデーションを宣言する方法は[こちら](https://fastapi.tiangolo.com/ja/tutorial/path-params-numeric-validations/)を参照するとよいかも。<br />



## 参考文献
[公式ドキュメント](https://fastapi.tiangolo.com/ja/tutorial/query-params-str-validations/)
