---
layout: post
title: 【AWS】CloudSearchで全文検索 ドキュメントデータ入力編
date: 2022-05-05
tags: [aws, cloudsearch, json, cli, full-text-search]
---

# はじめに

お世話になります、hosochinです

> [【AWS】CloudSearchで全文検索 環境構築編]({{ '/aws-cloudsearch-environment-setup' | relative_url }})

↑ の続きです  
CloudSearchにデータを入力したいと思います

# ドキュメントデータ入力

検索対象のドキュメントデータをインポートします  
json or xml形式で入力可能ですが、今回はjsonデータで入力していきます  
以下のようなサンプルデータを作成します

**test.json**

```json
[
  {
    "type": "add",
    "id": "1",
    "fields": {
      "first_name": "貞治",
      "last_name": "王",
      "pr": "一本足打法の人。世界のホームラン王。"
    }
  },
  {
    "type": "add",
    "id": "2",
    "fields": {
      "first_name": "一朗",
      "last_name": "鈴木",
      "pr": "日米でヒット打ちまくった人。演技力も高い。"
    }
  },
  {
    "type": "add",
    "id": "3",
    "fields": {
      "first_name": "有",
      "last_name": "ダルビッシュ",
      "pr": "副業でメジャーリーガーしてるYouTuber。"
    }
  },
  {
    "type": "add",
    "id": "4",
    "fields": {
      "first_name": "翔平",
      "last_name": "大谷",
      "pr": "日米で160キロ投げまくってホームラン打ちまくってる人。昔はみちのくのダルビッシュなんて呼ばれたりもしてた。"
    }
  }
]
```

コンソールからでもインポートできるんですがめっちゃ遅い & ちょくちょく謎のエラーに見舞われます  
aws cliからインポートするのがおすすめです  
endpoint-urlはダッシュボードのSearch Endpointを指定します

```bash
aws cloudsearchdomain upload-documents --endpoint-url https://search-search-test-misbuybhc7wm62ox6m3cvjq2s4.ap-northeast-1.cloudsearch.amazonaws.com --content-type application/json --documents test.json
```

**実行結果**

```json
{
    "status": "success",
    "adds": 4,
    "deletes": 0
}
```

データぶっ込めました👍  
次に実際に検索してみます

```bash
# 鈴木一郎のデータが取得できる
aws cloudsearchdomain search --endpoint-url https://search-search-test-misbuybhc7wm62ox6m3cvjq2s4.ap-northeast-1.cloudsearch.amazonaws.com --search-query "鈴木"

# ダルビッシュ有と大谷翔平のデータが取得できる
aws cloudsearchdomain search --endpoint-url https://search-search-test-misbuybhc7wm62ox6m3cvjq2s4.ap-northeast-1.cloudsearch.amazonaws.com --search-query "ダルビッシュ"

# last_nameに対して検索、ダルビッシュ有のデータのみが取得できる
aws cloudsearchdomain search --endpoint-url https://search-search-test-misbuybhc7wm62ox6m3cvjq2s4.ap-northeast-1.cloudsearch.amazonaws.com --query-parser structured --search-query "last_name:'ダルビッシュ'"
```

検索オプションについては[こちら](https://docs.aws.amazon.com/ja_jp/cloudsearch/latest/developerguide/searching-text.html)を参照

# まとめ

AWS CloudSearchへのドキュメントデータ投入はAWS CLIを使用すると効率的に行えます。JSON形式でデータを準備し、upload-documentsコマンドでインポート、searchコマンドで検索動作確認が可能です。

次回はJavaアプリケーションからCloudSearchにアクセスして検索してみたいと思います！