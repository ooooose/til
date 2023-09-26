# ジェネリクス（Generics）について
## ジェネリクスとは
TypeScriptにおけるジェネリクスとは、型の安全性とコードの共通化を両立させるために導入された言語仕様のこと。<br >
これを用いることで、**型の安全性とコードの共通化を両立できる。**


## 実装例
ジェネリクスが具体的にどのように使われているかを実装例を見ながら確認していく。<br />

```
function sampleLottery(v1: string, v2: string): string {
     return Math.random() <= 0.5 ? v1 : v2 
}

```
上記は`sampleLottery()`関数で50%の確率で第一引数あるいは第二引数をランダムで返す。<br />
この関数は、文字列の抽選によって以下のように再利用可能である。<br />


```
const lottery1 = sampleLottery("あたり", "はずれ")
```

次に、文字列だけはなく数値のランダムな抽出も同じ関数を使って実装する場合を想定する。<br />
`sampleLottery()`関数は文字列にしか対応していないので、数値を入れるための新しい関数を別途で用意しなくてはならない。<br />


```
function sampleLottery(v1: number, v2: number): number {
     return Math.random() <= 0.5 ? v1 : v2 
}
const num: number = sampleLottery(1, 3)
```

さらに、`sampleLottery()`関数のロジックを応用して、`Person`オブジェクト向けの実装を作ることにもなった。<br />


```
function sampleLottery(v1: Person, v2: Person): Person {
     return Math.random() <= 0.5 ? v1 : v2 
}
const num: number = sampleLottery(person1, person2)
```

このように考えると、引数の型が異なる`sampleLottery()`関数が3つも複製されることになり冗長である。<br />

```
// NOTE: 引数のデータ型が異なる3つの同じ関数が出力される。できるだけ重複したコードは１つに統一したい...
function sampleLottery(v1: string, v2: string): string {
     return Math.random() <= 0.5 ? v1 : v2 
}

function sampleLottery(v1: number, v2: number): number {
     return Math.random() <= 0.5 ? v1 : v2 
}

function sampleLottery(v1: Person, v2: Person): Person {
     return Math.random() <= 0.5 ? v1 : v2 
}

```

引数をまとめて、1つの関数でこれらを表現するにはどうすればいいか？<br />

考えられる方法として、データ型を`any`にすることが考えられる。<br />
しかし、この方法には致命的な欠陥があり、戻り値の方も`any`として出力されるのでコンパイラがチェックしない。バグを生みやすく型の安全性が損なわれてしまう。<br />


```
// 実行するとバグが発生するサンプルコード
function sampleLottery(v1: any, v2: any): any {
     return Math.random() <= 0.5 ? v1 : v2 
}

const str = sampleLottery(1, 2)
str = str.toLowerCase() // この行でTypeErrorが出力されてしまう

```

上述は、`sampleLottery()`関数に`number`型を渡しているものの、戻り値は`string`型として扱っている。<br />
このコードはコンパイルエラーにならないものの、コンパイル後のエラーを出力すると5行目で`TypeError: str.toLowerCase is not a function`というエラーが出力されてしまう。<br />

**ここで、コードの共通化と型の安全性の両方を達成するためにジェネリクスが採用される**。<br />
ジェネリクスの発想は非常にシンプルで、**型も変数のように扱えるようにすること**である。<br />
汎用的なコードを多種多様な型で使えるようにするためにジェネリクスが使われている。<br />

上述の`sampleLottery()`関数はジェネリクスを使って書き直すと以下のようになる。<br />

```
// NOTE: 同じ引数を使うので、型変数の名前をTと表記。
// このように書くことで、似たような関数を重複して書くことを防いでいる。
function sampleLottery<T>(v1: T, v2: T): T {
  return Math.random() <= 0.5 ? v1 : v2 
}
sampleLottery<string>('あたり', 'はずれ')
sampleLottery<number>(1, 2)
sampleLottery<Person>(person1, person2)
```

前述の実行するとバグが発生するコードに、ジェネリクス化した`sampleLottery()`関数を使って書いてみると以下のようになる。<br />

```
function sampleLottery<T>(v1: T, v2: T): T {
     return Math.random() <= 0.5 ? v1 : v2 
}

// この変数宣言文にコンパイルエラーが出力される。
const str = sampleLottery<string>(1, 2)

str = str.toLowerCase()

```
上記サンプルコードの次の変数宣言文である。<br />


```
const str = sampleLottery<string>(1, 2)
```

こちらに`Argument of type '1' is not assignable to parameter of type 'string'.`というように出力されるが、このようにジェネリクスを使って書くと、`string`型を入れなければならないところに`1`を代入しているバグ2期づことができる。<br />

このようにして、ジェネリクスはコードの共通かと型の安全性を両立してくれる言語機能である。<br />
コードを使い回すときに、多種多様な型で使えるようにしたい場合は、ジェネリクスを検討してみると良い。<br />

## 主なサンプルコード
### 複数のデータ型の値を出力する

```
function printData<A, B>(data1: A, data2: B) {
      console.log(`出力したデータ：{$data1}、{$data2}`)
}

printData('Hello', 'World')
printData(100, ['hello', 100])

```

### インターフェイスとジェネリクスを融合させる
TypeScriptのジェネリクスはインターフェイスと組み合わせて使うことができる。<br />

```
// 型を変数として扱うことで、どのような型にも対応できるインターフェイスを作る
interface UserData<X,Y> {
    name: X 
    personNo: Y 
}

const user: UserData<string, number> = {
    name: "Shota", // nameはstring型
    personNo: 1 // personNoはnumber型
}

```
上記では、`<string, number>`が`UserData`というインターフェイスに渡されている。<br />
このように、`UserData`はユースケースに応じて任意のデータ型を割り当てることができる、再利用可能なインターフェイスとなる。<br />


### クラス


```
// Queueクラスで作成されるリストに<T>と表記することで、格納できるデータ型を指定できる。
class Queue<T> {
  private data = [] 
  push(item: T) { this.data.push(item) }
  pop(): T | undefined { return this.data.shift() }
}

// この変数宣言文のように、予め格納するデータ型を指定できる。
const queue = new Queue<number>() 
queue.push(0) 

```

## まとめ

- コードの共通かをすると、型の安全性が弱まり、型の安全性を高めるとコードの共通かが難しくなる。**このようなトレードオフの関係ではなく、両者を両立させるため**にジェネリクスという言語仕様がある。
- ジェネリクスは、コードの共通かと型の安全性を両立させるための言語仕様である。
- ジェネリクスは、型を引数のように扱うという発想をベースに作られている。


## 参考文献
[zenn記事](https://zenn.dev/nameless_sn/articles/ts_generics_tutorial)
