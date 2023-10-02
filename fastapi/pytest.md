# Pytestについて
## 概要
Pythonにはpytestという単体テストを書く機能がある。<br />
他の言語と若干仕様が異なるので使う機能や書き方をまとめておく。<br />


## テストの書き方・実行方法
実装例として、`CreateXxx`というクラスが`print_aaa`というメソッドを持っている場合のテストケース<br />

```:python:tests/test_create_xxx.py
import pytest
from xxx import CreateXxx, XxxError

class TestCreateXxx:
  # 通常の評価
  def test__can_print_aaa(self):
    xxx = CreateXxx()
    assert xxx.print_aaa(111) == 'aaa'

  # エラーがでることを評価
  def test__can_raise_error(self):
    xxx = CreateXxx()
    with pytest.raises(XxxError):
      xxx.print_aaa(222)

```

このようなファイルを用意して以下コマンドを打つと実行する。<br />

```:bash
$ pytest tests/test_create_xxx.py

```


## fixtureについて
- fixtureという機能を使ってsetupを実現する。
- 引数にfixtureとして定義したメソッド名を書くことでテスト開始直前に実行されることになる。

```:python:tests/test_create_xxx.py

  @pytest.fixture
  def init_xxx(self):
     self.xxx = CreateXxx()

  def test__can_print_aaa(self, init_xxx):
    assert self.xxx.print_aaa() == 'aaa'

```

## yieldの使用
- fixtureの中で`yield`を呼ぶと、その段階で一旦実行が中断され、テストメソッド本体が実行され、その後、`yield`以降の処理が実行されることになる。
- 変数をローカルに閉じ込められるので可読性を高められる。
- 戻り値を戻したい時は`yeild`の背後に戻り値を書く。
- なお、`yield`は1回しか使えず、複数回使うとエラーとなる。

```:python:tests/test_create_xxx.py

  @pytest.fixture
  def init_xxx(self):
     self.xxx = CreateXxx()
     yield
     # ここから teardown
     self.xxx.close()

  def test__can_print_aaa(self, init_xxx):
    assert self.xxx.print_aaa() == 'aaa'

```

## 戻り値を返すfixture

```:python:tests/test_create_xxx.py

  @pytest.fixture
  def xxx(self):
     return CreateXxx()

  def test__can_print_aaa(self, xxx):
    assert xxx.print_aaa() == 'aaa'

```

## 戻り値を複数返すfixture

```:python:tests/test_create_xxx.py

  @pytest.fixture
  def init_xxx(self):
     return CreateXxx(), 'aaa'

  def test__can_print_aaa(self, init_xxx):
    xxx, expected = init_xxx
    assert xxx.print_aaa() == expected

```

## データジェネレート
同じテストケースを引数違いで何度も実行したい場合<br />

### @pytest.mark.parametrizeを使う方法（テストケースごとに定義）
fixtureではないので共有できないし、複雑な処理もできないがシンプル。<br />


```:python:tests/test_create_xxx.py

  @pytest.mark.parametrize('val, expected', [
    ('aa', 'bb'),
    ('xx', 'yy'),
  ])
  def test__can_print_aaa(self, val, expected):
    assert xxx.print_aaa(val) == expected

```
#### parametrizeのテストケースが文字化けしないようにする方法
##### 全角文字がエスケープされないように
後日まとめる。

##### オブジェクトの内容が表示されるように
後日まとめる。（イメージがわかないので、とりあえずパス）

### fixtureを使う方法（複数のテストケースで共有可能）

```:python:tests/test_create_xxx.py

  @pytest.fixture(params=[
    ('aa', 'bb'),
    ('xx', 'yy'),
  ])
  def case__printable(self, request) -> Tuple[str, str]:
    return request.param

  def test__can_print_aaa(self, case__printable):
    val, expected = case__printable
    assert xxx.print_aaa(val) == expected

```

## ファイルを跨いで共通化したい場合: conftest.py

`conftest.py`に書いたものはファイル間で共有される。

