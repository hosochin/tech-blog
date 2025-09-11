---
layout: post
title: 【Java】streamでListの重複削除
date: 2022-07-03
tags: [Java]
---

# はじめに

お世話にになります、hosochinです  
JavaでListの重複を削除する方法について載せときます  
streamのdistinct使うのが良さそうです

# 実装

## 文字列リストの重複削除

```java
List<String> list = Arrays.asList("A", "B", "C", "A");

// ["A", "B", "C"] になる
list.stream().distinct().collect(Collectors.toList());
```

## オブジェクトリストの重複削除

```java
List<MyObj> list = Arrays.asList(
        new MyObj(1, "Ichiro"),
        new MyObj(2, "Jiro"),
        new MyObj(3, "Sabro"),
        new MyObj(1, "Ichiro")
);

// [(1, "Ichiro"), (2, "Jiro"), (3, "Sabro")] になる
list.stream().distinct().collect(Collectors.toList());
```

**MyObjクラス**  
**Equalsメソッドを実装する必要あり**
- lombokのEqualsAndHashCodeを付与

```java
@RequiredArgsConstructor
@EqualsAndHashCode
public class MyObj {
    private final Integer id;
    private final String name;
}
```

# まとめ

Java 8のStreamAPIのdistinct()メソッドを使用することで、簡潔にListの重複を削除できます。オブジェクトの場合は、equalsメソッドとhashCodeメソッドが適切に実装されている必要があります。Lombokの@EqualsAndHashCodeアノテーションを使用すると、これらのメソッドを自動生成できて便利です。
