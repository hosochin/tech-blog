---
layout: post
title: 【Java】オブジェクトをValidationする
date: 2021-08-21
tags: [Java]
---

# はじめに

お世話になってます、hosochinです

今回は  
**「Javaのオブジェクトをバリデーションしてみる」**  
です

入力値のバリデーションというよりかは、例えばDBからデータを取得後に加工したあとのオブジェクトとかをバリデーションするイメージでやっていこうと思います👍

# 目次

- [動かしてみる](#動かしてみる)
  - [Collectionや他のオブジェクトをフィールドに持つ場合](#collectionや他のオブジェクトをフィールドに持つ場合)

# 動かしてみる

* オブジェクトクラス

```java
@AllArgsConstructor
@Getter
public class SampleObject {
    @NotNull
    private String id;
    @NotNull
    private String name;
}
```

* バリデーションを実行するメソッド

```java
/**
 * オブジェクトをバリデーションするメソッド
 */
private void validation(Object obj) {
    ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
    Validator validator = factory.getValidator();

    // バリデーションの実行
    Set<ConstraintViolation<Object>> results = validator.validate(obj);
    // 結果をログに出力
    results.forEach(this::outputLog);
}

/**
 * ログ出力
 */
private void outputLog(ConstraintViolation<Object> result) {
    System.out.println(
            "propertyPath: " + result.getPropertyPath()
            + ", message: " + result.getMessage());
}
```

* バリデーションを呼ぶ

```java
public void check() {
    // idはnullを設定しとく
    SampleObject sampleObject = new SampleObject(null, "name");
    validation(sampleObject);
}
```

* build.gradle

```gradle
// 省略
dependencies {
    // 以下を追加しておく
    implementation 'org.hibernate.validator:hibernate-validator:6.1.5.Final'
    implementation 'jakarta.validation:jakarta.validation-api:2.0.2'
// 省略
}
```

* 実行結果

```bash
propertyPath: id, message: null は許可されていません
```

はい、意図した通りの結果が得られましたね🍺

sampleObject.idはnullを設定しましたが、SampleObjectクラスでidはNotNullとなっているので引っかかりました

## Collectionや他のオブジェクトをフィールドに持つ場合

collectionや他のオブジェクトの変数については、@Validアノテーションをつけてやらないと検証されないので注意です

```java
@AllArgsConstructor
@Getter
public class SampleObject {
    @NotNull
    private String id;

    @NotNull
    private String name;

    @Valid // ←これつけないとsubIdの検証がされない
    private List<SubSampleObject> list;

    @Valid // ←これつけないとsubIdの検証がされない
    private SubSampleObject subSampleObject;

    @AllArgsConstructor
    public static class SubSampleObject {
        @NotNull
        private String subId;
    }
}
```
