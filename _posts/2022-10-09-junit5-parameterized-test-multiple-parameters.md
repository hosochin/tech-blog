---
layout: post
title: 【Junit5】【ParameterizedTest】 2つ以上のパラメータをテストしたい場合
date: 2022-10-09
tags: [Java, Junit5, テスト]
---

# はじめに

お世話になります、hosochinです  
今回はJunit5のParameterizedTestの機能を使ってみたいと思います  
こいつはJunit5から使えるアノテーションで、用意されたパラメータの分だけテストメソッドを実行してくれる便利機能です  
テストメソッドがだいぶスッキリかけそうです  
詳細については[公式ドキュメント](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests)を参照してください

# 目次

- [シンプルなParameterizedTest](#シンプルなparameterizedtest)
- [2つ以上のパラメータをテストしたい場合](#2つ以上のパラメータをテストしたい場合)
  - [MethodSource](#methodsource)
    - [テストコード](#テストコード)
    - [説明](#説明)
  - [CsvSource](#csvsource)
    - [テストコード](#テストコード-1)
    - [説明](#説明-1)
  - [EnumSource](#enumsource)
    - [テストコード](#テストコード-2)
    - [説明](#説明-2)

# シンプルなParameterizedTest

こちらの記事にまとめています

> [【Junit5】ParameterizedTestを使ってみる]({{ '/junit5-parameterized-test' | relative_url }})

# 2つ以上のパラメータをテストしたい場合

シンプルなパターンで紹介した@ValueSourceだと、1度に複数のパラメータを渡すことができません  
複数パラメータを渡す手段として以下のようなアノテーションが用意されています

- [ArgumentsSource](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-ArgumentsSource)
- [MethodSource](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-CsvFileSource)
- [CsvSource](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-CsvSource)（[CsvFileSource](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-CsvFileSource)）

## MethodSource

### テストコード

```java
// テストメソッド
@ParameterizedTest
@MethodSource("methodSource")
public void test(int param1, int param2, int expected) {
    assertEquals(expected, multiply(param1, param2));
}

// パラメータのファクトリメソッド
public static Stream<Arguments> methodSource() {
    return Stream.of(
            arguments(10, 2, 20),
            arguments(10, 3, 30)
    );
}

// テスト対象のメソッド
public int multiply(int param1, int param2) {
    return param1 * param2;
}
```

### 説明

MethodSourceを使う場合はまずパラメータを生成するstaticメソッドを定義します（テストコードでいうmethodSourceメソッド）  
このstaticメソッドはパラメータ群のStreamを返す必要があります  
テストメソッドにはMethodSourceアノテーションを付与し、定義したstaticメソッドのメソッド名を指定してやります  
パラメータ（と期待する結果）を直感的に分かりやすく記述できますね

## CsvSource

### テストコード

```java
// テストメソッド
@ParameterizedTest
@CsvSource({
        "10, 2, 20",
        "10, 3, 30"})
public void test(int param1, int param2, int expected) {
    assertEquals(expected, multiply(param1, param2));
}
```

### 説明

MethodSourceの例で挙げたテストコードをCsvSourceを使って書いてみました  
MethodSourceだとstaticメソッドを書く必要があったので、CsvSourceならより簡潔に書けそうです  
パラメータは文字列で記述する必要があり、これらは暗黙の型変換によってキャストされます  
変換ルールについては[こちら](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-argument-conversion)を参照  
まあこいつが便利なんですけど、初見だと分かりづらいかなと思ったり思わなかったり

## EnumSource

### テストコード

```java
@ParameterizedTest
@EnumSource(names = { "DAYS", "HOURS" })
void test(ChronoUnit unit) {
    // ...省略
}
```

### 説明

EnumSourceはパラメータにenumを使用することができます  
利用するにはEnumSourceアノテーションを付与してあげます  
オプションのnamesでenumの要素を指定できます（namesを省略した場合、全ての要素を指定した扱いになります）  
あと、パラメータのパターンをenumで定義してやればMethodSourceみたいなこともできます

```java
@ParameterizedTest
@EnumSource
public void test(ParamEnum paramEnum) {
    assertEquals(paramEnum.expected, multiply(paramEnum.param1, paramEnum.param2));
}

@RequiredArgsConstructor
enum ParamEnum {
    TEST1(10, 2, 20),
    TEST2(10, 3, 30);

    private final int param1;
    private final int param2;
    private final int expected;
}
```

個人的には単純なパターンのテストであれば、MethodSource使うより見やすいかなと思いました😎
