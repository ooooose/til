# SQLAlchemyについて

## SQLAlchemyとは

SQLAlchemyとは、Pythonの中でよく利用されているORMの一つ。<br />
ORMとは、Object Relational Mapperのことで、テーブルとクラスを1対1に対応させて、そのクラスのメソッド経由でデータを取得したり、変更したりできるような存在。

## ORMの利点とは

### 異なるDBの違いを吸収してくれる

DBの種類によらず、同じソースコードで操作できるので、複数のDBを併用する場合や、DBを変更する場合にも、コードの書き換えの必要がない。

### SQLを書かなくてもよい

MySQLや、SQLite,PostgreSQLなどのDBを操作するにはSQL遠使うが、SQLAlchemyを使うと、SQLを直接記載することなしに、DBを`Pythonic`に操作できる。

## SQLAlchemyの使い方

まず、どのDBにどうやって接続するかの設定を行う。（その設定内容を保持したものが`エンジン`と呼ばれる）<br />
その後、マッピングを行い、セッションを作成。<br />
そして、そのセッションを使ってDB操作を行う。



### DBエンジンを作成する。

```
from sqlalchemy import create_engine
engine = create_engine("{dialect}+{driver}://{username}:{password}@{host}:{port}/{database}?charset={charset_type})

```

のように書き、エンジンのインスタンスを作成する。

| 要素             | 説明                                                  |
|:-----------------|:------------------------------------------------------|
| dialect          | DBの種類を指定。mysql, postgresql, oracleとか。       |
| driver           | DBに接続するためのドライバーを指定。                  |
| username         | DBに接続することができるユーザー名を指定。　　　　　　|
| password         | DBに接続するためのパスワードを指定する。              |
| host             | ホスト名を指定する。localhostとかIPアドレスとか。     |
| port             | ポート番号を指定する。                                |
| database         | 接続するDB名を指定する。                              |
| charset_type     | 文字コードを指定する。utf8とか。　　　　　　　　　　　|



例えば、以下のような感じ。

```
engine = create_engine("mysql://scott:tiger@localhost/foo")

```

### モデルクラスを作る（テーブル定義を書く）

まずは、モデルベースクラスを作る。

```
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()

```
そして、このベースクラスを拡張すると、ORMで扱える。<br />
例えば、以下のようにクラスを書けばよい。また、モデルクラスを定義する時にメソッドを定義することも可能。<br />

```
from sqlalchemy.schema import Column
from sqlalchemy.types import Integer, String

class User(Base):
    __tablename__ = "user"  # テーブル名を指定
    user_id = Column(Integer, primary_key=True)
    first_name = Column(String(255))
    last_name = Column(String(255))
    age = Column(Integer)

    def full_name(self):  # フルネームを返すメソッド
        return "{self.first_name} {self.last_name}"

```

上記定義したのは以下のようなテーブルをマッピングさせたクラス。<br />

| Field           | Type          | Null       | Key      | Default     | Extra           |
|:----------------|:--------------|:-----------|:---------|:------------|:----------------|
| user_id         | int(11)       | NO         | PRI      | NULL        | auto_increment  |
| first_name      | varchar(255)  | YES        |          | NULL        |                 |
| last_name       | varchar(255)  | YES        |          | NULL        |                 |
| age             | int(11)       | YES        |          | NULL        |                 |


このテーブルをDBに作成するには<br />
```
Base.metadata.create_all(engine)

```

