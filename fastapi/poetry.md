# Poetryについて
## 概要
`Poetry`はPythonのパッケージマネージャの一つ。<br />
`pip`と同じようにパッケージを`pypi`などからダウンロードしてきてインストールすることができるが、それに加えて以下のようなこともできる。<br />

- パッケージ管理ファイルの生成・変更
- インストールされているパッケージのインストール
- プロジェクトごとの仮想環境のセットアップ　など

他の言語だと、`npm`や`yarn`などのパッケージマネージャがあるが、それらと同等のものがPythonにもできたという感じ。<br />

## Poetryのインストール
Mac,Linux,Windows(Bash)上でのインストールは以下の一行でいける。<br />


```
curl -sSL https://install.python-poetry.org | python3 -

```

## Poetryを使う
利用する流れは以下のとおりになる。<br />


### Pythonのセットアップ
PoetryのインストールにPythonが必要になる。<br />
特定のバージョンを使いたい場合には`pyenv`を使ってインストールしておく。<br />
その上で、特定のバージョンのPythonを入れるには以下のようにする。<br />


```
# インストールされているバージョンを確認
pyenv versions 

# インストール可能なバージョンを表示
pyenv install --list

# 指定したバージョンをインストール
pyenv install <python-version>

# グローバルなデフォルトを指定したバージョンに変更
pyenv global <python-version>

```

### プロジェクトのセットアップ
使いたいバージョンのPythonがセットアップされたら、次にPoetryプロジェクト（パッケージ）をセットアップする。<br />
これには「新規に立ち上げる場合」と「すでにあるプロジェクトをPoetry管理下におく場合」の2通りがある。<br />

#### 新規に立ち上げる場合
まっさらな状態から新たにプロジェクトを作る場合には `poetry new`を使う。<br />

```
poetry new <project-name>

```

そうすると、`<project-name>`ディレクトリ配下にファイルがいくつかできる。<br />

以下`project_abc`というプロジェクト名のディレクト例である。<br />

```
project_abc
├── README.rst
├── project_abc
│   └── __init__.py
├── pyproject.toml
└── tests
    ├── __init__.py
    └── test_project_abc.py

```

Pythonパッケージの標準的なディレクトリ構成で自動的にファイルを作ってくれる。<br />
`pyproject.toml`はPoetryプロジェクトに関するメタデータや依存関係を記述するためのファイル。<br />


#### 既にあるプロジェクトをPoetry管理下におく場合
既にソースコードを描き始めてしまった場合、あるいは既にpipなどで管理しているPythonプロジェクトでpoetryを使い始める場合には`poetry init`を使う。<br />

```
cd project_xyz
poetry init

```
そうすると、対話的に色々聞かれていくのでそれに答えていく。<br />

英語ではあるが、それほど難しくはなく、だいたいは以下の感じで聞かれる。<br />


```
Package name [project_xyz]:
Version [0.1.0]:
Description []:
Author [John Doe <johnd@example.com>, n to skip]:
License []:
Compatible Python versions [^3.9]:

```

上から、パッケージ名、バージョン、説明文、著者、ライセンス、互換性のあるPythonのバージョンになる。<br />

そして、この後に"Would you like to define your main dependencies interactively? (yes/no) [yes]"と聞かれるが、これは「依存関係を今ここで登録する？」という問いである。<br />

後で一つ一つ`poetry add`で追加することができるので、NoでもいいがYesにしてみると、指定の方法について説明がでてきたり、名前を入れると似たような名前のものをサジェストしてくれたりする。<br />
追加するものがない場合には何も入れずにEnterを押すと次に進む。<br />

一通り必要な問いに回答し終えると「これでいい？」と今まで入力したものが確認されて、Enterを押すと完了。<br />
そして、カレントディレクトリに`pyproject.toml`ができる。<br />

