---
title: AWSでハニートークンを作ってみた
tags:
  - AWS
  - security
  - honeypot
categories: honeypot
date: 2018-12-14 07:08:14
---


この記事は[Honeypot Advent Calendar 2018](https://adventar.org/calendars/3011)の10日目の記事です。

## ハニー<ここに任意の名詞>
ハニーポットはだいたいの場合アプリケーション単位で、あたかも脆弱なものを騙って(エミュレートして)いる。
世の中にはアプリケーションの他にも、攻撃者が付け狙う脆弱なものは無数にあるので、その攻撃者を騙すためのハニーエンティティもまた無数に考えられる。
実際に運用されているかどうかは分からないが、アイディアとしては相当色々ありそう。

| 名称           | 騙る対象           |
|----------------|--------------------|
| ハニーポット   | アプリケーション   |
| ハニーネット   | ネットワーク       |
| ハニーポート   | 開いているポート   |
| ハニーURL      | URL                |
| ハニーメール   | メールアドレス     |
| ハニーワード   | パスワード(の一部) |
| ハニーパッチ   | 脆弱なコード       |
| ハニーピーポー | SNSアカウント      |
| ハニートークン | 認証情報           |  

今回は「ハニートークン」や「ハニークレデンシャル」という風に呼ばれている、「あたかも使えそうな認証情報」をAWSのサービスで、ダッシュボードをポチポチと操作して作っていく。


## 作っていきます
### 目次
- [下準備](./#下準備)
- [トークンの生成](./#トークンの生成)
- [CloudTrailの設定](./#CloudTrailの設定)
- [CloudWatch Alarmの作成](./#CloudWatch Alarmの作成)
- [やってみた結果](./#やってみた結果)

### 下準備
何はともあれAWSアカウント自体のセキュリティはきちんとしておこう。
まずはIAMのセキュリティステータスの指示に従って、オールグリーンにしておくと気持ちがいい。
IAM、CloudTrail、CloudWatchなどは上の「サービス」タブから見つけられる。
![IAM](/images/AWS-Honeytoken-Tutorial/1.png)

まだrootアカウントでログイン中でしたら、セキュリティの設定のついで、料金アラームの設定もしておくと便利(デフォルトだとrootからしか設定できないので)。
準備が出来たらrootアカウントからはログアウトして、Administratorアカウントからログインしましょう。

### トークンの生成
次に、ユーザーを作成していく。
![ユーザー作成](/images/AWS-Honeytoken-Tutorial/2.png)

トークンを生成するために「プログラムによるアクセス」にチェックを入れる。
この次のステップで重要になってくるのが、このユーザーには権限を全くもって一つも与えないことだ。
![権限](/images/AWS-Honeytoken-Tutorial/3.png)

完了したらアクセスキーとシークレットキーが表示される。
これをハニートークンとして攻撃者に使ってもらう。
![トークン](/images/AWS-Honeytoken-Tutorial/4.png)

### CloudTrailの設定
次に、アカウント上で起こるあらゆるAPIコールを記録してくれるCloudTrailをオンにする。
何かしら問題が起きた時に追いやすいのでハニートークンを作るわけでは無くともオンにしておくと良い。
![CloudTrail作成](/images/AWS-Honeytoken-Tutorial/5.png)

CloudTrailはS3バケットに保存してもらう。
![名前](/images/AWS-Honeytoken-Tutorial/6.png)
![バケット](/images/AWS-Honeytoken-Tutorial/7.png)

CloudTrailの反映には若干ラグがあるので、作ってすぐだとイベント履歴には何も出てこない。
次に、作ったCloudTrail証跡をクリックして、CloudWatch Logsに流し込むロググループの設定をする。
![反映](/images/AWS-Honeytoken-Tutorial/8.png)
![CloudTrail-CloudWatch](/images/AWS-Honeytoken-Tutorial/9.png)

以下のようになっていれば準備OKだ。
![準備OK](/images/AWS-Honeytoken-Tutorial/10.png)

### CloudWatch Alarmの作成
最後に、インスタンスのCPU使用率が低くなったりとか、指定した使用料を超えたりなどモニタリングに使えるCloudWatchのアラームを作成する。
先程設定したロググループを選択する。
![ロググループ選択](/images/AWS-Honeytoken-Tutorial/11.png)

ハニートークンが使用されたときのみに反応して欲しいので、最初に作ったユーザーの名前でフィルタする。
`{ $.userIdentity.userName = "honeyuser" }`
![フィルタ](/images/AWS-Honeytoken-Tutorial/12.png)

モニタリングされるメトリクスの名前を決める。
![メトリクス](/images/AWS-Honeytoken-Tutorial/13.png)

無事作成出来たら、ついにアラームを作成出来る。
![アラーム](/images/AWS-Honeytoken-Tutorial/14.png)

1回でもAPIコールされたらアラームをあげて欲しいので、閾値を `> 0`に設定した。
![閾値](/images/AWS-Honeytoken-Tutorial/15.png)

Amazon SNSを用いて様々なサービスに流すことが出来るが、今回は最も簡単なメールでの通知にする。
![アクション](/images/AWS-Honeytoken-Tutorial/16.png)

「アラームの作成」を押せば、ハニートークンを使った時にアラームが鳴るように設定出来たはずだ。

### やってみた結果
動作確認として、aws cliにこのハニートークンを設定してみて、アカウント情報を閲覧するコマンドを打つ。
```bash
$ aws configure
$ aws get-account-summary
```
![get-account-summary](/images/AWS-Honeytoken-Tutorial/17.png)

権限が無いので、当然ながら弾かれる。
また、CloudWatchのダッシュボードから警告が上がっていることが確認できた。
すぐにメールも届いた。
動作確認は大丈夫そう。
![alert](/images/AWS-Honeytoken-Tutorial/18.png)
![email](/images/AWS-Honeytoken-Tutorial/19.png)

最後に、pastebinにjson形式でこのトークンを上げてみて、アラートが上がるか待ってみた。
![pastebin](/images/AWS-Honeytoken-Tutorial/20.png)

結論としては残念ながら、3日間でのこのpasteのunique viewは50程度であり、CloudWatchにも使われた痕跡は無かった。

インスタンスが立っているわけではないので、お金が一切掛からないのが良いところで、どんなプロジェクトでもとりあえずハニートークンを作っておけるように、Infrastructure-as-Codeツールのterraformで書いてみたので、これはまた別の記事にする。
これを読んでいる人も、スクリーンショットをちらちら見ながら作る必要も無く、`terraform apply`だけで再現出来るようになる。


## 侵入検知システムとしてのハニーポット
ハニーポットを植えるのは「流行している攻撃の情報を収集するため」ということが多いだろうが、攻撃者に本物と勘違いさせて騙すという特性はもっと可能性に満ちている。
まずは、機密情報や重要サービスなどを動かしているサーバーにまぎれて、そっくりなハニーポットを設置しておく。
そして例えば、凄腕の攻撃者が、ゼロデイの脆弱性やマルウェアを使って、アンチウィルスソフトなどの検知を全てすり抜け、バックドアを仕掛けることに成功したとする。
攻撃者の目当てが、侵入出来たサーバーと同じネットワーク上にある別のサーバーにあるとしても、管理者の誰一人として、この危機的状況に気付いてはいない。
しかし、この状況こそ、ハニーポットの出番で、目当てのサーバーを探すために、攻撃者はネットワーク上のサーバー幾つかに、パケットを送る。
この時、ハニーポットにアクセスしてくる正規ユーザーはほぼ100%いないので、ハニーポットへのアクセスを検知したら、既に侵入を許していて、ネットワークを調査されているということが考えられる。
