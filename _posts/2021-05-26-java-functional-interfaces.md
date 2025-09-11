---
layout: post
title: 【Java】標準関数型インターフェース
date: 2021-05-26
tags: [Java]
---

# はじめに

お世話になります、hosochinです

今回は
Javaの「**標準関数型インターフェース**」
についてです

# 目次

- [標準関数型インターフェース](#標準関数型インターフェース)
  - [基本的な標準関数型インターフェース](#基本的な標準関数型インターフェース)
  - [サンプルコード](#サンプルコード)
  - [関数の合成](#関数の合成)

# 標準関数型インターフェース

標準関数型インターフェースとして、典型的なメソッドの型が`java.util.function`パッケージで定義されてます

## 基本的な標準関数型インターフェース

| インターフェース | メソッド | 説明 |
|---|---|---|
| Function <T, R> | R apply (T t) | T型の引数を受け取って、R型の結果を返す |
| Consumer <T> | void accept (T t) | T型の引数を受け取って、結果は返さない |
| Predicate <T> | boolean test(T t) | T型の引数を受け取って、boolean値の結果を返す |
| Supplier <T> | T get() | 引数無し、T型の結果を返す |

## サンプルコード

```java
// Function<T, R>
// 例：引数の文字列長を返す
Function<String, Integer> function = String::length;
System.out.println(function.apply("12345")); // 5

// Consumer<T>
// 例：引数の文字列長を出力
Consumer<String> consumer = str -> System.out.println(str.length());
consumer.accept("12345"); // 5

// Predicate<T>
// 例：引数の文字列に"foo"が含まれているか判定
Predicate<String> predicate = str -> Objects.nonNull(str) && str.contains("foo");
System.out.println(predicate.test("foo bar")); // true

// Supplier<T>
// 例："Sample"を返す
Supplier<String> supplier = () -> "Sample";
System.out.println(supplier.get()); // Sample
```

## 関数の合成

標準関数型インターフェースを合成して新しい関数型インターフェースを作成できます

`compose`、`andThen`を使って合成します

`compose`と`andThen`の違いは合成の順序です！

Predicate系は`and`、`or`、`negate`を使って合成できます

```java
// Functionの関数合成
Function<String, Integer> function1 = String::length;
Function<Integer, Integer> function2 = i -> i * 100;

// composeを使う場合
Function<String, Integer> newFunction1 = function2.compose(function1);

// andThenを使う場合
Function<String, Integer> newFunction2 = function1.andThen(function2);
System.out.println(newFunction1.apply("12345")); // 500
System.out.println(newFunction2.apply("12345")); // 500

// Predicateの関数合成
Predicate<Integer> predicate1 = i -> i % 2 == 0; // 偶数かどうか判定
Predicate<Integer> predicate2 = i -> i >= 100; // 100以上どうか判定

// 偶数かつ100以上かを判定
Predicate<Integer> newPredicate1 = predicate1.and(predicate2);

// 奇数かつ100未満かを判定(negate()は論理否定)
Predicate<Integer> newPredicate2 = predicate1.negate().and(predicate2.negate());
System.out.println(newPredicate1.test(100)); // true
System.out.println(newPredicate2.test(99)); // true
```
