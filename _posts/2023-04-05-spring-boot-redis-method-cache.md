---
layout: post
title: 【Spring Boot】【Redis】メソッドをキャッシュする
date: 2023-04-05
tags: [spring-boot, redis, cache, java, annotation, ttl]
---

# はじめに

お世話になります、hosochinです  
今回はSpring Bootでキャッシュの機能として **Redis** を使ってみたいと思います

# 目次

- [環境](#環境)
- [実装](#実装)
  - [build.gradle](#buildgradle)
  - [Mainクラス](#mainクラス)
  - [コントローラ](#コントローラ)
  - [サービス](#サービス)
  - [Messageクラス](#messageクラス)
  - [application.yml](#applicationyml)
- [動かしてみる](#動かしてみる)
- [TTLを設定する](#ttlを設定する)

# 環境

- Redisをローカル環境に構築
- Spring Boot : 2.3.5
- Java : 1.8
- gradle : 6.9

Redisの構築手順についてはこちらでまとめています

> [【Redis】ローカルMacにRedisを入れて動かしてみる]({{ '/redis-local-mac-setup' | relative_url }})

# 実装

## build.gradle

```gradle
dependencies {
	implementation('org.springframework.boot:spring-boot-starter-web')

	implementation('org.springframework.boot:spring-boot-starter-cache')
	implementation('org.springframework.boot:spring-boot-starter-data-redis')

	runtimeOnly('org.springframework.boot:spring-boot-devtools')
	compileOnly("org.projectlombok:lombok:${lombokVersion}")
	annotationProcessor("org.projectlombok:lombok:${lombokVersion}")

	testCompileOnly("org.projectlombok:lombok:${lombokVersion}")
	testAnnotationProcessor("org.projectlombok:lombok:${lombokVersion}")
	testImplementation('org.springframework.boot:spring-boot-starter-test')
}
```

・なんか不要なものも入ってる気もしますが  
・今回Redisでキャッシュ機能を実装するためにspring-boot-starter-cache、spring-boot-starter-data-redisを入れてます  
・Spring Cacheについての詳細は[こちら](https://docs.spring.io/spring-boot/docs/2.7.5/reference/htmlsingle/#io.caching)  
・Spring Data Redisについての詳細は[こちら](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/)

## Mainクラス

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

・@EnableCachingをつけることでキャッシュ機能を有効にしてやります

## コントローラ

```java
package com.example.demo.controller;

import com.example.demo.model.Message;
import com.example.demo.service.SampleService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class SampleController {

    private final SampleService sampleService;

    @GetMapping("/get/message")
    public Message getMessage(@RequestParam(name = "message") String message) {
        return sampleService.getMessage(message);
    }

}
```

・動作確認用に簡単なコントローラクラスを作っておきます  
・リクエストパラメータでmessageっていう適当な文字列を受け取って、自分で定義したMessageオブジェクトを返します

## サービス

```java
package com.example.demo.service;

import com.example.demo.model.Message;
import lombok.extern.slf4j.Slf4j;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
@Slf4j
public class SampleService {

    @Cacheable(cacheNames = "getMessage")
    public Message getMessage(String message) {
        log.info("キャッシュ効いてない, message=" + message);
        return new Message(message, LocalDateTime.now());
    }

}
```

・サービスクラスにキャッシュするメソッドを定義します  
・@Cacheableをつけてやることでキャッシュの対象になります  
・キャッシュがきいてるかどうかを確認するためログを仕込んでます

## Messageクラス

```java
package com.example.demo.model;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.Setter;

import java.io.Serializable;
import java.time.LocalDateTime;

@Getter
@Setter
@AllArgsConstructor
public class Message implements Serializable {

    private String message;

    private LocalDateTime updateTime;

}
```

・レスポンスに使用するMessageクラスです  
・リクエストパラメータで受け取ったmessageに、なんとなく更新時間を入れてみました  
・SerializableにしてやらないとRedisにつっこむときに怒られます

## application.yml

```yaml
spring:
  redis:
    host: localhost
    port: 6379
```

・ローカルで起動しているRedisに接続する設定です  
・Redisサーバ側の設定はデフォルトのままで特にいじってないです

# 動かしてみる

Redisとアプリケーションを起動してエントリポイント叩いてみます  
以下のことが確認できたらうまくいってそうです

1. 初回のリクエスト
   1. 仕込んだログが出力される = メソッドの中身が処理されている
2. Redisを確認
   1. keyにキャッシュ名,引数の組み合わせが登録されている
   2. valueにメソッドのreturn値が登録されている
3. 同じリクエストパラメータで2回目のリクエスト
   1. 仕込んだログが出力されない = メソッドの中身は処理されていない
   2. 1回目のレスポンスと同じレスポンスが返ってくる（updateTimeが同じ）
4. リクエストパラメータを変えてリクエスト
   1. 仕込んだログが出力される = メソッドの中身が処理されている
5. Redisを確認
   1. 新たにデータが追加されている
6. Redisのデータを削除
7. 再度リクエスト
   1. 仕込んだログが出力される = メソッドの中身が処理されている

```bash
# 1. 初回のリクエスト
$ curl "localhost:8080/get/message?message=test"
{"message":"test","updateTime":"2023-04-05T13:49:58.294"}

# 2. Redisを確認
# （Redisにログインする）
redis-cli

127.0.0.1:6379> keys *
1) "getMessage::test"
↑ メソッド名と引数が登録されてる

# 3. 同じパラメータで2回目リクエスト
$ curl "localhost:8080/get/message?message=test"
{"message":"test","updateTime":"2023-04-05T13:49:58.294"}
↑ タイムスタンプが変わっていない！キャッシュが効いてる

# 4. リクエストパラメータを変えてリクエスト
$ curl "localhost:8080/get/message?message=test2"
{"message":"test2","updateTime":"2023-04-05T14:01:05.822"}
↑ 1回目と違う結果が得られた、キャッシュが効いてない

# 5. Redisを確認
127.0.0.1:6379> keys *
1) "getMessage::test2"
2) "getMessage::test"
↑ 2回目リクエストのパラメータが追加されてる

# 6. Redisのデータを削除
127.0.0.1:6379> flushdb
OK

# 7. 再度リクエスト
$ curl "localhost:8080/get/message?message=test"
{"message":"test","updateTime":"2023-04-05T14:04:42.193"}
↑ いい感じ
```

# TTLを設定する

上記の例だとTTL（キャッシュの有効時間）が設定できていなかったですね  
TTLを設定する場合はConfigクラスを書いてやります

```java
package com.example.demo;

import org.springframework.boot.autoconfigure.cache.RedisCacheManagerBuilderCustomizer;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;

import java.time.Duration;

@Configuration
@EnableCaching
public class RedisConfig {

    @Bean
    public RedisCacheManagerBuilderCustomizer redisCacheManagerBuilderCustomizer() {
        return (builder) -> builder
                .withCacheConfiguration(
                        // キャッシュ名を指定する
                        "getMessage",
                        // TTLを指定する（ここでは10秒に設定）
                        RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofSeconds(10))
                )
                // それ以外のキャッシュしたいメソッドについても続けて書いていける
                .withCacheConfiguration(
                        "そのほかにキャッシュしたいメソッド",
                        RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofMinutes(10))
                );
    }
}
```

実行してみると、10秒でキャッシュが消えることを確認できました！

# まとめ

Spring BootでRedisを使ったメソッドキャッシュを実装しました。@Cacheableアノテーションを使用することで、簡単にメソッドの戻り値をキャッシュできます。TTL設定により、適切なキャッシュライフサイクル管理も可能です。パフォーマンス向上に効果的な機能です。