# csv関連の処理
## インポート
// 近日作成予定

## エクスポート
主にコントローラで処理をする実装を行う。
### 実装手順
- 型として書き込み用csvファイルを用意し、
- 出力する対象のデータをループし、`mb_convert_variables()`で文字コードを`UTF-8`から`SJIS`に変換。
- 変換したデータレコードをCSVに`fputcsv()`で落とし込む。
- 全てのレコードをCSVに落とし込んだら、`fclose($f);`で作成したファイルを閉じる。
- 最後にHTTPヘッダを指定して、読み込み用ファイルとしてtest.csvを生成できれば処理完了。

### 実装例
出力するアクションを`postCSV()`として以下実装例を記載する。コントローラ内に実装することを想定。
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;

class UserController extends Controller
{
    public function postCSV()
    {
        $users = User::all();

        // csv出力用のカラムを準備する
        $head = ['名前', '年齢'];

        // 書き込み用ファイルを作成し開く
        $f = fopen('test.csv', 'w');

        if ($f) {
            // カラムの書き込み
            mb_convert_variables('SJIS', 'UTF-8', $head);
            fputcsv($f,$head);
            
            // データを一つずつループして書き込む
            foreach ($users as $user) {
                mb_convert_variables('SJIS', 'UTF-8', $user);
                fputcsv($f, $user);
            }
        }

        fclose($f);

        // HTTPヘッダ
        header("Content-Type: application/octet-stream");
        header("Content-Length:" .filesize('test.csv'));
        header('Content-Disposition: attachment; filename=test.csv');
        readfile('test.csv');

        return view('user.index', compact('users'));
    }
}
```
以上
### 参考URL
[LaravelでCSV出力を行う方法](https://snome.jp/framework/laravel-csv/)
[Laravelで行うCSVダウンロード](https://zenn.dev/qljmssqh/articles/d5f392f9704f4d)
