---
layout: post
title: 【Java】JUnit4からJUnit5へ移行
date: 2022-08-13
tags: [java, junit, junit4, junit5, testing, migration]
---

# はじめに

お世話にになります、hosochinです  
最近JUnit4からJUnit5に移行したのでその時のメモです

# 目次

- [JUnit4とJUnit5の変更点など](#junit4とjunit5の変更点など)
  - [JUnit5を使う準備](#junit5を使う準備)
  - [アノテーション](#アノテーション)
  - [public](#public)
  - [例外のテスト方法](#例外のテスト方法)
  - [AssertAllのすすめ](#assertallのすすめ)
  - [DisplayNameのすすめ](#displaynameのすすめ)
  - [Nestedのすすめ](#nestedのすすめ)
  - [ParameterizedTestのすすめ](#parameterizedtestのすすめ)
  - [staticメソッドのmock](#staticメソッドのmock)
  - [その他](#その他)

# JUnit4とJUnit5の変更点など

## JUnit5を使う準備

**build.gradle**  
バージョン情報：<https://mvnrepository.com/artifact/org.junit.jupiter/junit-jupiter-engine>

```gradle
test {
	useJUnitPlatform()
}

testImplementation group: 'org.junit.jupiter', name: 'junit-jupiter-engine', version: '5.7.0'
```

## アノテーション

| JUnit4 | JUnit5 | 役割 |
|---|---|---|
| @RunWith, @Ruleなど | @ExtendWith | 拡張機能を読み込む |
| @Before | @BeforeEach | 各テストメソッドを実行する前に都度実行される |
| @After | @AfterEach | 各テストメソッドを実行した後に都度実行される |
| @BeforeClass | @BeforeAll | 各テストクラスを実行する前に実行される |
| @AfterClass | @AfterAll | 各テストクラスを実行した後に実行される |
| @Ignore | @Disable | テスト実行から除外する |
| @Category | @Tag | テストケースにカテゴリを付与する |

## public

JUnit5からはテストクラス, テストメソッドにpublicを付与する必要がなくなりました

## 例外のテスト方法

**JUnit4**

```java
// JUnit4
@Test(expected = RuntimeException.class)
public void 例外テスト() {
    // テスト対象のメソッド
}
```

**JUnit5**

```java
// JUnit5
@Test
void 例外テスト() {
    Assertions.assertThrows(RuntimeException.class, () -> {
        // テスト対象のメソッド
    });
}
```

## AssertAllのすすめ

JUnit5からAssertAllアノテーションが使えます  
1つのアサートに失敗しても後続のアサートは全て実行されます  
失敗箇所が一度の実行で全てわかるので何度もテストをやり直す必要もなくおすすめです

**JUnit4**

```java
// JUnit4
@Test
public void テスト() {
    // テスト対象のメソッド
    List<String> actual = target.execute();

    assertEquals(1, actual.size());    // ここで失敗したらテスト終了
    assertEquals("expected2", actual);   
}
```

**JUnit5**

```java
// JUnit5
@Test
void テスト() {
    // テスト対象のメソッド
    List<String> actual = target.execute();

    assertAll(
            () -> assertEquals(1, actual.size()),    // ここで失敗しても最後までアサートが実行される
            () -> assertEquals("expected2", actual)
    );
}
```

## DisplayNameのすすめ

JUnit5からはDisplayNameというアノテーションが追加されました  
こいつはテストレポートに任意のテスト名を付与してくれます  
JUnit4までは頑張ってメソッド名でテスト内容を表現していたかと思いますが、JUnit5からはテスト内容はDisplayNameで表現するのが良さそうです  
クラスにも付与できます

```java
@DisplayName("DisplayNameのテスト")
class DisplayNameTest {

    @Test
    @DisplayName("1 テスト")
    void case1() {
        // ...
    }

    @Test
    void case2() {
        // ...
    }

}
```

![JUnit5テストレポート](/assets/junit5-test-report.png)

## Nestedのすすめ

JUnit5からNestedアノテーションが追加されました  
Nestedアノテーションを使ってテストクラスの構造を整理できます  
例えばテスト対象クラスのメソッドごとに、テストクラスを分けるなども良さそうです

```java
class NestedTest {

    @Nested
    @DisplayName("method : メソッド1")
    class Method1 {
        @Test
        @DisplayName("正常系")
        void case1() {
            // ...
        }
    }

    @Nested
    @DisplayName("method : メソッド2")
    class Method2 {
        @Test
        @DisplayName("正常系")
        void case1() {
            // ...
        }
    }
}
```

ただ個人的にはNested見づらい…テストコードが膨大=分割しないとってことだと思うんですが、そもそも実装クラスに問題あるのでは？とか思っちゃいますよね😎

## ParameterizedTestのすすめ

JUnit5からParameterizedTestアノテーションが追加されました  
こいつは用意されたパラメータの分だけテストメソッドを実行してくれる便利機能で、めちゃめちゃ便利です  
詳しくは以下の記事でまとめています

> 関連記事：【JUnit5】ParameterizedTestを使ってみる

## staticメソッドのmock

JUnit4はPowerMockを使用した方法が主流っぽいですが、JUnit5はPowerMockに対応していないそうです  
mockito3.4以降はstaticメソッドをMockできるようになっているのでmockitoを使って実現します  
詳細は別の記事にまとめてあるのでこちらを参照お願いしますmm

> [【JUnit】staticメソッドをMockする]({{ '/junit-mock-static-methods' | relative_url }})

## その他

JUnit4で書かれているテストをJUnit5で書き直していてちょいちょいミスるんですが、JUnit4とJUnit5が混ざってると正しく動かないです

```java
import org.junit.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
public class Test {

    @Test
    public void test() {

    }

}
```

こんな感じで@TestはJUnit4の方使っちゃってたりみたいな

# まとめ

JUnit4からJUnit5への移行では、アノテーションの変更、publicキーワードの省略、より強力なアサーション機能（assertAll、assertThrows）、テストの可読性向上（@DisplayName、@Nested）、パラメータ化テストなど、多くの改善が導入されています。移行時は、インポート文の混在に注意が必要です。