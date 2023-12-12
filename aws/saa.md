# SAAに関するメモ
## EC2の購入方式について
購入方式によっては利用期間と割引率が異なるため、設問で提示された要件に沿った方式を選択することが必要。<br />

### オンデマンドインスタンス
通常のインスタンス購入方式を指す。<br />
長期契約なしでコンピューティング性能に対して秒単位で支払う。<br />
ライフサイクルを完全に制御できるため、いつ起動・停止・休止・開始・再起動・終了など選択肢、決定することが可能。<br />

### リザーブドインスタンス
1年または3年の期間利用を予約することで、通常のオンデマンド料金に比べて大幅な割引価格（最大75%）が適用されるインスタンスの購入方式。<br />
特定のアベラビリティゾーンまたはリージョンで使用するキャパシティーを予約できる2つのタイプがある。<br />

### スポットインスタンス
オンデマンド価格より低価で利用できるAWS管理ように保持されているが未使用のEC2インスタンスがある場合、ユーザーは未使用のEC2インスタンスが静止状態割引を（最大90%）でリクエストできる。<br />
実行時間に柔軟性がある場合や、中断できる処理がある場合に適用する。<br />

## Auto Scalingについて

### 障害パターン
実行されない時間が24時間を超えた場合、自動的にAuto Scaling処理が停止するようになっている。<br />
以下、障害パターンをまとめる。<br />
#### インスタンスの起動失敗
- Auto Scalingはインスタンスの起動を繰り返し実施し、24時間失敗し続けるとAmazon側で停止される可能性がある。<br />

#### インスタンスの障害
- インスタンスの状態がImpairedとなると、数分間リカバリーされるかチェックする。
- リカバリーされない場合は新しいインスタンスを起動して、Impairedのインスタンスを終了する。

#### トラブルシューティングのステップ
- Auto Scalingグループを一時的に停止しないでインスタンスを停止すると新規インスタンスが停止してしまう。
- Auto Scalingを停止して、調査・復旧し、Auto Scalingを再開することが基本的な実施方法。

### 変更箇所
ここです。