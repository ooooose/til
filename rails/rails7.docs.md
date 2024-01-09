# rails7について

## import maps
`import maps`はJavaScriptモジュールをブラウザで直接importできる。<br />
rails7からデフォルトになっており、トランスパイルやバンドルの必要なしにほとんどのNPMパッケージを用いて誰でもモダンなJavaScriptアプリケーションを構築できるようになる。<br />


import mapsを利用するアプリケーションは`Node.js`や`Yarn`なしで機能する。<br />
RailsのJavaScript依存関係を`importmap-rails`で管理する予定であれば、Node.jsやYarnをインストールする必要はない。<br />

import mapsを利用する場合、別途ビルドプロセスを実行する必要はなく、`bin/rails server`コマンドでサーバーを起動するだけでOK。<br />


### NPMパッケージをimportmap-railsで追加する

