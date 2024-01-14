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

# UIの記述

## コンポーネント：UIの構成部品
サイドバー、アバター、モーダル、ドロップダウンといったあらゆるUIパーツの裏側では、マークアップがスタイルのためのCSSやユーザー対話のためのJavaScriptと組み合わさりながら働いている。<br />
Reactでは、マークアップ・CSS・JSを独自の"コンポーネント"と呼ばれる、**アプリのための再利用可能なUI要素**にまとめることができる。<br />
プロジェクトが成長するとともに、設計・デザインの色々な部分を既に描いたコンポーネントを再利用することで構築できるようになり、改発速度がアップする。<br />

## コンポーネントの定義
コンポーネントとは、**マークアップを添えることができるJavaScript関数**らしい。<br />
以下のようなコードで実装する。<br />

```javascript
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3Am.jpg"
      alt="Katherine Johnson"
    />
  )
}
```
以下のように作成する。
### コンポーネントをエクスポート
頭にある`export default`は標準的なJS構文であり、これによりファイル内のメインの関数をマークし、後で他のファイルからそれをインポートできるようにしている。<br />

### 関数を定義
`function Profile() {}`のように書くことで`Profile`という名前のJavaScript関数を定義。<br />

> [!NOTE]
> Reactコンポーネントは普通のJavaScript関数ですが、**名前は大文字から始める必要がある**
> でないと機能しないので注意が必要。


### マークアップを加える
上記コードのコンポーネントは`src`と`alt`という属性を有する`<img />`タグを返しているが、HTMLのように書かれているものの、裏では実際にはJavaScript。<br />
この構文は`JSX`と呼ばれるもので、これによりマークアップをJavaScriptないに埋め込めるようになる。<br />
return文は、以下のように一行にまとめても問題はない。<br />

```javascript
return <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />;
```

しかし、returnキーワード同じ行にマークアップ全体が収まらない場合は、**括弧で囲んで以下のように記述する必要がある。**

```javascript return (
  <div>
    <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />
  </div>
);
```

> [!NOTE]
> 括弧がないと、`return`の後にあるコードは全て無視されてしまう。


## コンポーネントを使う。
`Profile`コンポーネントが定義できたので、それを他のコンポーネント内にネストさせることができる。<br />
例えば、`Profile`コンポーネントを複数回使う`Gallery`というコンポーネントをエクスポートすることが可能。<br />

```javascript
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

## ブラウザに見えるもの

大文字・小文字の違いには気を付けること。<br />
- `<section>`は小文字なので、ReactはこれがHTMLタグを指していると理解する。
- `<Profile />`は大文字の`P`で始まっているので、Reactは`Profile`という名前の独自コンポーネントを使うのだと理解する。

`Profile`の中には`<img />`というHTMLがさらに含まれている。最終的にブラウザに見えるのは以下のマークダウンになる。
```html
<section>
  <h1>Amazing scientists</h1>
  <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />
  <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />
  <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />
