---
layout: post
title: 【SpringCache】同一オブジェクト内のメソッド呼び出しでキャッシュが効かなかった話
date: 2022-01-27
tags: [spring-boot, cache, caffeine, proxy, aop, java]
---

## はじめに

お世話になります、hosochinです

さて、今回は  
**「Springのキャッシュ機構で、同一オブジェクト内のメソッド呼び出しにキャッシュが適用されなかった話」**  
です

今回のサンプルにはCaffeineを使ってます

Caffeineについてはこちら

・[公式](https://github.com/ben-manes/caffeine)

・[【Spring Boot】Caffeineを使ってローカルキャッシュしてみる]({{ '/spring-boot-caffeine-cache' | relative_url }})

## 目次

- [やってみる](#やってみる)
  - [問題は同一オブジェクトからキャッシュ対象のメソッドを呼んだとき](#問題は同一オブジェクトからキャッシュ対象のメソッドを呼んだとき)

## やってみる

検証に使ったソースコードをばーっと貼っときます

* build.gradle

```gradle
implementation 'org.springframework.boot:spring-boot-starter-cache'
implementation 'com.github.ben-manes.caffeine:caffeine'
```

* キャッシュさせるメソッドを実装したクラス

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
@Slf4j
public class SampleService {

    public String loadMessage(String message) {
        // 同一クラス内のCacheableを付与したメソッドを呼び出す
        return loadMessageCache(message);
    }

    @Cacheable("loadMessageCache")
    public String loadMessageCache(String message) {
        log.info("キャッシュ効いてない, message=" + message);
        return message;
    }
}
```

* CacheConfigクラス

```java
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableCaching
public class CacheConfig {
}
```

* application.yml

```yaml
spring:
  cache:
    # キャッシュのキー名
    cache-names: loadMessageCache
    caffeine:
      # 10秒キャッシュ
      spec: expireAfterWrite=10s
```

動かしてみると・・・

loadMessageCacheを呼び出してみると引数が同じ場合にキャッシュが効いていることがわかります

が、loadMessageを呼んでみると…毎回ログが出力されてキャッシュが効いていないことがわかります😇

### 問題は同一オブジェクトからキャッシュ対象のメソッドを呼んだとき

[Spring Framework ドキュメント](https://spring.pleiades.io/spring-framework/docs/current/reference/html/integration.html#cache-annotation-enable) に書いてありました😎

プロキシモード（デフォルト）では、プロキシを介して受信する外部メソッド呼び出しのみがインターセプトされます。これは、**呼び出されたメソッドが @Cacheable でマークされている場合でも、自己呼び出し（実際には、ターゲットオブジェクトの別のメソッドを呼び出すターゲットオブジェクト内のメソッド）が実行時に実際のキャッシュにつながらない**ことを意味します。この場合、aspectj モードの使用を検討してください。また、期待される動作を提供するためにプロキシを完全に初期化する必要があるため、初期化コード（つまり @PostConstruct）でこの機能に依存しないでください。

なるほど、デフォルトではキャッシュが効かないようです

警告も出ないしエラーにもならないので注意が必要ですね

また、「aspectj modeの使用を検討してください。」とありますが、実装が複雑になりがち&性能にも跳ねる可能性があるそうで、可能なら別オブジェクトから呼び出すなどして回避するのが良さそうです👍