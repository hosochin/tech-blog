---
layout: post
title: 【Java】 初期化ブロックの色々
date: '2020-04-01'
tags: [Java]
---

# はじめに
お世話になります、hosochinです

さて、今回は
変数の初期化ブロック
について紹介していきます

# 目次

- [サンプル](#サンプル)
  - [初期化ブロック](#初期化ブロック)
  - [初期化ブロックの処理順序](#初期化ブロックの処理順序)
  - [コレクションの初期化ブロック](#コレクションの初期化ブロック)
- [まとめ](#まとめ)

# サンプル

## 初期化ブロック
初期化ブロックはオブジェクトの初期化を行うための仕組みの1つです

```java
public class TutorialInitializationBlock {
    String s;
    {
        s = "foo";
    }

    public void print() {
        System.out.println(s); // foo
    }
}
```

はい、こんな感じのやつですね
ブロックの中で変数の初期化が行われます

## 初期化ブロックの処理順序
フィールドの初期化、初期化ブロックは上から書かれた順に実行されます
同じ変数を初期化ブロックで初期化する場合、通常の代入と同様上書きされていきます

```java
public class TutorialInitializationBlock {
    // フィールド内の初期化ブロックは上から順に処理されていく
    String s;
    int i;
    {
        // 1つのブロックで複数の変数を初期化可能
        s = "foo";
        i = 1;
        System.out.println("1つ目のブロック [s:" + s + ", i:" + i + "]");
    }
    {
        // 上書き可能
        i = 2;
        System.out.println("2つ目のブロック [s:" + s + ", i:" + i + "]");
    }

    public void print() {
        {
            // メソッドの中でも有効
            s = "bar";
            i = 3;
            System.out.println("メソッドの中のブロック [s:" + s + ", i:" + i + "]");
        }
        System.out.println(s);
        System.out.println(i);
    }
}
```

実行結果

```bash
1つ目のブロック [s:foo, i:1]
2つ目のブロック [s:foo, i:2]
メソッドの中のブロック [s:bar, i:3]
bar
3
```

## static初期化ブロック

static初期化ブロック はクラス自身の初期化のタイミングで実行されます
static finalな変数はstatic初期化ブロックを使って初期化します

```java
public class TutorialInitializationBlock {
    static String s1;
    { // 通常の初期化ブロック
        s1 = "static";
    }

    final String s2;
    { // 通常の初期化ブロック
        s2 = "final";
        // s2 = "final"; 再代入のためコンパイルエラー
    }

    static final String s3;
    static { // static初期化ブロック
        s3 = "static final";
        // s3 = "static final"; 再代入のためコンパイルエラー
    }
}
```

## コレクションの初期化ブロック
コレクションの初期化は匿名クラスと初期化ブロックを使ってできます
また、初期化ブロック内でfor文などを利用した初期化が便利です
ダイヤモンド演算子は使えないので<>の中に型を宣言しないといけないです

```java
public class TutorialInitializationBlock {
    List<Integer> list = new ArrayList<Integer>() {
        { // 匿名クラス
            add(1);
            add(2);
            add(3);
        }
    };

    Map<Integer, String> map = new HashMap<Integer, String>() {
        { // 匿名クラス
            for (Integer i : list) {
                put(i, "value");
            }
        }
    };

    static final List<String> staticList = new ArrayList<String>() {
        {
            for (int count = 0; count < 10; count++) {
                add(String.valueOf(count));
            }
        }
    };

    public void print() {
        System.out.println(list);
        System.out.println(map);
        System.out.println(staticList);
    }
}
```

実行結果

```bash
[1, 2, 3]
{1=value, 2=value, 3=value}
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

# まとめ
以上、javaの初期化ブロックの使い方でした
初期化ブロックを使うことで可読性が上がることも多そうですね、フィールドに沢山変数を持ったクラスなどは見直してみてもいいかもです
