---
layout: post
title: 【負荷テスト】vegetaでcookieをつけてリクエストする方法
date: 2021-07-03
tags: [vegeta, テスト]
---

# はじめに

お世話になります、hosochinです

今回は、**「vegetaでcookieをつけてリクエストする方法」**についてです

# 目次

- [vegetaって？](#vegetaって)
- [検証](#検証)
  - [vegetaをインストール](#vegetaをインストール)
  - [テキトーなコントローラ作成してみる](#テキトーなコントローラ作成してみる)
  - [targets.txtを用意する](#targetstxtを用意する)
  - [Attack!](#attack)
- [Cookieをつけてリクエストする](#cookieをつけてリクエストする)
  - [Cookieを受け取るコントローラをテキトーに作成](#cookieを受け取るコントローラをテキトーに作成)
  - [Cookie付きのtargets.txtを用意する](#cookie付きのtargetstxtを用意する)
  - [Attack!](#attack-1)
- [複数URLにCookieをつけてリクエストする場合](#複数urlにcookieをつけてリクエストする場合)

# vegetaって？

READMEがイカしてるシンプルなGolang製のHTTP負荷テストツールです  
[https://github.com/chidakiyo/vegeta](https://github.com/chidakiyo/vegeta)

<img src="{{ '/assets/images/vegeta-logo.png' | relative_url }}" alt="Vegeta Logo" width="300">

かっけえ…👍

# 検証

## vegetaをインストール

brewでインストール

```bash
$ brew update && brew install vegeta
```

## テキトーなコントローラ作成してみる

今回はJava(spring-boot)で構築

```java
@RestController
public class VegetaTestController {
    @GetMapping("/vegeta-test")
    public String get() {
        System.out.println(LocalDateTime.now()); // 一応リクエストきてるかログに時間を出力
        return "Hello World";
    }
}
```

## targets.txtを用意する

負荷をかけるURLとかをファイルにまとめて外出ししとく  
{メソッド} {エントリポイント}って感じで書く

* targets.txt

```
GET http://localhost:8080/vegeta-test
```

## Attack!

以下のコマンドの感じで負荷をかけれる  
下記だとtargets.txtにあるURLに対して、秒間5リクエストを10秒間かけ続ける

```bash
# コマンド
$ vegeta attack -rate=5 -duration=10s -targets=targets.txt | vegeta report

# 実行結果（パーセンタイルでも出てくるので嬉しい）
Requests      [total, rate, throughput]         50, 5.10, 5.08
Duration      [total, attack, wait]             9.837s, 9.8s, 36.887ms
Latencies     [min, mean, 50, 90, 95, 99, max]  3.853ms, 19.481ms, 16.305ms, 40.456ms, 53.898ms, 81.368ms, 81.368ms
Bytes In      [total, mean]                     550, 11.00
Bytes Out     [total, mean]                     0, 0.00
Success       [ratio]                           100.00%
Status Codes  [code:count]                      200:50
Error Set:
```

# Cookieをつけてリクエストする

**さあここからが本題！**  
あれ、Cookieありのテストってどうやってやるんだろってなったんでメモ

## Cookieを受け取るコントローラをテキトーに作成

```java
@RestController
public class VegetaTestController {
    @GetMapping("/vegeta-test")
    public String get(@CookieValue("test_cookie") String cookie) {
        System.out.println(cookie); // 受け取ったcookieを出力
        System.out.println(LocalDateTime.now()); // 一応リクエストきてるかログに時間を出力
        return "Hello World";
    }
}
```

## Cookie付きのtargets.txtを用意する

{メソッド} {エントリポイント} はそのままで、改行してその下に Cookie: {クッキー情報} って書いてやる

* targets.txt

```
GET http://localhost:8080/vegeta-test
Cookie: test_cookie=sample
```

## Attack!

ここはCookieなしのときと同じっす、ログに渡したcookieが出てくれば成功！

```bash
$ vegeta attack -rate=5 -duration=10s -targets=targets.txt | vegeta report
```

# 複数URLにCookieをつけてリクエストする場合

空行を入れてやる必要があるっぽい

* targets.txt

```
GET http://localhost:8080/xxxxx
Cookie: test_cookie=cookie1

GET http://localhost:8080/xxxxx
Cookie: test_cookie=cookie2

GET http://localhost:8080/xxxxx
Cookie: test_cookie=cookie3
```
