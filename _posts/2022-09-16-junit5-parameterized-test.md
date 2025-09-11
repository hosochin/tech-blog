---
layout: post
title: 【Junit5】ParameterizedTestを使ってみる
date: 2022-09-16
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
  - [テストコード](#テストコード)
  - [説明](#説明)
- [パラメータにnullや空文字列を含める場合](#パラメータにnullや空文字列を含める場合)
  - [テストコード](#テストコード-1)
- [2つ以上のパラメータをテストしたい場合](#2つ以上のパラメータをテストしたい場合)

# シンプルなParameterizedTest

## テストコード

```java
// テストメソッド
@ParameterizedTest
@ValueSource(ints = {0, 1})
void test(int num) {
    assertTrue(checkNum(num));
}

// テスト対象のメソッド
public boolean checkNum(Integer num) {
    if (num == null || num == 0 || num == 1) {
        return true;
    }
    return false;
}
```

## 説明

ParameterizedTestを使うにはおなじみTestアノテーションの代わりにParameterizedTestアノテーションを付与してあげます  
次にValueSourceアノテーションでパラメータを指定します  
今回は0と1の2パターンを用意して、checkNumメソッドの結果がtrueになることを確認するテストとなっています  
テストメソッドは0, 1の順番で合計2回実行されます👍  
また、パラメータの型はプリミティブ型で用意されてそうです  
指定できるパラメータの種類については[こちら](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-ValueSource)を参照

# パラメータにnullや空文字列を含める場合

シンプルなParameterizedTestに載せたテストコードだとnullのパターンが網羅できていません  
nullのパターンを含める場合はNullSourceアノテーションを付与するだけです（テストメソッドの引数はIntegerにしてあげる必要があります）  
以下のテストメソッドは0, 1, nullの順番でテストが実行されます

## テストコード

```java
// テストメソッド
@ParameterizedTest
@ValueSource(ints = {0, 1})
@NullSource
void test(Integer num) {
    assertTrue(checkNum(num));
}
```

ちなみにパラメータがStringの場合はEmptySourceで空文字列のパラメータ、NullAndEmptySourceでnullと空文字列の両方のパラメータを実行してくれます

```java
// テストメソッド
@ParameterizedTest
@NullSource // nullのパラメータで実行
@EmptySource // 空文字列のパラメータで実行
@NullAndEmptySource // nullと空文字列のパラメータで実行
void test(String str) {
    assertTrue(checkStr(str));
}
```

# 2つ以上のパラメータをテストしたい場合

ValueSourceだと、1度に複数のパラメータを渡すことができません  
複数パラメータを渡す方法については、こちらの記事にまとめています👍

> [【Junit5】【ParameterizedTest】 2つ以上のパラメータをテストしたい場合]({{ '/junit5-parameterized-test-multiple-parameters' | relative_url }})
