---
layout: post
title: 【Elasticsearch】ローカル環境でElasticsearchを試してみる
date: 2023-05-23
tags: [elasticsearch, search-engine, database, kibana, full-text-search, analytics]
---

# はじめに

お世話になります、hosochinです  
今回はElasticsearchをローカル環境で試してみた話です

# 目次

- [Elasticsearchとは](#elasticsearchとは)
- [環境構築](#環境構築)
- [動作確認](#動作確認)
  - [ドキュメントを投入する](#ドキュメントを投入する)
  - [投入したドキュメントを確認する](#投入したドキュメントを確認する)
  - [ドキュメントの検索](#ドキュメントの検索)
    - [全てのドキュメントを検索](#全てのドキュメントを検索)
  - [ドキュメントの更新](#ドキュメントの更新)
  - [ドキュメントの削除](#ドキュメントの削除)
- [Kibana も試してみる](#kibana-も試してみる)
  - [Kibana とは](#kibana-とは)
  - [Kibana の環境構築](#kibana-の環境構築)

# Elasticsearchとは

- 分散型の全文検索エンジン/データ分析エンジン
- [公式ドキュメント](https://www.elastic.co/jp/what-is/elasticsearch)
- [Github](https://github.com/elastic/elasticsearch)

# 環境構築

1. [Elasticsearch をダウンロードする](https://www.elastic.co/jp/downloads/elasticsearch) （この時のバージョンは 8.7.0）
2. ダウンロードした tar.gz ファイルを解凍
3. elasticsearch-8.7.0 の直下に移動

```bash
$ cd elasticsearch-8.7.0
```

4. 日本語プラグインをインストール

```bash
$ bin/elasticsearch-plugin install analysis-kuromoji
```

5. 起動させる（1 分くらいかかる）

```bash
$ bin/elasticsearch
```

6. http://localhost:9200 にアクセスして json が表示されたら成功

## うまくいかない場合

こんなエラーが出力されているとき：

```bash
received plaintext http traffic on an https channel, closing connection Netty4HttpChannel
```

1. config/elasticsearch.yml を修正する
2. `xpack.security.enabled`が`true`になっていると思うので、`false`に変更する
3. 再起動してやる

# 動作確認

## ドキュメントを投入する

```bash
curl -H "Content-Type: application/json" -X POST "http://localhost:9200/myindex/_doc/1" -d @example1.json
```

example1.json

```json
{
  "firstname": "Taro",
  "lastname": "Suzuki"
}
```

## 投入したドキュメントを確認する

```bash
$ curl -X GET "http://localhost:9200/myindex/_doc/1" | jq
{
  "_index": "myindex",
  "_id": "1",
  "_version": 1,
  "_seq_no": 0,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "firstname": "Taro",
    "lastname": "Suzuki"
  }
}
```

## ドキュメントの検索

### 全てのドキュメントを検索

```bash
-- リクエストパラメータ方式
$ curl -X GET "http://localhost:9200/myindex/_search?q=firstname:Taro" | jq

-- リクエストボディ方式
$ curl -H "Content-Type: application/json" -X GET "http://localhost:9200/myindex/_search" -d '{"query": {"match": {"firstname": "Taro"}}}' | jq
```

他にも AND 条件、OR 条件、数値の検索、フィルターつけたり、スコアに重み付けしたり…めっちゃ色々ある

詳しくは[こちら](https://www.elastic.co/guide/en/app-search/8.7/guides.html)

## ドキュメントの更新

```bash
curl -H "Content-Type: application/json" -X PUT "http://localhost:9200/myindex/_doc/1" -d @example2.json
```

example2.json

```json
{
  "firstname": "Ichiro",
  "lastname": "Suzuki"
}
```

## ドキュメントの削除

```bash
curl -X DELETE "http://localhost:9200/myindex/_doc/1"
```

# Kibana も試してみる

## Kibana とは

- Elasticsearch の可視化ツールみたいなやつ
  - Elasticsearch データの可視化
  - Elastic Stack の制御
  - クエリの負荷の追跡
  - アプリ内部のリクエストフローの把握
- [公式ドキュメント](https://www.elastic.co/jp/what-is/kibana)

## Kibana の環境構築

1. [Kibana をダウンロードする](https://www.elastic.co/jp/downloads/kibana)
2. 起動させる（1 分くらいかかる）

```bash
$ bin/kibana
```

3. http://localhost:5601/ をブラウザで開く
4. http://localhost:5601/app/dev_tools でコンソールが開かれる → ターミナルから curl してたのは全てこのコンソールから実行可能

# まとめ

Elasticsearchのローカル環境構築から基本的なCRUD操作まで試してみました。Kibanaとの連携により、データの可視化や管理が簡単にできるようになります。全文検索やデータ分析に強力な機能を提供してくれる優秀なツールです。