#### pyptoject.toml
`poetry new`や`poetry init`した時にできる`pyproject.toml`だが、これは[PEP-518](https://www.python.org/dev/peps/pep-0518/)で定義されたPython標準のフォーマットである。<br />
そのため、Poetryだけではなく例えば最近のpipでもこのファイルを参照してパッケージのビルドやインストールができたりするし、今後対応するツールがもっとでてくる可能性が高い。<br />

これまでは`setup.py`や`requirements.txt`に同じような情報をコピーして持たなければならなかったのが、`pyproject.toml`に集約された感じ。<br />

以下は生成された`pyproject.toml`の例になる。<br />

```
[tool.poetry]
name = "project_abc"
version = "0.1.0"
description = ""
authors = ["John Doe <johnd@example.com>"]

[tool.poetry.dependencies]
python = "^3.9"

[tool.poetry.dev-dependencies]
pytest = "^5.2"

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"

```
### 仮想環境のセットアップ
前の手順でPoetryのセットアップが終わったので、次にこのプロジェクト用の仮想環境を立ち上げる。<br />

```
poetry install
```
上記コマンドを打つと、まずは`./.venv`というディレクト下に仮想環境のセットアップを行い、そこに`pyptoject.toml`に書かれた依存関係（mainとdevelopの双方）のパッケージをインストールしておく。<br />

先ほど、`poetry new`で作った`project_abc`で実行してみると、`pytest`がデフォルトでdevelopment依存関係に入っているので、それがインストールされると思う。<br />

もしdevelopmentの依存関係が不要であれば、、、<br />

```
poetry install --no-dev

```
都することも可能。<br />

### 依存パッケージの追加
`poetry new`した場合や`poetry init`で依存関係の追加をスキップした場合は必要なパッケージの追加をする必要がある。<br />
また、開発途中で必要に応じて依存パッケージを追加することもよくあるので、その時には`poetry add`を使う。<br />

```
poetry add <package-name>

```

pip同様に指定したパッケージだけでなく、それが依存しているパッケージも合わせてインストールしてくれる。<br />

そして、`pyproject.toml`にインストールしたパッケージをバージョンと共に追加してくれる。<br />

```
[tool.poetry.dependencies]
python = "^3.9"
requests = "^2.26.0"

```

ここでバージョンに`^`という記号がついているが、これは許容するバージョンの範囲を示すものである。<br />
例えば`^2.26.0`であれば、`2/26/0 <= version < 3.0.0`の範囲でアップグレード可能ということになる。<br />
ここで用いているのはSemantic Versionという考えかstateあで、バージョンは`majojr.minor.patch`という形で、`minor`と`patch`は値が上がっても後方互換性は維持するということになっている。<br />
したがって、この場合、`major`バージョンの`2`が維持される限りアップグレードしても問題は起こらない前提をおくことができる。<br />
このように、バージョンを範囲で指定できるので、新しいバージョンが出る度に`pyproject.tom`を変更しなくてもいいということになる。<br />

ただそうすると実際に使っている依存パッケージのバージョンが曖昧に成ってしまうという問題がある。<br />
上記の例だと、ある人は`2.26.0`を使い続け、また別の人はその後リリースされた`2.27.0`を使っているということが起こりえる。<br />
その問題を解決するために`poetry.lock`というファイルがある。<br />

`poetry.lock`はpoetryが自動的に生成するファイルの一つ。<br />
実は上で`poetry install`した時に既につくられている。<br />

ここに何が書かれているかというと、今現在仮想環境にインストールされているパッケージのリストとそのバージョンが記載されている。<br />
`pyproject.toml`で指定されているものだけでなくい、それらが依存していうprofilesパッケージも含まれる。<br />


そして、`poetry.lock`ファイルがある場合には、`poetry install`はそちらを先に見にいく。<br />
したがって、例えば`pyproject.toml`に`^2.26.0`と書いてあって、`poetry.lock`に`version="2.27.0"`と書いてあれば、`2.27.0`がインストールされることになる。<br />
チーム開発の場合などは`poetry.lock`をバージョン管理下において、リポジトリ煮込みっとすれば、チームメンバー全員が同じバージョンのパッケージを使って開発をすることができる。<br />

インストールされているパッケージのアップグレード（バージョンアップ）を行い時には`poetry updata`を使う。<br />

```
poetry update --dry-run

```

とするとアップグレードされるパッケージがわかるので、それを確認した上で<br />

```
poetry update

```

すると実際にアップグレードが行われる。<br />
なお、`poetry update`した時に変更されるのは`poetry.lock`だけで`pyproject.toml`はそのままとなる。<br />
新しいバージョンの新機能を使うなどの場合は`pyproject.toml`を手動で修正して依存するバージョンを帰る必要がある。<br />


### 仮想環境で実行

必要な依存パッケージの追加ができたら実行する。カオス環境でPythonのプログラムを実行するには<br />

```
poetry run python <python-file>

```
あるいは、<br />

```
poetry shell
python <python-file>

```

とする。前者は実行の度に仮想環境に入り、終わったら元の世界に戻ってくるという実行方法。<br />
後者は一旦仮想環境に入って、行きっぱなしのまま実行するという方法になる。<br />

なお、仮想環境ではPythonだけでなく、依存関係で追加したパッケージに含まれるコマンドも実行できる。<br />

例えば、`project_abc`で`pytest`によるユニットテストを実行するには、<br />

```
poetry run pytest
```
あるいは<br />


```
poetry shell
pytest

```
とすれば良い。<br />


## 参考文献
[Qiita記事](https://qiita.com/ksato9700/items/b893cf1db83605898d8a)





