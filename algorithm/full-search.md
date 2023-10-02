# 全探索について

## 探索アルゴリズムとは
以下のような手法のことを指す。<br />

>
探索アルゴリズムとは、大まかに言えば、問題を入力として、考えられるいくつもの解を評価した後、解を返すアルゴリズムである。（Wikipediaより）


もう少し具体的に書くと、「あり得るパターンを全部列挙する」という手法のことを**全探索**といい、これが探索アルゴリズムの基本である。<br />
また、後述する**二分探索**などを用いて探索回数を減らすアルゴリズムも探索アルゴリズムの仲間である。 <br />


## 探索アルゴリズムの事例

以下の問題を例に説明を進める。<br />

>
N x Nの碁盤目状道路がある。左上座標を(0, 0)、右上座標を(N, N)とするとき、左上座標から右下座標まで同じ交差点を通らずに行くような方法は何通りあるか。


例えば、N = 2の場合は、以下の12通りの生き方が存在する。<br />


[![Image from Gyazo](https://i.gyazo.com/5f2227af0b7cb3102b786bf7ddeda356.png)](https://gyazo.com/5f2227af0b7cb3102b786bf7ddeda356)<br />

動画の解説は[こちら](https://www.youtube.com/watch?reload=9&v=Q4gTV4r0zRs)の動画を参照すると良いかも。<br />

このように全ての行き方（パターン）を列挙することによって通り数などを求めるアルゴリズムを**全探索**という。<br />
また、全探索意外にも様々な探索アルゴリズムが存在する。ちなみに補足ではあるが、N = 3の場合 184通り、N = 4の場合 8512通り...という感じで通り数が指数関数的に増えていく。<br />
N = 6くらいになると全探索でも数秒で実行できなくなるので工夫が必要である。<br />


## 探索アルゴリズムの奥深さ

上記の例については以下の疑問が生じる。<br />

>
1. 単純なforループを回すだけでは書けない。どうやって全探索を実装するのか
2. さらに効率のいい探索アルゴリズムが存在するのか


多くの全探索問題は単純なfor文ループを回すだけで実装することができる。<br />
一方で、上記の問題は複雑な構造を扱うので一筋縄ではいかず、「再帰関数」を用いてやっと解くことができる。<br />


### 探索通り数を減らせないのか？
比較的簡単に改善できる方法が一つある。<br />
N x N の碁盤目状道路は対角線を軸に線対称なので、全体の通り数をS、初手で下向きに進む通り数をTとするとき、必ずS = 2Tとなる。<br />
したがって、全通り探索する必要はなく、最初に下向きに進むものの通り数だけ計算すれば、けんさん時間が1/2に削減できる。それ以外にも様々な改善方法が存在する。<br />
[![Image from Gyazo](https://i.gyazo.com/f5d1092a394ed28bda81e4874395117e.png)](https://gyazo.com/f5d1092a394ed28bda81e4874395117e)<br />


このように、
- 「探索視点」という新たな視点でアルゴリズムを考えられる
- 全探索を実装するだけでも実は単純とは限らない
- 工夫をすると計算時間が減るような様々な探索アルゴリズムが存在する


といった点で探索アルゴリズムは奥深くて面白い。<br />


## すべての基本、全探索

### 全探索とは
全探索は「全てのあり得る通り数を探索する」ということである。<br />
以下のような問題を考えてみる。<br />


>
N個のカードが一列に並んでおり、左からi番目のカードに書かれた数はAiです。N個の中から3つのカードを選び、和をKにするような方法は何通りあるか？



これは三重ループを用いて以下のように綺麗に実装できる。

```
#include <iostream>
using namespace std;

int N, K, A[59];
int cnt = 0;

int main() {
    cin >> N >> K;
    for (int i = 1; i <= N; i++) cin >> A[i];
    for (int i = 1; i <= N; i++) {
        for (int j = i + 1; j <= N; j++) {
            for (int k = j + 1; k <= N; k++) {
                if (A[i] + A[j] + A[k] == K) cnt += 1;
            }
        }
    }
    cout << cnt << endl;
    return 0;
}

```
このようにループを用いてあり得る状態を前通り探索するのが全探索の基本である。

### 全探索では、通り数を見積もることが重要
全探索でも最も重要なことは「通り数をしっかり見積もること」<br />
一般的に、コンピュータでは1秒に10の8乗から9乗程度計算できると言われている。<br />
例えば競技プログラミングだと1 ~ 2秒以内で答えを出さなければならないので、およそ2億〜10億通り程度しか探索できないことが多い。<br />

探索視点のアルゴリズムを書くときには<br />
- 「全部で何通りになるか」を考えること
- 全体の計算回数がどれくらいになるかを考えること

がとても重要になってくる。<br />
一方で、AtCoderやコーディング試験の問題であっても全t何作で解ける問題は多い。<br />

[![Image from Gyazo](https://i.gyazo.com/ab38983507a13d24ceac8fe7a4564084.png)](https://gyazo.com/ab38983507a13d24ceac8fe7a4564084)

### 全探索で問われる基本パターン
以下３パターンがよく問われる。
- 問題文通りに全探索すると解ける問題
- あり得る通り数を全部試すと解ける問題
- 答えを全探索する問題

#### 問題文通りに全探索する
以下の問題を考える。

>
105は奇数であり正の約数を8個持ちます。さて、１以上N以下の奇数のうち、正の約数をちょうど8個持つ数の約数でしょうか？
制約　1 <= N <= 200
出典: [ABC106B - 105](https://atcoder.jp/contests/abc106/tasks/abc106_b)


この問題は問題文通りに１以上N以下の数それぞれについて、条件を満たすかどうか試せばよいだけである。これが全探索の1つ目の考え方である。<br />

このような問題では、、、
- 関数を用いて実装すると、実装とデバッグが楽になることがある
- Nが10の9乗以下などと大きい場合、この手法では間に合わないことがあるので、通り数の見積もりに気をつかう


ことがとても大切である。実装例（C++）は以下のようになる。<br />


```
#include <iostream>
using namespace std;

bool solve(int n) {
    int cnt = 0;
    for (int i = 1; i <= n; i++) {
        if (n % i == 0) cnt += 1;
    }
    if (cnt == 8 && n % 2 == 1) return true;
    return false;
}

int main() {
    int N, ans = 0;
    cin >> N;
    for (int i = 1; i <= N; i++) {
        if (solve(i) == true) ans += 1;
    }
    cout << ans << endl;
    return 0;
}

```


## 参考文献
[Qiita記事](https://qiita.com/e869120/items/25cb52ba47be0fd418d6)<br />