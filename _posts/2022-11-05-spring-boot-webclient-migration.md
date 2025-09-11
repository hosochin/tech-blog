---
layout: post
title: 【SpringBoot】RestTemplateからWebClientに移行
date: 2022-11-05
tags: [java, spring-boot, webclient, resttemplate, webflux, migration]
---

# はじめに

お世話になります、hosochinです  
Spring MVCでWebAPIをコールする場合によく使われる[RestTemplate](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)ですが、現在メンテナンスモードとなっていて、

"**注意** : 5.0 の時点では、このクラスはメンテナンスモードになっており、今後は変更やバグを受け入れるためのマイナーなリクエストのみが行われます。最新の API を備え、同期、非同期、ストリーミングのシナリオをサポートする `org.springframework.web.reactive.client.[WebClient](https://spring.pleiades.io/spring-boot/docs/current/reference/html/features.html#boot-features-webclient)` の使用を検討してください。"

とのことだったので、spring-web(RestTemplate)からspring-webflux(WebClient)に移行しようと思ったときのメモになります

# 目次

- [この記事についての注意](#この記事についての注意)
- [サンプルコード](#サンプルコード)
  - [外部APIを用意する](#外部apiを用意する)
  - [RestTemplateの場合](#resttemplateの場合)
  - [WebClientの場合](#webclientの場合)
- [解説](#解説)
- [参考](#参考)

# この記事についての注意

この記事ではspring-webfluxの特徴であるリアクティブプログラミングによるノンブロッキングな書きっぷりには **していないです**  
従来のspring-web的なブロッキング処理をspring-webfluxで書いただけなので注意してください

また、本来であればspring-webfluxでブロッキング処理を使用したい場合は、publishOn関数やsubscribeOn関数を使うべきです  
[こちら](https://yuya-hirooka.hatenablog.com/entry/2020/10/03/095015)の記事にあるように、webfluxでブロッキング処理を安易に実装するのは危険です（spring-webfluxでは、デフォルトで使用可能なリクエストを処理するスレッドが少ないため、すぐに詰まってしまう可能性があるようです）

# サンプルコード

外部APIにGET, POSTする場合に

- RestTemplateでの実装
- WebClientでの実装

についてまとめました

## build.gradle

```gradle
dependencies {
    implementation('org.springframework.boot:spring-boot-starter-web') // spring-web
    implementation('org.springframework.boot:spring-boot-starter-webflux') // spring-webflux
}
```

## コントローラ

```java
@RestController
@RequiredArgsConstructor
public class SampleController {

    private final TutorialRestTemplate tutorialRestTemplate;
    private final TutorialWebClient tutorialWebClient;

    // RestTemplateのGET
    @GetMapping("/restTemplate")
    public Message getRestTemplate() {
        return tutorialRestTemplate.get();
    }

    // RestTemplateのPOST
    @PostMapping("/restTemplate")
    public void postRestTemplate(@RequestBody Message message) {
        tutorialRestTemplate.post(message);
    }

    // WebClientのGET
    @GetMapping("/webClient")
    public Message getWebClient() {
        return tutorialWebClient.get();
    }

    // WebClientのPOST
    @PostMapping("/webClient")
    public void postWebClient(@RequestBody Message message) {
        tutorialWebClient.post(message);
    }
}

@AllArgsConstructor
@NoArgsConstructor
@Data
public class Message {
    private String value;
}
```

## 外部APIを用意する

こちらを参照してくださいmm  
localhost:3000/usersにGET,POSTのエントリポイントを用意します

> [【Node.js】【Express】 jsonを返却するシンプルなREST APIの作成]({{ '/nodejs-express-json-api' | relative_url }})

## RestTemplateの場合

```java
@Component
public class TutorialRestTemplate {

    private final RestTemplate restTemplate;

    // コンストラクタインジェクション
    public TutorialRestTemplate(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }

    // GETメソッド
    public Message get() {
        return restTemplate.getForObject("http://localhost:3000/users", Message.class);
    }

    // POSTメソッド
    public void post(Message message) {
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        HttpEntity<Message> request = new HttpEntity<>(message, headers);
        restTemplate.postForObject("http://localhost:3000/users", request, String.class);
    }
}
```

## WebClientの場合

```java
@Component
public class TutorialWebClient {

    private final WebClient webClient;

    // コンストラクタインジェクション
    public TutorialWebClient() {
        this.webClient = WebClient.builder()
                .baseUrl("http://localhost:3000")
                .build();
    };

    // GETメソッド
    public Message get() {
        return webClient.get()
                .uri("/users")
                .retrieve() // retrieveの後にレスポンスを抽出する方法を記述する
                .bodyToMono(Message.class) // Message型で受け取る
                .block(); // ブロッキング
    }

    // POSTメソッド
    public void post(Message message) {
        webClient.post()
                .uri("/users")
                .contentType(MediaType.APPLICATION_JSON) // リクエストボディのタイプ
                .body(Mono.just(message), Message.class) // リクエストボディ
//                .syncBody(message)
                .retrieve()
                .bodyToMono(String.class)
                .block(); // ブロッキング
    }
}
```

# 解説

RestTemplateを使った処理をWebClientで書き換えてみました  
ですが冒頭の注意書きで示したとおり、webfluxを利用する場合は本来ブロッキング処理を持たせるべきではないです  
ノンブロッキングで実装する場合はコントローラの戻り値をPublisher型（[Mono](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html)型、[Flux](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html)型）とし、関数型プログラミングによって処理を構築する必要があります（なのでRestTemplate→WebClientへの移行は場合によったらだいぶ大掛かりな改修になりそうです。。）  
今回はひとまずWebClientを使った同期処理に書き換えてみるという部分に焦点を置いてみました  
今後はspring-webfluxでのノンブロッキングな実装についても記事にできたらなあと思います😎

# 参考

<https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/reactive/function/client/WebClient.html>  
<https://spring.pleiades.io/spring-framework/docs/current/reference/html/web-reactive.html>  
<https://www.alpha.co.jp/blog/202105_02>  
<https://yuya-hirooka.hatenablog.com/entry/2020/10/03/095015>  
<https://qiita.com/kilvis/items/fb18be963da6cac03ee9>