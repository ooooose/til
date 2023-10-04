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
後日まとめる。

## 参考文献
[公式ドキュメント](https://fastapi.tiangolo.com/ja/tutorial/query-params-str-validations/)
