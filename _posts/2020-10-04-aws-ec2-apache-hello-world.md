---
layout: post
title: 【AWS】EC2でHello World してみた
date: 2020-10-04
tags: [aws, ec2, apache, cloud, infrastructure]
---

# はじめに

お世話になります、hosochinです

今回は
**「AWS EC2にApacheを入れてHello Worldしてみる」**
です

[EC2について](https://aws.amazon.com/jp/ec2/)

今回はEC2インスタンスの作成とElasticIPの関連付けまで完了している前提とします

# 目次

- [やってみる](#やってみる)
  - [キーペア作成](#キーペア作成)
  - [TeraTermで接続](#teratermで接続)
  - [Apacheをインストールして起動](#apacheをインストールして起動)
  - [ブラウザからの確認](#ブラウザからの確認)

# やってみる

## キーペア作成

画面左の選択欄から キーペア > キーペアの作成 を選択、適当なキーペア名を入力後作成ボタンをクリックして、「キーペア名.pem」をダウンロードします

インスタンス一覧画面のキー名のところに作成したキーペアが登録されていることを確認

![EC2キーペア作成](/assets/ec2-keypair.png)

## TeraTermで接続

接続先のインスタンスのIPv4パブリックIPを入力して接続します

![TeraTerm接続](/assets/teraterm-connection.png)

ユーザ名にec2-userを入力、先ほどダウンロードした認証キーを使って接続します

![TeraTerm認証](/assets/teraterm-auth.png)

接続完了！

![TeraTerm接続完了](/assets/teraterm-connected.png)

## Apacheをインストールして起動

```bash
// パッケージ更新
$ sudo yum update -y

// Apacheのインストール
$ sudo yum install -y httpd

// Apacheの起動
$ sudo service httpd start

// htmlファイル作成
$ sudo vim /var/www/html/index.html

$ cat /var/www/html/index.html
Hello World
```

## ブラウザからの確認

その前にポートを開いておきます

セキュリティグループ > アクション > インバウンド のルールの編集

タイプをHTTPにして追加しときます

![セキュリティグループ設定](/assets/security-group-http.png)

インスタンス > パブリックDNS(IPv4) にあるアドレスにブラウザからアクセスすれば、Hello Worldが表示されました！