</section>
```

## コンポーネントのネストと整理方法
コンポーネントは普通のJavaScript関数なので、同じファイルに複数のコンポーネントを書くことも可能。<br />
コンポーネントが比較的小さい場合や違いに密接に関連している場合は便利。<br />

> [!NOTE]
> コンポーネントが他のコンポーネントをレンダーすることはできるが、コンポーネントの定義をネストさせてはいけない。
> ```javascript
> export default function Gallery() {
>   function Profile() {
>     //
>   }
> }
> ```
> 上記のコードはとても遅く、バグの原因になるため、代わりに、全てのコンポーネントをトップレベルで定義するようにすること。
> ```javascript
> export default function Gallery() {
>     //
> }
> 
> function Profile() {
>   //
> }
> ```
> 子コンポーネントが親コンポーネントの情報を必要とする場合は、コンポーネント定義をネストさせるのではなく、Propsを渡すようにする。

## まとめ
- Reactでは、**アプリのための再利用可能な部品**であるコンポーネントを作成することができる。
- Reactアプリでは、UIのあらゆる部品はコンポーネントである。
- Reactのコンポーネントとは普通のJS関数だが、以下の点が異なる。
  - 名前は常に大文字で始まる。
  - JSXマークアップを`return`する。

# コンポーネントのインポートとエクスポート
## ルートコンポーネントファイル

```javascript
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```


上記のファイル自体は`App.js`というファイルにあるため、**ルート（root）コンポーネントファイル**に置かれていることになる。<br />
Next.jsのようなファイルベースのルーティングがあるフレームワークの場合、ルートコンポーネントはページごとに異なるものになる。<br />


## コンポーネントのエクスポートとインポート
コンポーネントは**デフォルトエクスポート**あるいは、**名前付きエクスポート**のいずれかを使い、関数コンポーネントをそのファイルからエクスポートする。<br />
利用する側のファイルでは、エスクポートされたコンポーネントファイルをインポートする。<br />
デフォルトエクスポートされたのか、名前付きエクスポートされたのかに応じて対応するインポート手法を採る。<br />


> [!NOTE]
> `.js`というファイル拡張子が省略された、以下のようなファイルがあるが、Reactでは`./Gallery.js`でも`./Gallery`でも動作する。
> 前者の方が*ネイティブESモジュールの動作*により近い方法らしい。

# JSXでマークアップを記述する

## HTMLをJSXに変換する
以下HTMLがあるとする。<br />

```html
<h1>Hedy Lamarr's Todos</h1>
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  class="photo"
>
<ul>
    <li>Invent new traffic lights
    <li>Rehearse a movie scene
    <li>Improve the spectrum technology
</ul>
```

これをコンポーネントの中に入れたいとする。<br />

```javascript
export default function TodoList() {
  return (
    // ???
  )
}
```
`???`の箇所にコピペしてもうまく動かないらしい。<br />
JSXの方が厳密であり、HTMLよりも若干ルールが多いという理由によるもの。<br />
```javascript
export default function TodoList() {
  return (
    // This doesn't quite work!
    <h1>Hedy Lamarr's Todos</h1>
    <img 
      src="https://i.imgur.com/yXOvdOSs.jpg" 
      alt="Hedy Lamarr" 
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve the spectrum technology
    </ul>
  );
}
```
## JSXのルール
### 単一のルート要素を返す。
コンポーネントから複数の要素を返すには、**それを単一の親タグで囲む。**<br />
例えば、`<div>`を使うことができる。

```html
<div>
  <h1>Hedy Lamarr's Todos</h1>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
  >
  <ul>
    ...
  </ul>
</div>
```
マークアップに余分な`<div>`を加えたくない場合は、代わりに`<>`と`</>`を使うことができる。<br />

```html
<>
  <h1>Hedy Lamarr's Todos</h1>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
  >
  <ul>
    ...
  </ul>
</>
```

この中身のないタグは*フラグメント(Fragment)*と呼ばれるものであり、フラグメントを使えば、ブラウザのHTMLツリーに痕跡を残すことなく、複数の要素をまとめることが可能。<br />

### 全てのタグを閉じる。

JSXではすべてのタグを明示的に閉じる必要がある。<br />
`<img>`のような自動で閉じるタグは`<img />`のようになる。`<li>oranges`のような囲みタグは`<li>oranges</li>`と書かなくてはならない。<br />

```html
<>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
   />
  <ul>
    <li>Invent new traffic lights</li>
    <li>Rehearse a movie scene</li>
    <li>Improve the spectrum technology</li>
  </ul>
