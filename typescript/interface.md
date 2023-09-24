# Interfaceとは
## 概要
オブジェクトの型に名前をつけること。<br />
一般的に手っ取り早く型を定義できる方法は下の実装になる。<br />


```
let level: number = 15;

level = 15

level = "十五" //Type 'string' is not assignable to type 'number'
```

上記実装のように宣言時の変数に型の注釈をつけることを型アノテーション（`Type Annotation`）という。<br />
上記コードでも問題ないが、実装が複雑になったら問題がある。<br />

```
let apple: { nickName: string; isHuman: boolean; level: number } = {
  nickName: "りんご",
  isHuman: true,
  level: 0
};

```
このようにオブジェクトの型を定義する時に型アノテーションでやると面倒になるため、`interface`を使用する。<br />


## 使用方法
以下実装例<br />
```
interface Member {
  nickName: string;
  isHuman: boolean;
  level: number;
}

let apple: Member = {
  nickName: "りんご",
  isHuman: true,
  level: 0
};

apple.isHuman = "yes" //Type 'string' is not assignable to type 'boolean'

```

変数の隣に型名を書けば済むので、先ほどの型アノテーションより大分楽。

## Typeとの相違点
型に名前をつけられる方法として`type`もあるが、正式には型エイリアス（`Type Alias`）という名称らしい。


```
type Member = {
  nickName: string;
  isHuman: boolean;
　level: number;
};

let apple: Member = {
  nickName: "りんご",
  isHuman: true,
  level: 0
};

apple.isHuman = "yes" //Type 'string' is not assignable to type 'boolean'

```
`type`は`interface`と似ているが、以下の違いがる。<br />

### 宣言と代入
`interface`は型の宣言なので、**型に名前をつけることが可能**。一方で、`type`は**無名で作られた型に参照のため別名を与えるということをやっている**。

```
interface book {
  title: string;
  pages: number;
}

type book = {
  title: string;
  pages: number;
};

```
上記のコードで`interface`にはセミコロンがなく、`type`にはセミコロンがついている。<br />
`interface`構文はブロック{}で終わる分なのでセミコロンが不要で、`type`は最終的に代入分になるのでセミコロンが必要になる。<br />

```
function dummy () { ... }
const dummy = function () { ... };

```

### 定義できる型の種類
`interface`では**オブジェクトとクラスの方だけ定義できる**が、`type`では**他の型も参照できる**。<br />


```
type Color = "白" | "黒" | "赤" | "緑";

let color: Color = "白";
color = "青"; //Type '"青"' is not assignable to type 'Color'.
```
オブジェクト以外の型も参照できるので、`type`では上記の実装も可能となる。

### 拡張
`interface`は**拡張ができる**。

```
interface User {
  name: string;
}

interface User {
  level: number;
}

const user: User = {
  name: "apple",
  level: 0
};

const user2: User = {
  name: "banana"
}; //Property 'level' is missing in type '{ name: string; }' but required in type 'User'

```

上記コードをみると、再宣言しているように見えるが実際には新しいプロパティの型定義を追加している。<br />
そんため、`user2`では`levelプロパティ`がないと怒られている。<br />

`type`では**このような拡張はできない**。<br />

```
type User = {
  name: string;
} //Duplicate identifier 'User'

type User = {
  level: number;
} //Duplicate identifier 'User'

```

## どちらを使うか？
### interface派
TSに関する記事の多くでは、`interface`を使うようにといっているものも多い。<br />
根拠としては**interfaceの持つ拡張性**が挙げられる。<br />
[公式ドキュメント](https://www.typescriptlang.org/docs/handbook/advanced-types.html#interfaces-vs-type-aliases)でも`interface`の方が**拡張にオープンなJavaScriptのオブジェクト動作方式に似ている**という理由で`interface`をおすすめしている様子。

>
Because an interface more closely maps how JavaScript objects work by being open to extension, we recommend using an interface over a type alias when possible.
### type派
以下実装を想定してみる。<br />


```
interface Shoes {
  size: number;
  color: string;
}


// コード1万行


const heel: Shoes = {
  size: 235,
  color: "red"
}; //Property 'isSecondhand' is missing in type '{ size: number; color: string; }' but required in type 'Shoes'

```
ちゃんと`Shoes`の型に合わせてオブジェクトを宣言しているのに、なぜかエラーが起きている。<br />
コード1万行の中に以下の処理が入ってしまっていた。<br />


```
interface Shoes {
  isSecondhand: boolean;
}

```

このように、拡張できるというのはある意味**知らないうちに拡張されている可能性がある**ことを示す。<br />
`type`は`interface`のような拡張はできないが、先述のような拡張ができないだけで**拡張自体は可能**<br />


```
type ErrorHandling = {
  success: boolean;
  error?: { message: string };
};

type ArtistsData = {
  artists: { name: string }[];
};

type ArtistsResponse = ArtistsData & ErrorHandling;

const dummyData: ArtistsResponse = {
  artists: [{ name: "apple" }, { name: "banana" }],
  success: true
};

```
上記のように`&`を使用したら既存のタイプを組み合わせることも可能。<br />

ちなみに全く同じ例を`interface`で書き換えたら以下の実装になる。<br />


```
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtistsData {
  artists: { name: string }[];
}

interface ArtistsResponse extends ErrorHandling, ArtistsData {}

const dummyData: ArtistsResponse = {
  artists: [{ name: "apple" }, { name: "banana" }],
  success: true
};

```

拡張の場合、`type`で表現した方が直感的でわかりやすいような気もする。<br />


`interface`でできることが大体`type`でもできるし、`type`の方が表現できる範囲が広いし、知らないうちに拡張されたくないから最初は`type`でよいという理由が`type`派の理由。<br />


## 参考文献
[Qiita記事](https://qiita.com/nogson/items/86b47ee6947f505f6a7b)
[zenn記事](https://zenn.dev/luvmini511/articles/6c6f69481c2d17)
