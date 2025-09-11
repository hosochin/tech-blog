---
layout: post
title: 【Java】staticの有無など・・・内部クラスの種類を整理
date: 2020-07-24
tags: [Java]
---

# はじめに

お世話になります、hosochinです

さて、今回は
**「Javaの内部クラスについてまとめてみた」**
です

あれ、これstaticつけた方がいいのかな？つけない方がいいのかな？って悩んだときがあったのでまとめておこうと思います

# 目次

- [サンプル](#サンプル)
  - [staticなネストしたクラスと非staticなネストしたクラスについて](#staticなネストしたクラスと非staticなネストしたクラスについて)
  - [staticなネストしたクラス](#staticなネストしたクラス)
  - [非staticなネストしたクラス](#非staticなネストしたクラス)
  - [ローカル内部クラス](#ローカル内部クラス)
  - [匿名クラス](#匿名クラス)
- [まとめ](#まとめ)

# サンプル

まず、ネストしたクラス（クラスの中に定義するクラス）には次の４種類があります

- **staticなネストしたクラス**
- **非staticなネストしたクラス**
- **ローカル内部クラス**
- **匿名クラス**

## staticなネストしたクラスと非staticなネストしたクラスについて

どちらも以下のような用途を持ちます

- エンクロージングクラス（内部クラスに対して外側のクラスのこと）内部だけでオブジェクトを使う場合
- ネストしたクラスの実装を親クラス内に隠蔽したい場合
- トップレベルクラスの名前を節約する場合

staticをつけるのとつけないのとで挙動が微妙に変わります

```java
public class OuterObject{
    // エンクロージングクラスのメンバ変数
    String outer = "";

    static void method() {
        // staticなネストしたクラスのインスタンスを生成
        InnerStaticObject innerStaticObject = new InnerStaticObject();
        // 非staticなネストしたクラスのインスタンスを生成
        InnerObject innerObject = new InnerObject(); // コンパイルエラー
    }

    /** staticなネストしたクラス **/
    private static class InnerStaticObject {
        void method() {
            System.out.println(outer); // コンパイルエラー
            System.out.println(OuterObject.this.outer); // コンパイルエラー
        }
    }

    /** 非staticなネストしたクラス **/
    private class InnerObject {
        void method() {
            System.out.println(outer); // 参照可能
            System.out.println(OuterObject.this.outer); // 参照可能
        }
    }
}
```

## staticなネストしたクラス

- エンクロージングオブジェクトへの参照を **持たない**
- エンクロージングオブジェクトのクラスメソッド内で内部クラス（staticなネストしたクラス）のオブジェクト生成が **できる**

## 非staticなネストしたクラス

- エンクロージングオブジェクトへの参照を（暗黙に） **持つ**
- エンクロージングクラスのクラスメソッド内で内部クラス（非staticなネストしたクラス）のオブジェクト生成が **できない**

非staticな方が余計な依存をもたらすため、基本的にはstaticありを使う方が良さそうです

## ローカル内部クラス

メソッド内、コンストラクタ内、初期化ブロック内、if節などのブロック内で定義するクラス

- 以下のような用途を持つ
  - クラスの実装をブロック内に隠蔽したい場合

```java
class MyObject {
    private void method() {
        // ローカル内部クラス
        class LocalInnerClass implements MyInterface {
            @Override
            public void print() {
                System.out.println("Local Inner Class");
            }
        }
        LocalInnerClass localInnerClass = new LocalInnerClass ();
        localInnerClass.print();
    }
}

interface MyInterface {
    void print();
}
```

## 匿名クラス

クラス名のないクラスのこと

- 以下のような用途を持ちます
  - コンストラクタが不要
  - オブジェクト作成が1つだけ

```java
class MyObject {
    private void method() {
        // 匿名クラス
        MyInterface myInterface = new MyInterface() {
            @Override
            public void print() {
                System.out.println("Anonymous Class");
            }
        };
        myInterface.print();
    }
}

interface MyInterface {
    void print();
}
```

# まとめ

内部クラスについてまとめてみました、staticつけるつけないで悩んだら基本的にはつける、で良さそうです
