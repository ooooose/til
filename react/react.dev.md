# React公式ドキュメントメモ

以下URLからReactの必要な知識をまとめる。
- [ja.react.dev](https://ja.react.dev/)


## コンポーネントとは
Reactを構成する部品のようなもので、独自のロジックと外見を持つUIの部品を指す。<br />
コンポーネントの大きさは大小あり、小さなボタンのようなものから、ページ全体を表す大きいものである場合もある。<br />

Reactにおけるコンポーネントとは、マークアップを返すJS関数を指す。<br />

```javascript
function MyButton() {
  return (
    <button>I'm a button</button>
  )
}
```
上記の`MyButton`を宣言したら、別コンポーネントにネストできる。

```javascript
export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

`<MyButton />`が大文字で始まることに注意。このような表記にすることで、Reactのコンポーネントであることを指している。<br />
Reactのコンポーネント名は常に大文字で始める必要があり、HTMLタグは小文字である必要がある。<br />

```javascript

function MyButton() {
  return (
    <button>
      I'm a button
    </button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

`export default`キーワードは、ファイル内のメインコンポーネントを指している。<br />

## JSXとは
上記のマークアップ構文は、*JSX*と呼ばれるもので、使用そのものは任意。<br />
コンポーネントを返す構文は基本的にJSXの拡張子をつけたファイルで展開する。<br />

JSXはHTMLより構文が厳格であり、`<br />`のようにタグを閉じる必要がある。<br />
また、コンポーネントは複数のJSXタグを`return`することはできない。<br />
`<div>...</div>`や空の`<>...</>`ラッパのような共通の親要素で囲む必要がある。（2つの親要素を展開することは不可）<br />

```javascript
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  );
}
``` 

## スタイリング
Reactでは、CSSクラスを`className`で指定するとHTMLの`class`属性と同じ方法で動作する。<br />
```html
<img className="avatar" />
```

別のCSSファイルに対応するCSSルールを記述することも可能。<br />
```css
.avatar {
  border-radius: 50%;
}
```
`CSS Modules`といったWebpackのローダーにより、CSSのクラスをJSで読み込むという戦略もある。<br />


## データの表示方法
JSXクォ使うことで、JavaScript内にマークアップを入れることができる。<br />
波括弧{}を使うことで、コード内の変数を埋め込みユーザーに表示することができる。<br />

```javascript
return (
  <h1>
    {user.name}
  </h1>
)
```
JSXの波括弧の中にもっと複雑な式を入れることも可能。<br />

```javascript
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```

上記の`style={{}}`は`style={}`というJSXの波括弧内にある通常の`{}`オブジェクト。<br />
スタイルがJavaScript変数に依存する場合は、`style`属性を使うことができる。<br />

## 条件付きレンダー
Reactには条件分岐を書くための特別な構文は存在せず、JavaScriptコードを書く時に使うのと同じ手法を使う。<br />
例えば、`if`ステートメントを使って条件付きでJSXを含めることが可能。<br />


```javascript
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}
return (
  <div>
    {content}
  </div>
);
```
条件演算子の使用も可能。<br />

```javascript
<div>
  {isLoggedIng ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```

`else`側の分岐が不要な場合は、短い`論理構文（&&）`を使用することも可能<br />

```javascript
<div>
  { isLoggedIn && <AdminPanel /> }
</div>
```

## リストのレンダー　
コンポーネントのリストをレンダーする場合は、`for`ループや配列の`map()`関数といったJavaScriptの機能を使って行う。<br />
例えば、以下のような商品の配列があると仮定。<br />
```javascript
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```

コンポーネント内で、`map()`関数を使って商品の配列を`<li>`要素の配列に変換する場合は以下のコードで実現可能。<br />

```javascript
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```
ここで`<li>`に`key`属性があることに注意。リスト内の各項目には、兄弟の中でそれを一意錦別するための文字列または数値を渡す必要がある。<br />
通常、keyはデータからくるはずでデータベース上のIDなどが該当する。<br />
Reactは、後でアイテムを挿入、削除、並び替えることがあった際に、何が起こったかを`key`を使って把握する。<br />

## イベントに応答
コンポーネントの中で*イベントハンドラ関数*を宣言することで、イベントにも応答可能。<br />

```javascript
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

`onClick={handleClike}`の末尾には括弧がないことに注意。<br />
これはイベントハンドラ関数を呼び出すわけでなく、渡すだけであり、ユーザーがボタンをクリックした時に、Reactがイベントハンドラを呼び出す。<br />

## 画面の更新
コンポーネントに情報を記憶させて表示したいことがある。<br />
この場合にコンポーネントに*State*を追加する。<br />

まず、Reactから`useState`をインポートする。<br />

```javascript
import { useState } from 'react';
```

これにより、コンポーネント内にstate変数を宣言することができる。<br />

```javascript
function MyButton() {
  const [count, setCount] = useState(0);
  // ...
```

`useState`からは現在のstateと、それを更新するための関数の2つが得られる。<br />
命名的な慣習としては`[something, setSomething]`のように記述する。<br />

## フックの使用
`use`で始まる関数は、フック（Hook）と呼ばれる。<br />
`useState`はReactが提供する組み込みのフック。<br />
[APIリファレンス](https://ja.react.dev/reference/react)で他の組み込みフックをみることも可能。<br />
また、既存のフックを組み合わせて独自のフックを作成することもできる。<br />

フックには通常の関数より多くの制限がある。<br />
- フックはコンポーネントの*トップレベル*でのみ呼び出すことができる。
- 条件分岐やループの中で`useState`を使いたい場合は、新しいコンポーネントを抽出してそこに配置する。


## コンポーネント間でデータを共有
`state`などをコンポーネント間で共有し、常に一緒に更新したいということもよくある。<br />
この場合、上位のコンポーネントに`state`の定義を移動し、*props*としてデータ（`state`）の受け渡しを行うことができる。<br />
これは更新関数も同様。<br />

