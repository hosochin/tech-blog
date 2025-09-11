---
layout: post
title: 【Redis】ローカルMacにRedisを入れて動かしてみる
date: 2023-04-05
tags: [Redis]
---

# はじめに

お世話になります、hosochinです  
今回は **「Redis」** を手元のMacPCに入れて動かしてみたいと思います

# 目次

- [Redisとは](#redisとは)
- [環境構築](#環境構築)

# Redisとは

NoSQLの1つです！詳しくはここらへん見るといいかもです  
https://qiita.com/gold-kou/items/966d9a0332f4e110c4f8

**Redisの特徴:**
- インメモリデータベース
- キー・バリュー型のデータストア
- 高速な読み書き性能
- データ構造が豊富（String, Hash, List, Set等）

# 環境構築

## インストール手順

```bash
# brewでインストール
$ brew install redis

# Redisを起動させる
# Redisのアイコンみたいなのがモリモリって出てきます
# デフォルトポートは6379で立ち上がる
$ redis-server

# Redisクライアントを起動させる
# 別ターミナル開いてやってくだせえ
$ redis-cli

# こんな感じで表示されてたら成功です！
$ 127.0.0.1:6379>

# こうなってたらたぶんRedisサーバの起動がうまくいってないと思います
$ not connected>
```

## 接続確認

Redisクライアントで以下のような表示になれば正常に起動しています：

```
127.0.0.1:6379>
```

もし `not connected>` と表示される場合は、Redisサーバの起動に失敗している可能性があります。

## 基本的な動作確認

```bash
# 接続テスト
127.0.0.1:6379> ping
PONG

# 簡単なキー・バリューの設定と取得
127.0.0.1:6379> set mykey "Hello Redis"
OK
127.0.0.1:6379> get mykey
"Hello Redis"
```

# まとめ

HomebrewでRedisをインストールし、redis-serverとredis-cliを使って基本的な環境を構築できました。この記事ではデータの登録とか取得は触れませんが、とりあえずこれで環境は作れたと思います！

次はデータの操作やデータ型について詳しく触れてみたいですね。
