# SQLAlchemyについて

## SQLAlchemyとは

SQLAlchemyとあh、Pythonの中でよく利用されているORMの一つ。<br />
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


# 参考文献
[SQLAlchemyの基本的な使い方](https://qiita.com/arkuchy/items/75799665acd09520bed2)
