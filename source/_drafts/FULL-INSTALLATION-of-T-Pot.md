---
title: FULL INSTALLATION of T-Pot
categories: honeypot
tags:
- honeypot
- T-Pot
---

誰もが心に持つ「人の金でT-Pot FULL INSTALLしたい」という欲求を叶えてきました。

## T-Pot
これは多数のハニーポットをDockerコンテナで建てて、Logstash + ElasticSearch + Kibana でログをまとめて解析出来るまでを超簡単に用意してくれるものです。
建て方はこちらを参考にしてみてください: [EC2上にHoneypot(T-Pot)をインストールして、サイバー攻撃をELKで可視化してみた  - Qiita](https://qiita.com/tarosaiba/items/871ab0c155578f8a38fe)

今回は要求するスペックが割とお高いので敬遠していたFULL INSTALLATIONをしてみました。
普通のSTANDARD INSTALLATIONと違うのは、産業用に使われている機器のハニーポットが、一般のサービスのハニーポットと同居している点です。

```
##########################################################
#                                                        #
#     How do you want to proceed? Enter your choice.     #
#                                                        #
# 1 - T-Pot's STANDARD INSTALLATION (w/o INDUSTRIAL)     #
#     Requirements: >=4GB RAM, >=64GB disk               #
#     Services: Cowrie, Dionaea, ElasticPot, Glastopf,   #
#     Honeytrap, ELK, Suricata+P0f                       #
#                                                        #
# 2 - T-Pot's HONEYPOTS ONLY (w/o INDUSTRIAL)            #
#     Requirements: >=3GB RAM, >=64GB disk               #
#     Services:                                          #
#     Cowrie, Dionaea, ElasticPot, Glastopf, Honeytrap   #
#                                                        #
# 3 - T-Pot's INDUSTRIAL EDITION                         #
#     Requirements: >=3GB RAM, >=64GB disk               #
#     Services: ConPot, eMobility, ELK, Suricata+P0f     #
#                                                        #
# 4 - T-Pot's FULL INSTALLATION                          #
#     Requirements: >=8GB RAM, >=128GB disk              #
#     Services: Cowrie, Dionaea, ElasticPot, Glastopf,   #
#     Honeytrap, ELK, Suricata+P0f                       #
#                                                        #
##########################################################

Your choice: 4
```

[ConPot](https://github.com/mushorg/conpot)はICS(Industrial Control System = 産業用制御システム) やSCADA(Supervisory Control And Data Acquisition = 監視制御システム)のハニーポット。
Modbus,s7といった産業用プロトコルを喋ります。
Stuxnetは産業用のPLC(Programmable Logic Controller)を狙った攻撃だったそう。
ニュースでしか知らない知識を自ら観測できたら楽しそう。

[eMobility](https://github.com/dtag-dev-sec/emobility)は電気自動車と充電ステーションが使うOCPP(Open Charge Point Protocol)を喋ります。
果たして本当にこういうものを狙う攻撃者がいるのか、それともセキュリティ研究者の考えが先走ってしまったのか、気になるところです。

他にも、自分が前にT-Potをインストールした時には無かったのが、リモートデスクトップ系のプロトコルを喋る[vnclowpot](https://github.com/magisterquis/vnclowpot)と[rdpy](https://github.com/citronneur/rdpy)あたりでしょうか。
あとはSMTPを喋る[mailoney](https://github.com/awhitehatter/mailoney/)も気になります。

## 3日後
期間: `2018/09/02 18:00:00` - `2018/09/05 18:00:00`

なんと! eMobilityとElasticPotを狙う攻撃者はいませんでした。
(「なんと!」じゃないですね、薄々気づいていました…少なくとも3日ではね…)
気を取り直して、攻撃の概要を見てみます。

![概要](/images/FULL-INSTALLATION-of-T-Pot/1.png)

### ConPot
ConPotに1件だけですが攻撃が来ていました!
やったー
このハニーポットは高対応型を裏に置いて低対応型を攻撃させる、いわゆるハイブリッド型のハニーポットになっているようです。
面白いですね: [Outsmarting the smart meter | The Honeynet Project](https://www.honeynet.org/node/1179)
![kamstrup](/images/FULL-INSTALLATION-of-T-Pot/2.png)
スマートメーターのKamstrupを狙っていたようです。

### Cowrie
![Cowrie](/images/FULL-INSTALLATION-of-T-Pot/3.png)
攻撃として1番多かったのはWindowsのRDP(Remote Desktop Protocol)を使ったDoS攻撃のCVE-2012-0152やCVE-2001-0540です。
9/4の極端に高い山はこれで、中国の特定のIPからの攻撃でした。
他に多かったのはロシアからの、443番ポートでRaspberry Piを狙ったものでした。
MiningをするTrojanを置いていってくれました: [VirusTotal](https://www.virustotal.com/#/file/46b79608c9a603c1f0046b0952f080b6cce855320a80bb6db4155a26ab0fd5f0/detection)
建てておいて難ですが443番ポートも聞いていたのですね、何も考えずにポート全開放にするのは良くない。

### Suricata
見方がいまいちわかりませんがとにかく「ET EXPLOIT [PTsecurity] DoublePulsar Backdoor installation communication」。
これが多い。毎度おなじみ、もはやインターネット天気予報ですね。

## T-Potについて一考
T-PotはELKスタックとの連携、多種多様なハニーポットのデプロイをここまで圧倒的に簡単にしていること、そして日進月歩している点本当にすごいプロジェクトだと思います。
今回は特に、いつの間にかsshトンネルを掘る必要がなく、64295ポートを指定すればブラウザで即見れるようになっていたので驚きました。

一方で見てほしいのが、このIPをshodan.ioでを見てみたところです。
![shodan.io](/images/FULL-INSTALLATION-of-T-Pot/4.png)

あり得ない数のポートが開いてサービスが動いていますね。
また、具体的に見ていくと`\n`など、本来のサービスなら返さないようなレスポンスを返しているものもあります。
![shodan.io詳細](/images/FULL-INSTALLATION-of-T-Pot/5.png)

ある程度狙いを定める攻撃者ならばこんなホストは避けるでしょう。
そもそもAWSで産業用システムが動いているとは考えにくいです。
相応の設置場所で相応のチューニングをして待ち構えるのが良いでしょう。
しかし、逆にここに来た攻撃は、相当盲目にスキャンしても攻撃者にメリットのあるような攻撃だとも考えられます。
パッチされていないkamstrupが世には多いのだろうな、と普及具合も伺えます。

## 今後
ConPotやeMobilityは引き続き、別の設置場所に置いてみたいですが、T-Potではなく単独で置いてみたいと思います。
あと、dionaeaからバイナリをダウンロードし忘れたのが悔しいので、インターネット天気予報出来るようにスクリプトを書きます。
