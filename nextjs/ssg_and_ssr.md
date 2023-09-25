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



## 参考文献
[zenn記事](https://zenn.dev/luvmini511/articles/1523113e0dec58)
