# `canvas`の描画機能について

## `canvas`とは
`Canvas API`では、`JavaScript`と`HTML`の`<canvas>`要素によってグラフィックを描く方法が提供されている。<br />
全体的に2Dグラフィックを対象としている。<br />


## 基本的な例

以下の例ではキャンバス上に緑の四角が描画される。<br />
### HTML
```html
<canvas id="canvas"></canvas>
```

### JavaScript
`Document.getElementById()`メソッドで、HTMLの`<canvas>`要素への参照を取得する。<br />
`HTMLCanvasElement.getContext()`メソッドで対象のコンテキストを取得。ここに描画されたものが表示される。<br />

```javascript

const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

ctx.fillStyle = "green"; // 塗りつぶし
ctx.fillRect(10, 10, 150, 100); // 形状を調整
```

## パスを描く
パスは点のリストであり、さまざまな形、曲線、幅、色の線分によって結ばれている。<br />
パスを使って描画するにはいくつかの手順を踏む。<br />

- はじめに、パスを作成する。
- 次にパスを描画するために[描画コマンド](https://developer.mozilla.org/ja/docs/Web/API/CanvasRenderingContext2D#paths)を使用。
- パスを作成したら、パス塗りつぶしで描くことができる。


上記ステップで使用する関数は以下の通り。<br /> 

### `beginPath()`
新しいパスを作成する。パスを作成すると以降の描画コマンドは、そのパスを構築するために直接作用する。<br />

### `closePath()`
直線をパスに追加し、現在のサブパスの開始点につなぐ。<br />

### `stroke()`
輪郭をなぞる方式で、図形を描く。

### `fill()`
パスの内部領域を塗りつぶして、単色の図形を描きます。<br /> 



## 特定領域の塗りつぶし
絵を各機能で、特定の領域を単一色で塗りつぶしたいケースは一定存在する。<br />

以下のステップで塗りつぶしの実装ができるのではないだろうか？
- 一番内側の線で囲まれた領域の特定
- `fill()`による塗りつぶし

アルゴリズムの種類を調べたら`Bucket Fill Algorithm`など色々ありそうだったので、考察用に記事を残します。<br />

- [【JavaScript】Bucket Fill Algorithm【Canvas】](https://qiita.com/PG0721/items/fd5cd16dd80b8dfa221d) <br />
- [塗りつぶしアルゴリズムを JavaScript / canvas で実装してみた](https://qiita.com/zaru/items/29ea3c76b66eb1c1ac8b)

### 一番内側で囲まれた線の特定（塗りつぶし箇所の特定）


### `fill()`による塗りつぶし