</>
```

### （ほぼ）すべてキャメルケースで書く
JSXはJavaScriptに変換され、中に書かれた属性はJavaScriptオブジェクトのキーになる。<br />
コンポーネント内では、これらの属性を変数に読み出したくなることがあるが、JavaScriptの変数名には一点の制約がある。<br />
例えば、名前にハイフンを含めたり、`class`のような予約語を使ったりすることはできない。<br />

Reactでは多くのHTMLやSVGの属性はキャメルケースで書かれる。<br />
例えば、`stroke-width`の代わりに`strokeWidth`を使う。`class`は予約語なので、reactでは`className`を使う。<br />


```javascript
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  className="photo"
/>
```

全リストは[ReactDOMコンポーネントに存在する属性の一覧](https://ja.react.dev/reference/react-dom/components/common)にある。

> [!NOTE]
> 歴史的理由により`aria-*`や`data-*`属性はHTML属性と同じようにハイフン付きで書くことになっている。


## まとめ
- レンダリングロジックとマークアップは互いに関連しているので、Reactではそれらをグループ化する。
- JSXはHTMLと似ているがいくつか違いがある。
- エラーメッセージを見れば、大概はマークアップの修正方法について指針が得られる。

# JSXに波括弧でJavaScriptを含める
JSXでJavaScriptファイル内にHTMLにようなマークアップを書いて、レンダリングロジックとコンテンツを同じ場所にまとめることができる。<br />
マークアップの中でJavaScriptのロジックを書いたり、動的なプロパティを参照したりしたくなることがある。<br />
この場合、JSX内で波括弧を使うことでJavaScriptを使用することができる。<br />

## 引用符で文字列を渡す
JSXに文字列の属性を渡したい場合、シングルクオートがダブルクオートで囲む。<br />

```javascript
export default function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/7vQD0fPs.jpg"
      alt="Gregorio Y. Zara"
    />
  );
}
```

この`src`や`alt`のテキストを動的に指定したい場合は、クオーテーションを`{}`に置き換えることで、JavaScriptの値を使うことができる。<br />

```javascript
export default function Avatar() {
  const avatar = 'https://i.imgur.com/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <img
      className="avatar"
      src={avatar}
      alt={description}
    />
  );
}
```

上記波括弧を使うことで、マークアップの中で直接JavaScriptを使えるようになる。<br />

## 波括弧を使える場所
JSX内部で波括弧を使う方法は2つだけ。<br />

- **テキストとして**、JSXタグの中で直接つかう。<br />
`<h1>{name}'s To Do List</h1>`は動作するが、`<{tag}>Gregorio Y. Zara's To Do List</{tag}>`は動作しない。
- **属性として**、`=`記号の直後に使う。<br />
`src={avatar}`は`avatar`というヘンスを読み出すが、`src="{avatar}"`と書くと"{avatar}"という文字列そのものを渡す。


## 「ダブル波括弧」でJSX内にCSSやその他のオブジェクトを含める

文字列や数字、その他のJavaScriptの式に加えて、オブジェクトをJSXに渡すことも可能。<br />
オブジェクトも波括弧を使って`{ name: "Hedy Lamarr", inventions: 5 }`のように記述するので、これをJSXに渡す時には別の波括弧のペアでラップして`person={{ name: "Hedy Lamarr", inventions: 5 }}`のようにする必要がある。<br />

JSX内でインラインのCSSスタイルを使う時にも`style`属性にオブジェクトを渡す。

```javascript
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Improve the videophone</li>
      <li>Prepare aeronautics lectures</li>
      <li>Work on the alcohol-fuelled engine</li>
    </ul>
  );
}
```
`{{}}`とみた時には、JSXの波括弧の中に書かれたオブジェクトに過ぎないという認識が重要。<br />

> [!NOTE]
> インラインの`style`属性はキャメルケースで書く。
> 例えばHTMLで`<ul style="background-color: black">`となっていれば、コンポーネントでは`<ul style={{ backgroundColor: 'black' }}>`となる。

## まとめ
- 引用符内に書かれたJSX属性は文字列として渡される。
- 波括弧を使えばJavaScriptのロジックや変数をマークアップ内に含めることができる。
- 波括弧はタグのコンテンツの中に使うか、属性の場合は`=`の直後で使う。
- `{{}}`は特別な構文ではなく、JSXの波括弧にくっつくように書かれたJavaScriptオブジェクトである。

# コンポーネントにpropsを渡す
## propsとは
propsとはJSXタグに渡す情報のこと。<br />
例えば、`className`、`src`、`alt`、`width`や`height`は`<img>`に渡すことができるpropsである。<br />

```javascript
function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/1bX5QH6.jpg"
      alt="Lin Lanying"
      width={100}
      height={100}
    />
  );
}

