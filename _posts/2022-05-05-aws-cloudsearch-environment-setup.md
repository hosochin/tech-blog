---
layout: post
title: 【AWS】CloudSearchで全文検索 環境構築編
date: 2022-05-05
tags: [aws, cloudsearch, full-text-search, elasticsearch]
---

# はじめに

お世話になります、hosochinです  
最近個人開発のサービスに検索機能を実装したく、AWSに全文検索的なのあんのかなーって思って調べたら **CloudSearch** ってのがありました  
[CloudSearch](https://aws.amazon.com/jp/cloudsearch/)  
今回はこのCloudSearchを使ってみたって話になります  
30日間無料枠も用意されてたので👍  

ちなみにAWSには検索サービスとしてCloudSearchの他に[OpenSearch Service（Elasticsearch Service）](https://aws.amazon.com/jp/opensearch-service/?nc=bc&pg=wik)ってのも用意されています  
違いについてはこちらの記事がわかりやすかったです  
[Elasticsearch Service と CloudSearch どっちを選べば良いの？](https://dev.classmethod.jp/articles/elasticsearch-service-vs-cloudsearch/)  
この記事によると

- CloudSearch → シンプルで始めやすい、カスタマイズ性は劣る
- OpenSearch Service → 高いカスタマイズ性、その分学習コスト高め？

って感じぽいです  
迷わずCloudSearchを選択していくスタンス

# 目次

- [前提](#前提)
- [環境構築](#環境構築)

# 前提

- AWSアカウント作成済み
- ローカルPCにaws cliインストール済み
  - credentialとかもろもろ設定済み

# 環境構築

[CloudSearchのマネージメントコンソール](https://ap-northeast-1.console.aws.amazon.com/cloudsearch/home?region=ap-northeast-1)から「Create a New Domain」をクリックします

![CloudSearch初期画面](/assets/cloudsearch-create-domain.png)

ダイアログが表示されると思います  
「Search Domain Name」を入力して「Continue」をクリックします  
今回はsearch-testとします

![CloudSearchドメイン名設定](/assets/cloudsearch-domain-name.png)

次にインデックスのconfigのインポート方法を選択します  
今回は手動で入力したいと思うんで一番下の「Manual configuration」を選択して「Continue」をクリックします

![CloudSearch設定方法選択](/assets/cloudsearch-config-method.png)

次にインデックスの設定を入力していきます  
今回は

- first_name : text : Japanese
- last_name : text : Japanese  
- pr : text : Japanese

の3行を「Add Index Filed」から追加して「Continue」をクリックします

![CloudSearchインデックス設定](/assets/cloudsearch-index-config.png)

次にアクセスポリシーの設定を行います  
今回は一番上の「Search and Suggester service: Allow all. Document Service: Account owner only.」をクリックします  
ただこれだけだとPrincipal.AWSのところがアスタリスクになっていて誰でもアクセスできちゃうんで、自分のアカウントに書き換えておきます  
XXXXX部分はIDです  
問題なければ「Continue」をクリックします

![CloudSearchアクセスポリシー設定](/assets/cloudsearch-access-policy.png)

最後に確認画面になるんで確認後に「Confirm」をクリックして環境構築は終了です  
10分くらいかかるで〜ってメッセージが表示されますが自分は20分くらいかかってた気がします  
ビールでも飲んで気長に待ちましょう🍺  
ダッシュボードに戻るとstatusが「LOADING」と表示されてるはずです  
こいつが「ACTIVE」になったらドメイン作成完了となります

![CloudSearchローディング状態](/assets/cloudsearch-loading.png)

ACTIVEになったのでダッシュボードを確認してみます

![CloudSearchアクティブ状態](/assets/cloudsearch-active.png)

いい感じですね👍  
では次にドキュメントデータをインポートしていきます

> 続き：【AWS】CloudSearchで全文検索 ドキュメントデータ入力編

# まとめ

AWS CloudSearchの環境構築は非常にシンプルで、マネージメントコンソールから数ステップで完了できます。OpenSearch Serviceと比較してカスタマイズ性は劣りますが、簡単に全文検索機能を導入したい場合には最適な選択肢です。