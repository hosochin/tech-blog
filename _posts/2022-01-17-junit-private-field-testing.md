---
layout: post
title: 【Junit】【Spring】privateなフィールドの値を書き換える
date: 2022-01-17
tags: [junit, spring, testing, reflection, mockito, java]
---

## はじめに

お世話になります、hosochinです

今回は  
**「Junitでテスト対象クラスのprivateなフィールドの値を書き換える」です**

## 目次

- [やってみる](#やってみる)
  - [ReflectionTestUtilsを使う](#reflectiontestutilsを使う)
  - [Whiteboxを使う](#whiteboxを使う)
  - [リフレクションで書く](#リフレクションで書く)
  - [JMockitを使う](#jmockitを使う)

## やってみる

* テスト対象のクラス

```java
@Getter
public class Sample {

    private String privateFiled;

}
```

### ReflectionTestUtilsを使う

Springプロジェクトなら[ReflectionTestUtils](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/util/ReflectionTestUtils.html)がおすすめ

```java
import org.junit.jupiter.api.Test;
import org.springframework.test.util.ReflectionTestUtils;

public class SampleTest {

    @Test
    void privateなフィールドを書き換える_ReflectionTestUtils() {
        // テスト対象のクラス
        Sample target = new Sample();

        // 値を書き換え
        ReflectionTestUtils.setField(target, "privateFiled", "値を書き換え");

        // 結果
        System.out.println(target.getPrivateFiled());
    }
}
```

### Whiteboxを使う

mockitoの[Whitebox](https://www.javadoc.io/doc/org.mockito/mockito-core/1.9.0/org/mockito/internal/util/reflection/Whitebox.html)を使った方法です

```java
import org.junit.jupiter.api.Test;
import org.mockito.internal.util.reflection.Whitebox;

public class SampleTest {

    @Test
    void privateなフィールドを書き換える_Whitebox() {
        // テスト対象のクラス
        Sample target = new Sample();

        // 値を書き換え
        Whitebox.setInternalState(target, "privateFiled", "値を書き換え");

        // 結果
        System.out.println(target.getPrivateFiled());
    }
}
```

### リフレクションで書く

モックライブラリを使わずに実現する方法です

```java
import org.junit.jupiter.api.Test;
import java.lang.reflect.Field;

public class SampleTest {

    @Test
    void privateなフィールドを書き換える_リフレクション() throws Exception {
        // テスト対象のクラス
        Sample target = new Sample();

        // private変数のフィールドを取得
        Field field = target.getClass().getDeclaredField("privateFiled");
        // private変数へのアクセス制限を解除
        field.setAccessible(true);
        // 値を書き換え
        field.set(target, "値を書き換え");

        // 結果
        System.out.println(target.getPrivateFiled());
    }
}
```

### JMockitを使う

こちらはDeencapsulationというクラスのsetFiledメソッドを使う方法

ただしバージョン1.47でDeencapsulationが廃止されているため注意

[JMockit – Development history](http://jmockit.github.io/changes.html)

```java
import org.junit.jupiter.api.Test;

public class SampleTest {

    @Test
    void privateなフィールドを書き換える_jmockit() {
        // テスト対象のクラス
        Sample target = new Sample();

        // 値を書き換え
        Deencapsulation.setField(target, "privateFiled" ,"値を書き換え");

        // 結果
        System.out.println(target.getPrivateFiled());
    }
}
```