export default function Profile() {
  return (
    <Avatar />
  );
}
```
しかし、`<Avatar />`のような独自のコンポーネントの場合は、任意のpropsを渡してそれをカスタマイズすることが可能。<br />


## コンポーネントにpropsを渡す
以下のコードでは、`Profile`コンポーネントは子コンポーネントである`<Avatar>`に何のpropsも渡していない。<br />

```javascript
export default function Profile() {
  return (
    <Avatar />
  );
}
```
以下の2ステップで`Avatar`にpropsを与えることが可能。<br />

### Step 1: 子コンポーネントにpropsを渡す
まず、`Avatar`に何かのpropsを渡す。<br />
今回は、`person`（オブジェクト）と`size`（数字）を渡してみる。<br />

```javascript
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
```

これでpropsを`Avatar`コンポーネント内から読み出せるようになる。<br />

### Step 2: 子コンポーネントからpropsを読み出す
これらのpropsを読み出すには、`function Avatar`の直後の`({})`内で、コンマで区切って`person, size`のように名前を指定する。<br />
これにより、`Avatar`のコード内で変数と同じようにこれらのpropsが支えるようになる。<br />

```javascript
function Avatar({ person, size }) {
  // person and size are available here
}
```

`Avatar`内に、`person`や`size`を使って何かをレンダーするロジックを書き加えれば完成。<br />

```javascript
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
    </div>
  );
}
```
propsのおかげで、親と子のコンポーネントを独立して考えることができるようになる。<br />
例えば、`Profile`で`person`や`size`を変更するときに`Avatar`内でどう使われるかを気にしなくてよくなる。<br />
同様に、`Avatar`がこれらのpropsをどのように使うのかは、`Profile`を見ずに変更できるようになる。<br />

propsとは自分で調整できるコントローラの**ツマミ**のようなもの。<br />
関数における引数と同じ役割を果たしている。<br />

```javascript
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

通常は`props`オブジェクト全体を必要とすることはないため、個々のpropsへと分割代入する。<br />

> [!NOTE]
> propsを宣言する際は`()`の中に`{}`という**波括弧のペアを描き忘れないように**
> ```javascript
>   function Avatar ({ person, size }) {
>     // ...
>   }
> ```
> この構文は**分割代入**と呼ばれるもので、関数の引数からプロパティを読み出す以下のようなコードと同等。
> ```javascript
> function Avatar(props) {
>   let person = props.person;
>   let size = props.size;
>   // ...
> }
> ```


## propsのデフォルト値を指定する
propsに、値が渡されなかった場合にフォールバックとして使うデフォルト値を指定したい場合、分割代入の中でパラメータ名の後に`=`とデフォルト値を書くことができる。<br />

```javascript
function Avatar({ person, size = 100 }) {
  // ...
}
```

これで、`size`プロパティを指定せずに`<Avatar person={...} />`のようにレンダーされた場合、`size`は`100`にセットされる。<br />

このデフォルト値は`size`がない場合や`size={undefined}`を渡した場合にのみ使用される。<br />
`size={null}`や`size={0}`を渡した場合にはデフォルト値は**使われないことに注意**。

## JSXスプレッド構文でpropsを転送する
propsの受け渡しが冗長な繰り返しになってしまうことがある。<br />
```javascript
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
```
簡潔に書くために以下のような**スプレッド構文**を使って書くとよい。<br />

```javascript
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```
これにより`Profile`に渡されたpropsを、個々の名前を列挙することなくすべて`Avatar`に転送できる。<br />

## childrenとしてJSXを渡す
ブラウザの組み込みタグをネストすることはよくある。<br />

```javascript
<div>
  <img />
</div>
```
同様に独自コンポーネントもネストしたくなることがある。<br />

```javascript
<Card>
  <Avatar />
</Card>
```
このようにJSXタグ内でコンテンツをネストした場合、親側のコンポーネントはその中身を`children`というpropsとして受け取る。<br />
例えば、以下の`Card`コンポーネントは`<Avatar />`がセットされた`children`プロパティを受け取ってラッパdiv要素の内部にそれをレンダーしている。<br />

