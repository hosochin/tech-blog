---
layout: post
title: 【Spring Boot】Caffeineを使ってローカルキャッシュしてみる
date: 2020-07-26
tags: [spring-boot, java, caffeine, cache, performance]
---

## はじめに

お世話になります、hosochinです

さて、今回は
**「Spring BootでCaffeineを使ってローカルキャッシュしてみる」**
です

CaffeineとはJava8で書かれたキャッシュライブラリです

キャッシュはメモリで持つため、キャッシュ用のDBなどは不要です

> Caffeineは、Java 8に基づく、[最適](https://github.com/ben-manes/caffeine/wiki/Efficiency)に[近い](https://github.com/ben-manes/caffeine/wiki/Efficiency)[高性能の](https://github.com/ben-manes/caffeine/wiki/Benchmarks)キャッシュライブラリです。詳細については、[ユーザーガイド](https://github.com/ben-manes/caffeine/wiki)を参照し、最新リリースの[APIドキュメント](http://www.javadoc.io/doc/com.github.ben-manes.caffeine/caffeine)を参照して[ください](https://github.com/ben-manes/caffeine/wiki)。
>
> Caffeineは、Google GuavaにインスパイアされたAPIを使用してインメモリキャッシュを提供します。改善点は、[Guavaのキャッシュ](https://github.com/google/guava/wiki/CachesExplained)と[ConcurrentLinkedHashMap](https://code.google.com/p/concurrentlinkedhashmap)[の](https://github.com/google/guava/wiki/CachesExplained)設計経験に基づいています。
>
> [github](https://github.com/ben-manes/caffeine)

## 目次

- [サンプル](#サンプル)
  - [実装](#実装)
  - [実行結果](#実行結果)
  - [設定できる主なパラメータ](#設定できる主なパラメータ)
- [まとめ](#まとめ)

## サンプル

### 実装

**build.gradle**

```gradle
implementation 'org.springframework.boot:spring-boot-starter-cache'
implementation 'com.github.ben-manes.caffeine:caffeine'
```

**application.yml**

```yaml
spring:
  cache:
    cache-names: sampleCache # キャッシュさせるメソッドを識別する名前、複数指定する場合はカンマつなぎでいける
    caffeine:
      spec: expireAfterWrite=5s # キャッシュが作られてから5秒後に破棄される設定、他にもミリ秒・分・時・日とか指定できる
```

**メインクラスに@EnableCachingをつけてやります**

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

@SpringBootApplication
@EnableCaching
public class TutorialSpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(TutorialSpringBootApplication.class, args);
    }
}
```

**キャッシュさせたいメソッドに@Cacheable("application.ymlで定義したcache-names")を付与**

```java
package com.example.demo.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
@Slf4j
public class SampleService {

    @Cacheable("sampleCache")
    public String sampleCache(String message) {
        log.info("キャッシュ効いてない, message=" + message);
        return message;
    }
}
```

**動作確認用のコントローラ**

```java
package com.example.demo.controller;

import com.example.demo.service.SampleService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/sample")
@RequiredArgsConstructor
@Slf4j
public class SampleController {

    private final SampleService sampleService;

    @GetMapping
    public String get(@RequestParam(name = "message") String message) {
        log.info("処理開始");
        String str = sampleService.sampleCache(message);
        log.info("処理終了");
        return str;
    }
}
```

### 実行結果

```bash
// 1回目のリクエスト
2020-07-25 08:45:16.406  ・・・省略・・・     : 処理開始
2020-07-25 08:45:16.411  ・・・省略・・・     : キャッシュ効いてない, message=hello
2020-07-25 08:45:16.412  ・・・省略・・・     : 処理終了

// 2回目のリクエスト
// 1回目のリクエストから5秒以内、1回目のリクエストと同じパラメータ
2020-07-25 08:45:17.490  ・・・省略・・・     : 処理開始
2020-07-25 08:45:17.491  ・・・省略・・・     : 処理終了

// 3回目のリクエスト
// 1回目のリクエストから5秒以内、1回目のリクエストと異なるパラメータ
2020-07-25 08:45:19.189  ・・・省略・・・     : 処理開始
2020-07-25 08:45:19.190  ・・・省略・・・   : キャッシュ効いてない, message=HELLO
2020-07-25 08:45:19.190  ・・・省略・・・     : 処理終了

// 4回目のリクエスト
// 1回目のリクエストから5秒以上経過、1回目のリクエストと同じパラメータ
2020-07-25 08:45:21.689  ・・・省略・・・     : 処理開始
2020-07-25 08:45:21.689  ・・・省略・・・   : キャッシュ効いてない, message=hello
2020-07-25 08:45:21.691  ・・・省略・・・     : 処理終了
```

うん、想定通り動いてそうです

### 設定できる主なパラメータ

application.ymlで設定できる主なパラメータについてまとめときます

| **項目** | **内容** |
|---|---|
| maximumSize | キャッシュに含めることができるエントリの最大数を指定できる<br>maximumWeightと併用不可 |
| maximumWeight | キャッシュに含めることができるエントリの最大のサイズを指定<br>maximumSizeと併用不可 |
| expireAfterWrite | 最初にキャッシュが作られてから一定の時間が経過すると、キャッシュが削除される |
| expireAfterAccess | 最後のアクセスから一定の時間が経過すると、キャッシュが削除される |

詳しくは[こちら](https://www.javadoc.io/doc/com.github.ben-manes.caffeine/caffeine/latest/com/github/benmanes/caffeine/cache/Caffeine.html)

## まとめ

簡単にキャッシュ機能を実装できました！