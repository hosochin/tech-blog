---
layout: post
title: 【Spring Boot】AOPを使ってアノテーションを自作してみた
date: 2020-07-12
tags: [spring-boot, java, aop, annotation, backend, aspect-oriented]
---

## はじめに

お世話になります、hosochinです

さて、今回は
**「Spring BootのAOPを使ってアノテーションを自作」**
です

## 目次

- [AOPとは](#AOPとは)
- [サンプル](#サンプル)
- [まとめ](#まとめ)

## AOPとは

AOPとはアスペクト指向プログラミングの略です

> **アスペクト指向プログラミング**（アスペクトしこうプログラミング、Aspect Oriented Programming、**AOP**）は、[オブジェクト指向](https://ja.wikipedia.org/wiki/%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91)ではうまく分離できない特徴（クラス間を横断(cross-cutting)するような機能）を「[アスペクト](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%82%B9%E3%83%9A%E3%82%AF%E3%83%88)」とみなし、[アスペクト記述言語](https://ja.wikipedia.org/w/index.php?title=%E3%82%A2%E3%82%B9%E3%83%9A%E3%82%AF%E3%83%88%E8%A8%98%E8%BF%B0%E8%A8%80%E8%AA%9E&action=edit&redlink=1)をもちいて分離して記述することでプログラムに柔軟性をもたせようとする試み
>
> [wikipedia](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%82%B9%E3%83%9A%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0)

・・・・まあ、あれです、ログ出力とか例外のハンドリングといった、複数モジュールにまたがった処理は切り出して一箇所に集めると思うんですけど、それを実現する手法のこと（だと思ってます）

で、SpringにもこのAOPの機能があるので今回はそれを使ってみたいと思います

## サンプル

文字列を受け取って大文字や小文字に変換して返すというコントローラを作りたいと思います

この大文字 or 小文字に変換して返すという処理を自作アノテーションで実現したいと思います

アノテーションを定義しているところの @Target や @Retention については別の記事でまとめてありますのでそちらを参考にしてください

> [【Java】 アノテーションを自分で定義してみる]({{ '/java-custom-annotation/' | relative_url }})

**アノテーションを定義したクラス**

```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    ConversionType conversionType();
    enum ConversionType {
        UPPER,
        LOWER
    }
}
```

**AOP部分**

```java
@Aspect
@Component
public class AnnotationUtils {
    @Around("@annotation(com.example.demo.myannotation.MyAnnotation)")
    public String myAnnotationImpl(JoinPoint jp) {
        // controllerで受け取った引数を取り出す
        if(ArrayUtils.isEmpty(jp.getArgs())) {
            return "";
        }
        String arg = String.valueOf(jp.getArgs()[0]);

        MethodSignature signature = (MethodSignature) jp.getSignature();
        MyAnnotation instance = signature.getMethod().getAnnotation(MyAnnotation.class);

        switch (instance.conversionType()) {
            case UPPER:
                // 大文字に変換
                return arg.toUpperCase();
            case LOWER:
                // 小文字に変換
                return arg.toLowerCase();
            default:
                return "";
        }
    }
}
```

**自作アノテーションを付与したコントローラ**

```java
@RestController
@RequestMapping
public class SampleController {

    /**
     * リクエストパラメータの文字列を大文字に変換して返すcontroller
     */
    @GetMapping("/upper-case")
    @MyAnnotation(conversionType = MyAnnotation.ConversionType.UPPER)
    public String getUpperCase(@RequestParam(name = "message") String message) {
        return message;
    }

    /**
     * リクエストパラメータの文字列を小文字に変換して返すcontroller
     */
    @GetMapping("/lower-case")
    @MyAnnotation(conversionType = MyAnnotation.ConversionType.LOWER)
    public String getLowerCase(@RequestParam(name = "message") String message) {
        return message;
    }
}
```

**実行結果**

```bash
curl "http://localhost:8080/upper-case?message=test"
TEST
curl "http://localhost:8080/lower-case?message=TEST"
test
```

## まとめ

とりあえずやりたいことはできました

まあ今回の場合とかは普通にサービスクラスでも切ってそっちでやれよって感じがしますが。。。

ログ出力やエラーハンドリングなんかは自分で定義したアノテーション使ってみるといいかもですね！

Spring-bootのAOPについてはまたどこかでまとめたいと思います