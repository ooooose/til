# APIRotuerの設定方法

## 概要
FastAPIのモジュールに`APIRouter`がある。<br />
エンドポイントのパスを作成する際に使用するモジュールである。<br />

## 実装例

`main.py`では、以下のとおり実装している。<br />

```python

from fastapi import FastAPI
from src.routers import item

app = FastAPI()
app.include_router(item.router)


@app.get("/")
async def home():
    return {"message": "hello sql-alchemy-sample"}

```

一方で、`/routers/item.py`の実装内容は以下のとおり。<br />


```python

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from src.schemas.item import Item

from src.db import get_db

router = APIRouter()


@router.post("/items", response_model=Item)
async def create_item(item_data: Item, db: Session = Depends(get_db)):
    item = Item(**item_data.dict())
    db.add(item)
    db.commit()
    db.refresh(item)
    return item

```

まず、routerとしてAPIRouterのインスタンスを作成する。<br />
各エンドポイントには@router.post("/items")という形で定義することができる。<br />

エンドポイントを有効にするためには、`app`というFastAPIのインスタンスに読み込む必要がある。<br />
今回は`main.py`で以下のとおり実装した。<br />

FastAPIのインスタンスである`app`に対して、`app.include_router(item.router)`とすることで、itemファイルの中で定義している`router`を有効化することができる。<br />
定義する場所はプロジェクトによって異なるので、注意が必要。<br />

```python

from fastapi import FastAPI
from src.routers import item

app = FastAPI()

# 以下でrouterをFastAPIノインスタンスであるappに読み込む。
app.include_router(item.router)


@app.get("/")
async def home():
    return {"message": "hello sql-alchemy-sample"}

```

## 参考文献
- []()
