# Service層とRepository層の分離（リポジトリパターン）
LaravelプロジェクトでServiceクラスとRepositoryクラスを取り入れた場合、<br />
保守性が向上し、堅牢な実装が可能となるみたいなので、勉強してみる。<br />
## 役割分担
Serviceにはビジネスロジックを、Repositoryにデータ操作のロジックを分ける。
### Service
RepositoryとControllerの間に、ビジネスロジックを管理するための層。<br />
複雑なビジネスロジックをServiceに切り出し、Repositoryはデータの操作のみ行うように分離することで、Repositoryが複雑になることを防止する。<br />
ビジネスロジックがそれほど複雑出ない場合、Service層を実装せずにControllerに実装する方法もあり。
### Repository
DBの操作や外部APIによるデータ・通信や取得などのデータソースへのアクセス部分を記述する。
## ディレクトリ構造（完成形）
appディレクトリ内にServiceとRepositoryを作成し、コントローラを修正する。<br />
※あくまでも、ファイル名は例で引用したもの。
```
app
├ Console
├ Exceptions
├ Http
│ ├ Controllers
│ │ └ SampleController.php
│ ├ Middleware	
│ └ Requests
├ Providers
├ Repositories
│ ├ SampleRepository.php
│ └ SampleRepositoryInterface.php
└ Services
　 ├ SampleService.php
　 └ SampleServiceInterface.php
```
## Serviceクラスの作成
LaravelにはServicesディレクトリが最初は存在しないので、app配下に作成する。<br />
作成できたらインターフェイスと実装クラスを作成。<br />
インターフェイスで定義されたメソッドは派生クラスで全て実装しなくてはならない。<br />
このような実装を矯正することで、実装漏れを防ぎ、実行時に予期せぬエラーを防止することが可能になる。

`app/Services/SampleServiceInterface.php`<br />
`getSample()`を実装するにあたってインターフェースを定義する。
```
<?php
    namespace App\Service;

    interface SampleServiceInterface
    {
        public function getSample($id);

    }
?>
```

`app/Services/SampleService.php`
インターフェースで定義した`getSample()`を`SampleServiceクラス`で実装。
service層では、ビジネスロジックの記述に専念する。
[ビジネスロジックとは](https://qiita.com/kiwatchi1991/items/cd3be76d70f6dfb1f366)<br />
プレゼンテーション層・データアクセス層**以外**らしい。。
```
<?php
    namespace App\Service;

    class SampleService implements SampleServiceInterface
    {
        public function getSample($id)
        {
            // ここに処理を書く
        }

    }

?>
```

## Repositoryクラスの作成
Service同様、インタフェースと実装クラスを作成する。<br />
`app/Repositories/SampleRepositoryInterface.php`
実装するメソッド`getSampleById()`を先にインターフェースで定義する。
```
<?php
    namespace App\Repositories;

    interface SampleRepositoryInterface
    {
        public function getSampleById($id);
    }
?>
```

`app/Repositories/SampleRepository.php`<br />
Repositoryでは、データの操作という役割のみに集中するため、`Sample::find($id)`とEloquentへのアクセス処理のみメソッド内で実装する。
```
<?php
    namespace App\Repositories;

    use App\Models\Sample;

    class SampleRepository implements SampleRepositoryInterface
    {
        public function getSampleById($id)
        {
            return Sample::find($id);
        }
    }
?>
```
## AppServiceProviderに登録
サービスプロバイダに上記設定を登録する。上記の設定をコントローラに反映するための記述をする。
```
public function register()
{
    $this->app->bind(
        \App\Repositories\SampleRepositoryInterface::class,
        \App\Repositories\SampleRepository::class,
    );

    $this->app->bind(
        \App\Services\SampleServiceInterface::class,
        function($app) {
            return new \App\Services\SampleService(
                $app->make(\App\Repositories\SampleRepositoryInterface::class)
            );
        },
    );
}
```
※bindでは、登録されたクラス、またはインターフェースを結合する役割を持つ。

## コンストラクタインジェクション
最後にコンストラクタで依存対象のクラスを渡すように処理を記述。<br />
ServiceクラスにはRepositoryのインターフェースを渡す。<br />
そしてgetSample()でRepositoryのgetSampleById()を呼ぶように処理を追加する。

コンストラクタインジェクションとは<br />
[参考記事](https://qiita.com/minato-naka/items/afa4b930a2afac23261b)

`app/Services/SampleService.php`

```
class SampleService implements SampleServiceInterface
{
    private $sample_repository;

    public function __construct(
        SampleRepositoryInterface $sample_repository
    ) {
        $this->sample_repository = $sample_repository;
    }

    public function getSample($id);
    {
        return $this->sample_repository->getSampleById($id);
    }
}
```

同様にコントローラにServiceのインターフェースを渡すよう追加する。

`app/Http/Controllers/SampleController.php`

```
<?php
    namespace App\Http\Controllers;


    use App\Services\SampleServiceInterface;

    class SampleController extends Controller
    {
        private SampleServiceInterface $sample_service;

        public function __construct(SampleServiceInterface $sample_service)
        {
            $this->sample_service = $sample_service;
        }

        public function get($id)
        {
            $sample = $this->sample_service->getSample($id);

            return view('sample.service', $sample);
        }
    }
?>
```
## 参考URL
[LaravelでService層とRepository層を取り入れる](https://enjoyworks.jp/tech-blog/7743)
[リポジトリパターンとLaravelアプリケーションでのディレクトリ構造](https://qiita.com/karayok/items/d7740ab2bd0adbab2e06)
[【５分でざっくり理解！】Laravelでクリーンアーキテクチャ超入門](https://qiita.com/kiwatchi1991/items/cd3be76d70f6dfb1f366)
[RepositoryパターンにおけるMVC＋Service＋Repositoryの役割をもう一回整理してみる](https://zenn.dev/naoki_oshiumi/articles/0467a0ecf4d56a)
[PHP Dependency Injection依存性注入は難しくない](https://reffect.co.jp/php/dependency-injection-is-not-difficult)

