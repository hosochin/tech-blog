---
layout: post
title: 【AWS】無料枠内だと思ってたら料金発生してた話
date: 2020-06-21
tags: [aws, cloud, elastic-ip, billing, ec2]
---

# はじめに

お世話になります、hosochinです

さて、今回は **AWSで無料枠内だと思って使っていたら料金発生してた話** です

EC2のt2.microは月750時間までは無料とういことで使ってみたんですが、**いきなり料金発生してる！なんで？** ってなったのでメモです

# 目次

- [調べてみた](#調べてみた)

# 調べてみた

[AWS ドキュメント » Amazon EC2 » Linux インスタンス用ユーザーガイド » ネットワークとセキュリティ » Elastic IP アドレス](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)

![AWS Elastic IP 料金について](/assets/aws-elastic-ip-billing.png)

なるほど、

- Elastic IP アドレスが実行中のインスタンスに関連付けられていない場合
- Elastic IP アドレスが停止しているインスタンスやアタッチされていないネットワークインターフェイスに関連付けられている場合

にも料金が発生するようです

今回はElastic IPに関連付けられているインスタンスを停止させていたせいでした

使ってないときは停止しといた方がいいかなーってな感じで停止させていたのですが、24時間起動していても1インスタンス月750時間は超えないので、常時起動させとくのがいいですね