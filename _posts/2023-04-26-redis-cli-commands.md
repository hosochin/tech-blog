---
layout: post
title: 【Redis】redis-cliでよく使うコマンド
date: 2023-04-26
tags: [redis, cli, command, database, nosql]
---

# はじめに

お世話になります、hosochinです  
今回は **「redis-cli」** でよく使うコマンドを自分用にまとめた物になります  
ちなみにローカルでRedis環境を構築する手順は以下の記事にまとめています

> [【Redis】ローカルMacにRedisを入れて動かしてみる]({{ '/redis-local-mac-setup' | relative_url }})

# コマンド一覧

| コマンド | 説明 |
|---|---|
| keys * | キーの一覧を取得 |
| get \<key\> | キーを指定してvalueを取得 |
| del \<key\> | キーを指定して削除 |
| flushdb | 全てのキーを削除 |

# 基本的な使い方

Redis CLIに接続後、上記のコマンドを実行することでRedisサーバの操作が可能です。

```bash
# Redis CLIに接続
redis-cli

# キー一覧表示
127.0.0.1:6379> keys *

# データ取得
127.0.0.1:6379> get mykey

# データ削除
127.0.0.1:6379> del mykey

# 全データ削除
127.0.0.1:6379> flushdb
```

開発やテスト時に頻繁に使用するコマンドなので、覚えておくと便利です。