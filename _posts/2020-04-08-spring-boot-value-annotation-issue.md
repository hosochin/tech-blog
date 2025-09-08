---
layout: post
title: 【Spring Boot】Valueアノテーションが効かなくて困った話
date: 2020-04-08
tags: [java, spring-boot, annotation, troubleshooting]
---

# はじめに

お世話になります、hosochinです  

さて、今回は  
**SpringのValueアノテーションが効かなくなって困った話**  
についてです

[Valueアノテーション](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/Value.html)は外部設定値を取得する場合に使えます  
フィールドの変数やコンストラクタ引数などに、外部で定義した設定値をインジェクションできるアノテーションです  
今回はこのValueアノテーションで、思ったように設定値が取得できずハマった話をします

# 目次

- [サンプルコード](#サンプルコード)
- [解決策](#解決策)
- [まとめ](#まとめ)

# サンプルコード

サービスクラスのValueアノテーションのついたフィールド変数「firstName」「lastName」を定義します  
コンストラクタ内でfullNameを初期化しています

**Serviceクラス：SampleService.java**

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

@Service
public class SampleService {

    @Value("${name.first}")
    private String firstName;

    @Value("${name.last}")
    private String lastName;

    private final String fullName;

    /**
     * コンストラクタ
     */
    public SampleService() {
        fullName = firstName + lastName;
    }

    public void print() {
        System.out.println("firstName = " + firstName);
        System.out.println("lastName = " + lastName);
        System.out.println("fullName = " + fullName);
    }
}
```

**プロパティファイル：application.yaml**

```yaml
name:
  first: Taro
  last: Tanaka
```

**動作確認用のControllerクラス：SampleController.java**

```java
@RestController
@RequestMapping("/sample")
@RequiredArgsConstructor
public class SampleController {

    private final SampleService sampleService;

    @GetMapping
    public void get() {
        sampleService.print();
    }
}
```

一見、SampleServiceクラスのfullNameは「TaroTanaka」になっているように見えますが….コントローラにリクエストしてみましょう

**実行結果**

```
firstName = Taro
lastName = Tanaka
fullName = nullnull
```

**fullName が「nullnull」になっている！**  
**しかもfirstNameとlastNameはちゃんと値が入ってる！**

# 解決策

どういうことか・・・と言いますと、コンストラクタインジェクションの時点では@Valueがまだ動いていないということです  
単純に **初期化の順番が、フィールドインジェクションはコンストラクタインジェクションより後** なんですね～  
ということでServiceクラスのコンストラクタを以下のように書き換えると想定した動きになります

**SampleService.javaのコンストラクタを修正**

```java
/**
 * コンストラクタ
 */
public SampleService(
        @Value("${name.first}") String firstName, // 引数に追加
        @Value("${name.last}") String lastName // 引数に追加
) {
    fullName = firstName + lastName;
}
```

もしくは@ConfigurationでBean登録してやればいいです

**AppConfig.java**

```java
@Configuration
public class AppConfig {
    @Value("${name.first}")
    private String firstName;

    @Value("${name.last}")
    private String lastName;

    @Bean
    SampleService sampleService() {
        return new SampleService(firstName, lastName, firstName + lastName);
    }
}
```

**SampleService.javaを修正**

```java
public class SampleService {

    private String firstName;

    private String lastName;

    private final String fullName;

    /**
     * コンストラクタ
     */
    public SampleService(
            String firstName,
            String lastName,
            String fullName
    ) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.fullName = fullName;
    }

    public void print() {
        System.out.println("firstName = " + firstName);
        System.out.println("lastName = " + lastName);
        System.out.println("fullName = " + fullName);
    }
}
```

@ConfigurationによるBeanの初期化処理はフィールドインジェクションより後なので、@Valueで既にフィールド変数が初期化されているのでこっちでもいけますね

**実行結果**

```
firstName = Taro
lastName = Tanaka
fullName = TaroTanaka
```

# まとめ

今回このようなバグを生んでしまった原因は、、、Beanのライフサイクルを正しく理解していなかったためです  
あとはフレームワークの理解が十分でなくとも、ちょっとした違和感を放置しないことですね！
