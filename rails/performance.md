# パフォーマンス向上のコツ

## N+1を改善


### N+1とは

ループ処理の中で都度SQLを発行してしまい、大量のSQLが発行されることによりパフォーマンスが低下してしまう問題のこと。<br />


日常生活で例えると、スーパーで商品を1点ずつお会計しているようなものらしい。


#### N+1問題の例
会社（`companies`）とそれに所属する人（`users`）を例にとると...<br />
例えば、`companies`テーブルと`users`テーブル以下のようなデータがあると仮定。<br />

##### companiesテーブル
| id   | name           |
|:-----|:---------------|
| 1    | AB商事         |
| 2    | CDテクノロジー |
| 3    | EF株式会社     |

##### usersテーブル

| id   | company_id     | name        |
|:-----|:---------------|:------------|
| 1    | 1              | 田中さん    |
| 2    | 1              | 佐藤さん    |
| 3    | 1              | 鈴木さん    |
| 4    | 2              | 吉田さん    |
| 5    | 2              | 高橋さん    |
| 6    | 3              | 山田さん    |
| 7    | 3              | 渡辺さん    |

一つの会社には複数の人が所属している。<br />
言い換えると、会社と人との間には`1対多`の関係が成り立っている。<br />
それぞれのモデルの定義は以下のようになっている。



```:rails:app/models/company.rb
class Company < ApplicationRecord
  has_many :users
end
```


```:rails:app/models/user.rb
class User < ApplicationRecord
end

```
## 参考文献

[【Ruby on Rails】N+1問題ってなんだ？](https://qiita.com/massaaaaan/items/4eb770f20e636f7a1361)
