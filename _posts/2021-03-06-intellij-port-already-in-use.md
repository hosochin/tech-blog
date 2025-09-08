---
layout: post
title: 【IntelliJ】Webアプリケーションをlocalhost起動したらポート使用済みで怒られる
date: 2021-03-06
tags: [intellij, java, spring-boot, troubleshooting, port]
---

# はじめに

お世話になります、hosochinです

今回は
**「webアプリケーションをIntelliJで起動したらポート使用済みで怒られた」**
です

自分はIntelliJのCommunity版使ってる雑魚なので30分経過すると強制的にIntelliJが閉じられてしまいます（なかなか有料版買う勇気がでない…かと言ってEclipse使う気にもなれない）

で、いつものように30分経過してIntelliJが落ちて再起動、spring-bootで組んだAppをローカルで起動しようとしたら、、、

```bash
***************************
APPLICATION FAILED TO START
***************************

Description:

The Tomcat connector configured to listen on port 8080 failed to start. The port may already be in use or the connector may be misconfigured.

Action:

Verify the connector's configuration, identify and stop any process that's listening on port 8080, or configure this application to listen on another port.
```

・・・😇

まあメッセージ読めばそのまんまなんですが8080ポート使用済みですよと

そういえば30分経過で強制的に落とされた時にアプリケーション起動してたなーと

# 目次

- [解決方法](#解決方法)

# 解決方法

解決方法というほどでもないんですが、無料版使ってるとよくあるのでただのメモ

8080ポート使ってるプロセスを調べてkillするだけっす

```bash
-- 8080ポートを使用しているプロセスを調べる
$ lsof -i -P | grep 8080
java      14234 User   86u  IPv6 0xxxxxxxx      0t0  TCP *:8080 (LISTEN)

-- プロセスをkillする
$ kill -9 14234
```