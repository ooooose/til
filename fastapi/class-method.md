# classmethodとは
## 概要
以下のような実装をすると、`A.my_cls_method()`のように`クラス.メソッド()`のように呼び出せる。<br />
```python
class A:
    @classmethod
    def my_cls_method(cls):
        return "Hello"
```

上記のように`@classmethod`とつけることでクラスメソッドとして作成することができる。<br />
この場合メソッドの第一引数は`self`（インスタンス）ではなく、クラスそのもの（`cls`）になる。<br />

## クラスメソッドとメソッドの違いは？
Pythonのメソッドは、クラスをインスタンスにしないと呼び出せないが、クラスメソッドは以下のとおり、クラスから直接呼び出せる。<br />

```python
A.my_cls_method()
```

## クラスメソッドと関数の違いは？
クラスメソッドと関数の違いに大きなものはないが、以下の点がクラスメソッドの違いとして挙げられる。<br />
- 第一引数でクラスが取得できる。
- クラスの中にあるので、クラスをインポートすれば使える。

よく使われる例としては、そのクラスを作るメソッドを書くことである。<br />
以下の例では`Item`クラスと、Itemの情報をあるAPIから取得して返す`retrieve_from_api`というクラスメソッドを実装している。<br />

```python
class Item:
    def __init__(self, id, name):
        self.id = id
        self.name = name

    @classmethod
    def retrieve_from_api(cls, id):
        res = requests.get(f"https://api.example.com/items/{id}")
        data = res.json()
        return cls(id, data["name"])
```

クラスメソッドを使わず、以下のように関数でも書くことができる。<br />

``python
class Item:
    def __init__(self, id, name):
        self.id = id
        self.name = name


def retrieve_item(id):
    res = requests.get(f"https://api.example.com/items/{id}")
    data = res.json()
    return Item(id, data["name"])
```
上記のクラスメソッドと関数の違いには`import Item`とした時に、まとめてインポートできるかどうかがある。<br />

