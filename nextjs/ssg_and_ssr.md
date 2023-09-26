# SSRとは
## Pre-renderingについて
SPAではページをロードする時、まず空のhtmlを読み込んでJSファイルも読み込み、そのJSが画面をレンダリングする。<br />
このやり方では、SEO対策においてデメリットがあったりファーストビューが遅くなったりする問題がある。<br />


この問題を解決するため、Next.jsは基本的に全てのページを`Pre-rendering`する。<br />
これはクライアント側のJSがレンダリングする代わりに、**各ページに対してhtmlを予め作っておくことを**意味する。<br />
そうすると**パフォーマンスでもSEOでもより良い結果が出せる。**<br />


生成されたhtmlは最小限のJSコードに繋げられる。ページがブラウザにロードされるときにそのJSコードも実行され、ページは完全にインタラクティブになる。(このプロセスを`Hydration`と呼ぶ)<br />


[公式サイト]()の絵がわかりやすいので以下添付する。<br />


[![Image from Gyazo](https://i.gyazo.com/e314fd580c42424198f33e3f0d24f2e2.png)](https://gyazo.com/e314fd580c42424198f33e3f0d24f2e2) <br />


[![Image from Gyazo](https://i.gyazo.com/fd455c6017942106802338ebf1c640a8.png)](https://gyazo.com/fd455c6017942106802338ebf1c640a8)<br />


何らかのデータを読み込む時、PlainなReactだと`useEffect`を使用するケースが多いが、そうすると下の絵のように最初一瞬空のページがパチっと見えてしまう。ローディング画面で誤魔化すこともできるが、Pre-renderingするとファーストビュー自体が早くなるメリットがある。<br />



もちろんファーストビューが表示されてもJSがロードされる前の間はインタラクティブではないという注意点もあるが、画面にコンテンツが表示されうのが早い時点でユーザーの離脱を防ぐ効果がある。<br />


そしてこの`Pre-rendering`にも`SSG(Static Site Generation)`と`SSR(Server-side Rendering)`の２種類が存在する。<br />



## SSG

### SSGとは
SSGはStatic Site Generationの略で、言葉どおり**静的サイトを生成するPre-rendering**のことを指す。この方式では**htmlがビルド時に生成され、各リクエストに再利用される。** <br />
最初にページがロードされる時にすぐhtmlを作るということ。<br />

[![Image from Gyazo](https://i.gyazo.com/ce2d84ab1d51e7a4c158cc6015332334.png)](https://gyazo.com/ce2d84ab1d51e7a4c158cc6015332334)<br />


### getStaticProps
もしロードするページが外部データに全く依存していないならデータをフェッチする必要もないので、ただたんに静的なhtmlを生成すればよい。<br />

しかし、**外部データに依存する時**もある。例えば、ブログページで記事リストを読み込むなどが該当する。<br />

こういう時は`getStaticProps`関数を使用する。サンプルコードは下のとおり。<br />


```
function Blog({ posts }) {
   return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// この関数はビルド時に実行される
export async function getStaticProps() {
  // posts を取得するため外部 API endpoint を読み込む
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // { props: { posts } }を返すことで、
  // Blog コンポーネントはビルド時に`posts`を prop として受け取る
  return {
    props: {
      posts,
    },
  }
}

export default Blog

```
このように同じファイルで`getStaticProps`を使うと外部データをpropsとして渡すことができる。

### 使う場面
SSGはビルド時にデータを読み込むが、これは**ビルド時1回だけデータフェッチして、ビルドされた後にデータを変更することができない**という意味でもある。<br />
なので、SSGはビルド後にデータを変更する必要がないページに向いている。<br />

[公式サイト](https://nextjs.org/docs/basic-features/pages#when-should-i-use-static-generation)によると「ユーザーのリクエストより先にこのページをPre-renderingすることができますか？」という質問にYESの時SSGを使うようにと言及されている<br />

以下の場合が当てはまる。<br />

- マーケティングページ
- ブログ記事
- ECサイト商品リスト
- ヘルプページ・ドキュメント

確かに、ブログの記事やヘルプページなど最初に読み込んだら別にデータ変更する必要などない。<br />
**ユーザーのリクエストなしでレンダリングできない場合にSSGはいい線stateなくでない**ことにも気づく。<br />

この場合使える方法の一つは、SSGとCSR(`Client-side Rendering`)を一緒に使うことが挙げられる。<br />

ページの中で一部だけPre-renderingスキップして、そこはクライアント側のJSで読み込む方法である。<br />
もう一つの方法にSSR（Server-side Rendering）を使うことが挙げられる。

## SSR

### SSRとは
SSRはServer-side Renderingの略であり、この方法ではhtmlが各リクエストの度に生成される。<br />


[![Image from Gyazo](https://i.gyazo.com/422639b0c6dcae196362f7d2bea4bdcb.png)](https://gyazo.com/422639b0c6dcae196362f7d2bea4bdcb)<br />

リクエストが起こる度にhtmlを生成するので当然SSGより遅いが、Pre-renderingされたページを常に最新状態に維持することができるというメリットがある。<br />


### getServerSideProps
SSRでは`getServerSideProps`という関数を使う。<br />
Next.js 9.3以前のバージョンで使われていた`getInitialProps`とほぼ同じ動きらしい。

```
function Page({ data }) {
  // Render data...
}

// すべてのリクエストの度に実行される
export async function getServerSideProps() {
  // 外部APIからデータフェッチ
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // props を通じて Page に data を渡す
  return { props: { data } }
}

export default Page

```
使用方法は`getStaticProps`とほぼ同じ。違いは`getServerSideProps`はビルド時ではなく、すべてのリクエストの度に実行されるということ。<br />

### 使う場面
SSRは**ユーザーのリクエストなしではPre-renderingできないページ、最新状態を維持する必要があるページ**に向いている。<br />

SNSのタイムラインページのように、ユーザーによってデータが変わり画面も変わるのでレンダリングの前にユーザー情報を読み込む必要がある。<br />
なので、リクエストなしではレンダリングできない。<br />

つまり、タイムラインやマイページなど接続するユーザーによって変わる画面はSSRが適切である。<br />


そして、Twitterを考えてみると何蚊をつぶやいた時にすぐタイムラインに反映されるが、ページを常に最新状態に維持しているということ。<br />

SSRはSSGと違って**ビルドされた後にもデータを更新できるのでこういうページに向いている。**<br />

## まとめ
Pre-renderingには2種類あるが、一つに統一する必要はなく、あるページはSSGにして他はSSRにすることもできる。<br />

[![Image from Gyazo](https://i.gyazo.com/ee41f7ec24adfc2b8935a6aca6bf88e8.png)](https://gyazo.com/ee41f7ec24adfc2b8935a6aca6bf88e8)<br />


各ページの特性を考えて、向いている方法でカスタムすればハイブリッドなNext.jsアプリケーションが作れる。<br />
「SSGとSSRの中でどちらを優先して使うべきか？」という問いについて、公式サイトではパフォーマンス的観点からSSGをお勧めしている。<br />

>
We recommend using Static Generation over Server-side Rendering for performance reasons. Statically generated pages can be cached by CDN with no extra configuration to boost performance.


SSRは全てのリクエストの度に処理が走るので、ビルド時に生成したページをキャッシングして再利用するSSGより遅いことは当然である。<br />
しかし、最近のウェブアプリケーションで**ビルド時にデータ更新しないし、ユーザーによって画面が変わることもないページはそう多くはないのでは？**という気持ちもある。<br />

>
However, in some cases, Server-side Rendering might be the only option.


上記公式サイトの引用のように、SSRが唯一の手かも！と言っている。<br />



## 参考文献
[zenn記事](https://zenn.dev/luvmini511/articles/1523113e0dec58)