これで、Baseを継承しているテーブル群が一括して作成される。<br />
詳しいテーブル定義の内容は以下のとおり。<br />
[SQLAlchemyでのテーブル定義](https://qiita.com/arkuchy/items/8ae90e4a73ef30dc4749)

### metadataとは

上のコードで`metadata`という言葉が出てきたが、`metadata`とはDBの様々な情報を保持しているオブジェクトのこと。<br />
`metadata`を使うと、既存のDBからテーブル定義を持ってきたりすることもできる。


## セッションを作成する


SQLAlchemyはセッションを介してクエリを実行する。<br />
そもそもセッションとは、コネクションを確立してから切断するまでの一連の単位のこと。<br />
（DBとPythonコードを結びつける紐のようなものというイメージ）<br />



セッションを作るクラスを`sessionmaker`で作る。（使うエンジンが一定なら、この時指定する。）<br />


```
from sqlalchemy.orm import sessionmaker
SessionClass = sessionmaker(engine)  # セッションを作るクラスを作成
session = SessionClass()

```

## CRUD処理を行う

CRUDというのは、以下の機能をまとめた呼び方のこと。
- Create(新規作成)
- Read(読み取り)
- Update(更新)
- Destroy(削除)

セッションを閉じたり、commit()しないとDBが更新されないので注意。


### INSERT

新規オブジェクトをsessionにadd()すると、INSERT対象となる。

```
user_a = User(first_name="first_a", last_name="last_a", age=20)
session.add(user_a)
session.commit()
```
| user_id      | first_name    | last_name      | age    |
|:-------------|:--------------|:---------------|:-------|
| 1            | a             | a              | 20     |


### SELECT

テーブルからデータを取り出すには、`query`遠使う。<br >
（新しいSQLAlchemyだと`scalars`が推奨されているようなので、まとめる。）

```
users = session.query(User).all()  # userテーブルの全レコードをクラスが入った配列で返す
user = session.query(User).first()  # userテーブルの最初のレコードをクラスで返す
```


### UPDATE

セッションから取り出したオブジェクトを変更すると、UPDATE対象となる。

```
user_a = session.query(User).get(1)  # 上で追加したuser_id=1のレコード
user_a.age = 10
session.commit()

```

| user_id      | first_name    | last_name      | age    |
|:-------------|:--------------|:---------------|:-------|
| 1            | a             | a              | 10     |


### DELETE

セッションから取り出したオブジェクトを`delete()`すると、DELETE対象となる。

```
user_a = session.query(User).get(1)
session.delete(user_a)
session.commit()

```


もしくは、検索条件にマッチするものを消すことも可能。

```
session.query(User).filter(User.id == 1).delete()
session.commmit()

```

## 既存のテーブルを使う場合

先ほど、新規のテーブルをマッピングしたクラスを作成したが、既存のテーブルをマッピングしたクラスを作りたいこともある。<br />
手順としては、Baseクラスに`metadata`を私、`__tablename__`を既存のテーブル名に合わせて、autoloadを`True`にすればよい。<br />
Baseクラスにmetadataを渡す方法としては、Baseクラスを作る時にengineを渡す方法がある。

```
Base = declarative_base(bind=engine)

```


そのほかに、Baseクラスを作る時にmetadataを渡す方法もある。

```
from sqlalchemy.schema import MetaData
meta = MetaData(engine)
meta.reflect()  # metadataを取得， meta=MetaData(engine, reflect=True)と同じ
Base = declarative_base(metadata=meta)

```

また、Baseクラスを作ったあとのインスタンスに付与する方法も可能。

```
Base = declarative_base()
Base.metadata.bind = engine

```


例えば、テーブル名が`existing_user`というテーブルが存在していたとすれば

```
Base = declarative_base(bind=engine)

class Exisiting_user(Base):  # クラス名は何でもok
    __tablename__ = "exisiting_user"
    __table_args__ = {"autoload": True}


```

とすればよい。

# WEB+DBで読んだことまとめ
## データベース操作に必要な機能を提供するCoreについて
SQLAlchemyの機能は大きくCoreとORMの2つに分類できる。<br />
SQLAlchemyの中心にあるCoreは、データベースとの接続、クエリの構築や実行などのデータベース操作に必要となる機能の全てを提供する。<br />

## オブジェクト指向によるレコード操作を提供するORM
Coreの上に構築されたORMは、オブジェクト指向によるレコード操作を実現する。<br />
ORMではテーブルごとにPythonのクラスを用意し、そのインスタンスに対する操作がそのテーブルのレコードに対する操作と対応する。<br />
クエリの多くはSQLAlchemyが自動生成して必要なタイミングで実行してくれる。

## Coreを使ったDB操作
続きもまとめる。


# 参考文献
[SQLAlchemyの基本的な使い方](https://qiita.com/arkuchy/items/75799665acd09520bed2)
