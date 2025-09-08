---
layout: post
title: 【Java】 Streamのreduceメソッドについて
date: 2020-04-01
tags: [java, stream, functional-programming]
---

## はじめに

お世話になります、hosochinです  

さて、今回はStreamの終端処理の1つ  
**reduce処理**  
についてまとめます

reduceメソッドは、繰り返し処理で途中の計算結果を保持したい場合に使用するメソッドです  

例えば、あるint型配列の合計値を求める場合、for文などを使えば再代入用の変数が必要になります  
ラムダ式は外の変数に再代入できませんから、そういう場合にreduceメソッドを使います  

reduceメソッドには次の3種類があります 

```java
// 引数1つバージョン
Optional<T> reduce(BinaryOperator<T> accumulator);

// 引数2つバージョン
<R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner);

// 引数3つバージョン
<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);
```

特に引数3つバージョンがややこしい気がするのでこいつについてやっていきたいと思います

## 目次

- [サンプル](#サンプル)
  - [解説１](#解説１)
  - [解説２](#解説２)
  - [解説３](#解説３)
- [まとめ](#まとめ)

## サンプル

リストの中の`"dog"`を数える処理を例にしてみます

**TutorialStream.java**

```java
public class TutorialStream {
    public void countDog() {

        // 動物リスト
        List<String> animals = Arrays.asList("dog", "cat", "dog", "pig", "rabbit");

        // Listの中からdogの数を算出する
        int dogCount = animals.stream().reduce(
                0, // ・・・解説１
                (count, animal) -> { // ・・・解説２
                    System.out.println("count : " + count);
                    System.out.println("animal : " + animal);
                    // dogを見つけたらcountに1を足してく
                    return count + ("dog".equals(animal) ? 1 : 0);
                }, (count1, count2) -> null // ・・・解説３
        );
        
        System.out.println(dogCount + "匹");
    }
}
```

**実行結果**

```
count : 0
animal : dog
count : 1
animal : cat
count : 1
animal : dog
count : 2
animal : pig
count : 2
animal : rabbit
2匹
```

### 解説１

reduceメソッドの第1引数 `U identity` です  
Uはreduceメソッドの戻り値と一致する必要があるためint型になっています  
後述する第2引数の `BiFunction<T,U,R>` のapplyメソッドの第1引数の初期値になります

### 解説２

reduceメソッドの第2引数 `BiFunction<U, ? super T, U> accumulator` です  
ここでは標準関数型インターフェースBitFunctionのapplyメソッドを実装しています  
- `count`の初期値はreduceメソッドの第1引数で、計算の途中結果を保持するやつです  
- `animal`はanimalsの要素です  

BitFunctionは `apply(Object, Object)` を持つ関数インターフェースです  
`BiFunction<T,U,R>` は、以下の3つの型パラメータを持ちます  
- T・・・applyメソッドの第1引数  
- U・・・applyメソッドの第2引数  
- R・・・applyメソッドの戻り値  

（BitFunctionについて詳しくは[こちら](https://docs.oracle.com/javase/jp/8/docs/api/java/util/function/BiFunction.html)を参照）  
サンプルではapplyメソッドはint型の第1引数とString型の第2引数を受け取り、int型の戻り値を返す実装をしています

### 解説３

reduceメソッドの第3引数 `BinaryOperator<U> combiner` です  
こいつは並列処理の場合のみ使われます  
並列処理時の途中結果をマージするためのものです  
例えばサンプルコードを並列処理に書き換えた場合、以下のようになります  
count1とcount2はreduce処理の結果です

**並列処理に書き換えたソース**

```java
public class TutorialStream {
    public void countDog() {

        // 動物リスト
        List<String> animals = Arrays.asList("dog", "cat", "dog", "pig", "rabbit");

        // Listの中からdogの数を算出する
        int dogCount = animals.parallelStream().reduce(
                0,
                (count, animal) -> {
                    return "dog".equals(animal) ? 1 : 0; // dogを見つけたら1を返す
                },
                (count1, count2) -> {
                    System.out.println("count1 : " + count1 + ", count2 : " + count2);
                    return count1 += count2;
                    // 上記をInteger::sumと書いても同様の結果になる
                 }
        );

        System.out.println(dogCount + "匹"); // 2
    }
}
```

**実行結果**

```
count1 : 0, count2 : 0
count1 : 1, count2 : 0
count1 : 1, count2 : 0
count1 : 1, count2 : 1
2匹
```

## まとめ

やっぱりちょっとややこしいですね  
関数型インターフェースについてはまたどこかでまとめてみたいなーと思います  
ちなみにサンプルコードは以下のように書き換えられます（典型的な計算処理はStream APIで元から用意されています）

```java
animals.stream().filter("dog"::equals).count();
```
