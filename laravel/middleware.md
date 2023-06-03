# Middlewareについて
Middlewareについて理解が乏しいので備忘録として記録。
## Middlewareとは
アプリケーションに入るHTTPリクエストを検査、フィルタリングするための便利なメカニズムを提供。<br />
例えば、ユーザーが認証されていない場合、ミドルウェアはユーザーをアプリケーションのログイン画面にリダイレクトする。逆に、ユーザーが認証されている場合、ミドルウェアはリクエストをアプリケーションへ進めることを許可する。<br />
それら以外にもさまざまなタスクを実行できる。これらミドルウェアは全て`app/Http/Middleware` ディレクトリにある。
## Middlewareの定義
以下、Artisanコマンドを使用。
```
php artisan make:middleware EnsureTokenIsValid
```
これにより`EnsureTokenValid`クラスを`app/Http/Middleware`ディレクトリ内に配置する。　
## 実装例
## 参考文献
[公式HP](https://readouble.com/laravel/8.x/ja/middleware.html)
