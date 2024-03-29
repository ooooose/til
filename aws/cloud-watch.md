# CloudWatchについて
## 概要

リアルタイムのログ、メトリクス、イベントデータを収集して自動化されたダッシュボードに視覚化し、インフラストラクチャとアプリケーションのメンテナンスを合理化する。<br />

[![Image from Gyazo](https://i.gyazo.com/284f832af0fb507c77a37676d7416d2d.png)](https://gyazo.com/284f832af0fb507c77a37676d7416d2d)<br />

EC2やRDSなどのAWSサービスやオンプレミスのサーバーなどを監視できるサービス。<br />
各AWSリソースの変更をトリガーに何かアクションを起こしたり（CloudWatchイベント）、EC2のCPU使用率やディスクの読み取り量などを記録・監視する（CloudWatchメトリクス）ことが可能。<br />

## CloudWatchの6つの種類と料金体系

### CloudWatchメトリクス
メトリクスとは、各リソースから収集したデータを時系列でまとめたもの。<br />
例えば、以下のメトリクスがある。<br />

- EC2のCPU使用率やネットワーク通信料、ステータスのチェック合格したかどうかの報告
- RDSのディスク容量やデータベースへのネットワーク接続数、空いているストレージ領域
- ALBのアクティブな同時接続やエラーコードの数、正常または異常とみなされるターゲットの数

### CloudWatchエージェント
追加のメトリクスを収集した時に使用するサービス。<br />

標準のメトリクスでは「EC2」のデータしか収集できないが、エージェントを使用すれば「EC2の中で動いているアプリケーション」のデータや「EC2の中のログ」を収集することができる。<br />
また、オンプレミスのサーバにCloudWatchエージェントを導入すれば、AWS外のデータも集めることもできる。<br />

### CloudWatchログ
ログを文字列として保存したり、ログに特定の文字列があった際にアラームを出すことができるサービス。<br />
AWS CloudTrail、Amazon Kinesis Data Streams、AWS Lambdaからログを収集することが可能。<br />


また、CloudWatchエージェントを使用すればEC2内のアプリケーションやオンプレミスのサーバからもログを集めることも可能<br />


### CloudWatchアラーム
1つ、または複数のメトリクスが特定の値になった時にアラームを出すことができるサービス。<br />


例えば、以下のようなアラームを設定することができる。<br />

- EC2のCPU使用率が５分間80%を上回っているとき
- ログに「Out of Memory」という文字列が1時間のうちに2回出現したとき
- RDSのディスク空き容量が残り10GB未満になって５分間そのままのとき

### CloudWatchイベント
CloudWatchイベントはAWSリソースの変更やAPIの特定のイベントをトリガーとしてアクションの実行を自動化できるサービス。<br />
例えば、EC2インスタンスが新しく作成されたのをトリガーとして「0.0.0.0/0」のインバウンドルールが適用されていないかを確認し、もし適用されていたらEC2をシャットダウンするようなアクションを設定できる。<br />


### CloudWatchダッシュボード
メトリクス、アラーム、イベントを自分の好きなようにカスタマイズして表示できるサービス。<br />

別のリージョンのメトリクスなどもカスタマイズ可能なので、やろうと思えば全てのAWSリソースの監視を1つの画面ですることも可能。<br />


## 参考文献
[Engineer.Club](https://www.bold.ne.jp/engineer-club/aws-cloudwatch)
