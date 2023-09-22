# React Hook Formについて

## 概要
React Hook FormはReact用のフォームバリーデーションライブラリ。<br />
input要素に入力した値を取得するだけではなくバリデーション機能なども備えており、簡単にフォームを実装することができる。<br />
バリデーションについてはReact Hook Form自身も機能を備えているが、YupやZodを利用することも可能。<br />

ZodはTypeScriptとの相性もいいことから最近では、React Hook Form以外の場所でも利用されるケースが増えてきているらしい。<br />

## 設定方法
### ライブラリのインストール
React Hook Formライブラリをnpmコマンドを使ってインストール。<br />

```
npm install react-hook-form
```

### 利用方法
react-hook-formからuseFormをインストールする。<br />
useFormから戻るオブジェクトは複数のプロパティを持っているが、今回は`register`と`handleSubmit`の利用例をみる。<br />

以下のとおり記述することが可能。<br />

```
import './App.css';
import { useForm } from 'react-hook-form';

function App() {
    const { register, handleSubmit } = useForm();
    const onSubmit = (data) => console.log(data);


    return (
        <div className="App">
            <h1>ログイン</h1>
            <form onSubmit={handleSubmit(onSubmit)}>
                <div>
                    <label htmlFor="email">Email</label>
                    <input id="email" {...register('email')} />
                </div>
                <div>
                    <label htmlFor="password">Password</label>
                    <input id="password" {...register('password')} type='password' />
                </div>
                <button type="submit">ログイン</button>
            </form>
        </div>
    )
}

export default App;

```
#### register関数について
ドキュメントは[こちら](https://react-hook-form.com/docs/useform/register)<br />


register関数の引数にフィールド名を指定することでname(属性), ref, onBlur, onChamgeが戻される。<br />
以下のようなイメージになる。<br />


```
const { name, ref, onChange, onBlur } = register('email');
// 中略
<input 
    id='email'
    name={name}
    onChange={onChange}
    onBlur={onBlur}
    ref={ref}
/>

```
入力を行えばonChangeイベント、input要素からカーソルを外したらonBlurイベントが発火される。<br />

refはReact Hook Formからinput要素への参照としてアクセスする際に利用される。nameはinput要素のname属性に設定される。<br />

## バリデーションの設定
バリデーションは入力した値のチェックを行う機能。<br />

React Hook Formのバリデーションはregister関数で設定することができる。<br />
例えば以下のような実装だと必須入力の設定になる。<br />

```
<input id='email' {...register('email', { required: true })} />
```
設定できるルールは以下のとおり。
- required
- min
- max
- minLength
- maxLength
- pattern
- validate

上記バリデーションに失敗した場合は、onSubmit関数が実行されないことになる。<br />
バリデーションエラーの表示方法は以下の通り<br />

```
import './App.css';
import { useForm } from 'react-hook-form';

function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();

  const onSubmit = (data) => console.log(data);

  return (
    <div className="App">
      <h1>ログイン</h1>
      <form onSubmit={handleSubmit(onSubmit)}>
        <div>
          <label htmlFor="email">Email</label>
          <input id="email" {...register('email', { required: true })} />
          {errors.email && <div>入力が必須の項目です</div>}
        </div>
        <div>
          <label htmlFor="password">Password</label>
          <input id="password" {...register('password')} type="password" />
        </div>
        <button type="submit">ログイン</button>
      </form>
    </div>
  );
}

export default App;
```

上記ではuseForm Hookから戻されるformStateを利用する。<br />
formStateはオブジェクトなのでエラーだけでなくフォームに関する情報を持っている。その情報の中の一つにエラー情報をもつ`errors`がある。<br />

ここではformStateの中のerrorsだけ下記のように設定する。<br />


```
const {
    register,
    handleSubmit,
    formState: { errors },
} = useForm();

```
emailに関するエラーが発生した場合にはerrors.emailオブジェクトにtype, message, refが保存されている。<br />


```
{type: 'required', message: '', ref: input#email}
message: ""
ref: input#email
type: "required"

```

errors.emailを利用してエラーメッセージの表示を設定する。<br />
エラーがない場合には`errors.email`はundefinedになるため、errors.emailが値を持つ場合のみエラーメッセージを表示させる。<br />


```
<form onSubmit={handleSubmit(onSubmit)}>
  <div>
    <label htmlFor="email">Email</label>
    <input id="email" {...register('email', { required: true })} />
    {errors.email && <div>入力が必須の項目です</div>}
  </div>

```
設定後にemailの入力欄が空の状態でログインボタンをクリックするとエラーメッセージが表示されるようになる。<br />
[![Image from Gyazo](https://i.gyazo.com/9543969f9aa0d66ea73824a3a7839bd9.png)](https://gyazo.com/9543969f9aa0d66ea73824a3a7839bd9)


## 参考文献
[reffect記事](https://reffect.co.jp/react/react-hook-form/)
