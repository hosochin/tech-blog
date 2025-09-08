---
layout: post
title: 【Java】Arrays.asListの注意点
date: 2021-09-12
tags: [java, arrays, list, collection, exception]
---

# はじめに

お世話になってます、hosochinです

今回は  
**「Arrays.asListの注意点」**  
です

Arrays.asList()って便利ですが使うときにはちょっと注意しないとだったりします

# 目次

- [Arrays.asListについて](#arraysaslistについて)
  - [それでも1行で書きたい場合...](#それでも1行で書きたい場合)

# Arrays.asListについて

Arrays.asList()は、java.util.Arraysで定義されているメソッドです

便利ですが間違った使い方にならないよう注意しないとです

まずは普通にリスト作って要素を追加するとき

```java
// 普通にListにa, b, cという要素を追加するとき
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
```

追加する要素の分だけaddを書くのってちょっとめんどいですよね

そこでArrays.asListを使うと...

```java
// Arrays.asListを使うと
List<String> list = Arrays.asList("a", "b", "c");
```

おお、1行でかけていい感じ、便利ですね

・・・って感じで何も考えずにArrays.asList使ってると思わぬバグを生むので気を付けましょう😎

こんなパターンです

```java
// Arrays.asList使う
List<String> list = Arrays.asList("a", "b", "c");

// 要素の追加 → UnsupportedOperationExceptionが発生する
list.add("d");

// 要素の削除 → UnsupportedOperationExceptionが発生する
list.remove(0);
```

上記のコードはコンパイルエラーにはなりませんが、実行するとUnsupportedOperationExceptionが発生します

これはなぜかと言うと、Arrays.asListは固定長のリストを返却するためです

[API仕様](https://docs.oracle.com/javase/jp/8/docs/api/java/util/Arrays.html#asList-T...-) にも

> 指定された配列に連動する固定サイズのリストを返します。

ってありますね

Arrays.asList()は配列をListにラップしてるみたいなイメージってことですかねー

そのため、サイズが変わるような操作（要素の追加・削除）はできないです

## それでも1行で書きたい場合...

以下は要素の追加・削除が可能なリストになります👍

```java
List<String> list = new ArrayList<>(Arrays.asList("a", "b", "c"));
```
