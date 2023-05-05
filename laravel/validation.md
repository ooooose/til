# バリデーション設定
バリデーションの設定方法について列記する。<br />
今回はstoreメソッドで新規登録する際のバリデーションについて記載する。
## Requestファイルの作成
### ファイルの生成
まずはバリデーションのルールを記載するrequest層のファイルを作成するために以下のコマンドを実行。<br />
今回は例としてファイル名を`StoreContactRequest.php`とする。
```
php artisan make:request StoreContactRequest
```
### 作成時ファイルの中身
`app/Http/Requests/`配下に作成されており、以下の内容が記載されている。
```
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreContactRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return false;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, mixed>
     */
    public function rules()
    {
        return [
            //
        ];
    }
}
```
### バリデーションのルール設定
バリデーションを実装するために以下２ヶ所を編集する。設定しているルールはUdemy教材を参考に作成。
```
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreContactRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;   //falseをtrueにする。
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, mixed>
     */
    public function rules()
    {
        return [
            'name' => ['required','string','max:30'], //バリデーションのルールを設定
            'title' => ['required','string','max:50'],
            'email' => ['required','email','max:255'],
            'url' => ['url','nullable'],
            'gender' => ['required','boolean'],
            'age' => ['required'],
            'contact' => ['required','string','max:200'],
            'caution' => ['required','accepted']
        ];
    }
}
```
## Controllerに追加
コントローラに上記Requestファイルを反映する。以下２ヶ所を変更・追加する。
`app/Http/Controllers/ContactFormController.php`
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\ContactForm;
use App\Services\CheckFormService;
use App\Http\Requests\StoreContactRequest; //useでインポートする

class ContactFormController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $contacts = ContactForm::select('id', 'name', 'title', 'created_at')->get();
        return view('contacts.index', compact('contacts'));
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        return view('contacts.create');
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(StoreContactRequest $request) //RequestからStoreContactRequestに変更
    {
        ContactForm::create([
            'name' => $request->name, 
            'title' => $request->title, 
            'email' => $request->email, 
            'url' => $request->url, 
            'gender' => $request->gender, 
            'age' => $request->age, 
            'contact' => $request->contact, 
        ]);

        return to_route('index');
    }
```
## ビューへの反映
あくまでも一例だが、以下のテンプレを挿入すればバリデーションに引っかかった時に赤文字で警告文が出るようになる。
```
@if ($errors->any())
    <div class='text-red-500 mx-auto text-center'>
        <ul>
            @foreach ($errors->all() as $error)
                  <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```
updateメソッドにも適用したい場合は、UpdateContactRequest.phpのようなファイルを別途作成して設定する。