```:python:tests/conftest.py

  @pytest.fixture
  def xxx():
    return CreateXxx()

```

```:python/test_create_xxx.py

  def test__can_print_aaa(self, xxx):
    assert xxx.print_aaa() == expected

```

## fixtureの実行タイミングを明示する
デフォルトは（引数でfixture名を指定している）テストケースメソッドの開始直前だが、これを変更することができる。<br />
下記のように明示する。<br />

```:python

  @pytest.fixture(scope='class')

```
| スコープ名   | 実行されるタイミング                    |
|:-------------|:----------------------------------------|
| function     | テストケースごとに1回実行（デフォルト） |
| class        | テストクラス全体で1回実行               |
| module       | テストファイル全体で1回実行             |
| session      | テスト全体で1回だけ実行                 |

## モック
例えば、以下のような場合にモックしたくなる。<br />

- AWSなど外部と繋ぐ部分（単体Testでは繋げたくない）
- 現在時刻、ランダム値を使う場合（毎回変わるのでテストしづらい）
- 外部の責務（=別の単体テストですでにテストしている）部分（テストのメンテで別の責務に引きずられたくない）

### importされる関数の中身を一時的に差し替える
テストケース終了時に元に戻る。<br />

#### mocker.patch.objectで何もしないようにする
```:python:tests/test_create_xxx.py

from pytest_mock import MockFixture
import hoge

class TestCreateXxx:
  def test__can_print_aaa(self, mocker: MockFixture, xxx):
    mocker.patch.object(hoge, 'fuga')  # hoge の fuga という関数を何もしないモックに差し替える
    assert xxx.print_aaa() == 'aaa'

```

#### mocker.patch.objectで戻り値を定義する

```:python:tests/test_create_xxx.py

from pytest_mock import MockFixture
import hoge

class TestCreateXxx:
  def test__can_print_aaa(self, mocker: MockFixture, xxx):
    mocker.patch.object(hoge, 'get_fuga', return_value='fuga')
    assert xxx.print_aaa() == 'aaa'

```

#### mocker.patch.objectで呼び出しごとに戻り値を変える

```:python:tests/test_create_xxx.py

from unittest.mock import call
import hoge

class TestCreateXxx:
  def test__can_print_aaa(self, mocker: MockFixture, xxx):
    # side_effect で配列を渡すと呼び出しごとに順に返すようになる
    m_get_fuga = mocker.patch.object(hoge, 'get_fuga', side_effect=[
        'fuga1',
        'fuga2',
    )
    xxx.print_aaa()  # fuga1 が使われる
    xxx.print_aaa()  # fuga2 が使われる

```

## FastAPIにおけるAPIの単体テスト
### 実装例
簡単なTodoアプリを想定し、以下のようにタスクの詳細を読み込む場合のテストを実装。<br />
最初5行はfixtureを使ってダミーデータを作るでもいいかもしれない。<br />
testclientを使用して、apiにアクセスした場合のレスポンスコードや正しいデータが帰ってきているか確認することができる。<br />


```:python

from fastapi.testclient import TestClient
from sqlmodel import Session

from models.task import Task

def test_read_task(session: Session, client: TestClient):
    task1 = Task(title='task1', done=False)
    task2 = Task(title='task2', done=True)
    session.add(task1)
    session.add(task2)
    session.commit()

    resp = client.get(url='/tasks/1')
    data: dict = resp.json()
    assert resp.status_code == 200
    assert data == {'title': 'task1', 'done': False, 'id': 1}

    resp = client.get(url='/tasks/2')
    data: dict = resp.json()
    assert resp.status_code == 200
    assert data == {'title': 'task2', 'done': True, 'id': 2}

    resp = client.get(url='/tasks/3')
    assert resp.status_code == 404

```

## 参考文献
[Qiita記事](https://qiita.com/waterada/items/6143d80896eb9d89bf2f)<br />
[pytestを使ってFastAPIアプリケーションのテストコードを実装する](https://laid-back-scientist.com/fastapi-test)
