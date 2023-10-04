# SQL（リレーショナル）データベース
## SQLAlchemy
FastAPIでは、SQL（リレーショナル）データベースを使用する必要がないが、SQLAlchemyにより必要なリレーショナルデータベースを使用できる。<br />
次のようなSQLAlchemyでサポートされているデータベースに適合させることができる。<br />
[SQLAlchemy公式](https://www.sqlalchemy.org/)<br />


- PostgreSQL
- MySQL
- SQLite
- オラクル
- Microsoft SQL Serverなど

## ORM

FastAPIがデータベースと通信する一般的なパターンとして「ORM」（オブジェクト リレーショナル マッピング）ライブラリを使用することが挙げられる。<br />

ORMには、コード内のオブジェクトとデータベーステーブルの間で変換するツールがある。<br />
ORMを使用すると通常SQLデータベース内のテーブルを表すクラスを作成し、クラスの各属性は名前と型を持つ列を表す。<br />


例えば、クラスは`Pet`として場合SQLテーブル`pets`を表すことができる。<br />
そして、そのクラスの各インスタンスオブジェクトはデータベース内の行を表す。<br />

例えば、オブジェクト`orion_cat`のインスタンスは、列の`Pet`属性を持つことができる。そして、その属性は`orion_cat.type type "cat"`となる。<br />

ORMには、テーブルまたはエンティティ間の接続・関係を作成するためのツールもある。<br />





## 参考文献
[公式ドキュメント](https://fastapi.tiangolo.com/tutorial/sql-databases/a)<br />

