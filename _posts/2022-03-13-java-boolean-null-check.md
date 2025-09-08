---
layout: post
title: 【Java】Booleanの条件分岐でnull判定も一緒に行う
date: 2022-03-13
tags: [java, boolean, null-check, commons-lang, clean-code]
---

# はじめに

お世話になります、hosochinです

タイトルのとおりですが、Boolean型の判定とかで以下のような書きっぷりを見かけることがあります

```java
// bool → Boolean型の変数
if (Objects.nonNull(bool) && bool) {
    // 処理
}
```

別に問題ないんですが、このnullチェックを毎度やるのは冗長かなって思います

個人的には次のような書き方がいいなと😎

```java
if (Boolean.TRUE.equals(bool)) {
    // 処理
}
```

もしくはBooleanUtilsっていうBoolean型を扱いやすくしたクラスがあるんでこいつ使うでもいいと思います（中身は同じことしてますが）

BooleanUtilsは他にも色々メソッドあります、詳しくは[こちら](https://commons.apache.org/proper/commons-lang/javadocs/api-release/org/apache/commons/lang3/BooleanUtils.html)

```java
if (BooleanUtils.isTrue(bool)) {
    // 処理
}

// build.gradle
implementation 'org.apache.commons:commons-lang3:3.0'
```

きれいなコードを書きたいマン🍺