```javascript
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```
`children`プロパティを有するコンポーネントには、親に任意のJSXで「埋めて」もらうための「穴」が開いている、と考えることができる。<br />

## propsは時間とともに変化する
以下の`Clock`コンポーネントは親コンポーネントから`color`と`time`という2つのpropsを受け取っている。<br />

```javascript
export default function Clock({ color, time }) {
  return (
    <h1 style={{ color: color }}>
      {time}
    </h1>
  );
}
```
上記の例では、**コンポーネントは時間経過とともに別のpropsを受け取る可能性がある**ということを示す。<br />
ここでは、`time`プロパティは毎秒変化するし、`color`プロパティも別の色を選択するたびに変化する。<br />
propsとはコンポーネントの最初の時点ではなく、任意の時点でのコンポーネントのデータを反映するもの。<br />

だが、propsは*イミュータブル*であり、これは「不変」という意味のCS用語である。<br />
コンポーネントのpropsが変わらないといけない場合、親のコンポーネントに別のprops、つまり新しいオブジェクトを渡してもらう必要がある。<br />
古いpropsは忘れられ、使われたメモリはJSエンジンがそのうち回収する。<br />

なので**propsの書き換えを使用としてはいけない。**ユーザーの入力に反応する必要がある場合は、「stateのセット」が必要。<br />

## まとめ
- propsを渡すにはHTMLで属性を書くのと同様の方法でJSX内に記載する。
- propsを呼び出すには、`function Avatar({ person, size })`のような分割代入構文を使う。
- `size = 100`のようなデフォルト値を指定でき、これはpropsがない場合や`undefined`の場合に使われる。
- `<Avatar {...props} />`のようなJSXスプレッド構文ですべてのpropsを転送できる。（多様はしない方がよい）
- `<Card><Avatar /></Card>`のようなネストされたJSXを書くと`Card`コンポーネントの`children`プロパティになる。
- propsとはある時点の読み取り専用のスナップショットである。レンダー毎に新しいバージョンのpropsを受け取る。
- propsを書き換えることはできない。インタラクティブ性が必要な場合はstateを設定する必要がある。

# 条件付きレンダー

## 条件を満たす場合にJSXを返す
複数の`Item`をレンダーする`PackingList`コンポーネントがあるとする。<br />

```javascript

function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

複数の`Item`コンポーネントのうち一部のみで、propsである`isPacked`が`false`ではなく`true`になっていることに注意。<br />
目的は、`isPacked={true}`の場合にのみチェックマークを表示させるようにしたい。<br />
この場合`if/else文`を使って以下のように書くことができる。<br />


```javascript
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```
`isPacked`プロパティがtrueだった場合、このコードは**異なるJSXツリーを返す**。この変更により、一部のアイテムの末尾にチェックマークが表示されるようになる。<br />

```javascript
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✔</li>;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```
[![Image from Gyazo](https://i.gyazo.com/ff9a72356e986f4c36a33f985ac47fdb.png)](https://gyazo.com/ff9a72356e986f4c36a33f985ac47fdb) <br />

条件分岐ロジックを実装するためにJavaScriptの`if`や`return`文を使う。Reactにおいて制御フロー（条件分岐など）はJavaScriptで処理される。<br />

## `null`を使って何も返されないようにする
場合によっては、何もレンダーしたくないことがあるが、コンポーネントは常に何かを返す必要がある。<br />
このような場合、`null`を返すことができる。<br />

```javascript
if (isPacked) {
  return null;
}
return <li className="item">{name}</li>;
```

`isPacked`が`true`の場合、コンポーネントは「何も表示しない」という意味で`null`を返す。<br />
それ以外の場合、レンダーするJSXを返す。<br />

```javascript
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```
実際には、レンダーしようとしている開発者を混乱させる可能性があるため、コンポーネントから`null`を返すことは一般的ではない。<br />

代わりに、親コンポーネント側のJSXで条件付きでコンポーネントを含めたり除外したりすることが多い。<br />

## 条件付きでJSXを含める

