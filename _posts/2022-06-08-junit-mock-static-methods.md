---
layout: post
title: 【JUnit】staticメソッドをMockする
date: 2022-06-08
tags: [Junit, Java, テスト]
---

# はじめに

お世話にになります、hosochinです  
さて、今回は  
**「staticメソッドをMockする」**  
です

# 目次

- [サンプル](#サンプル)
  - [テスト対象のソース](#テスト対象のソース)
  - [テストソース](#テストソース)
- [まとめ](#まとめ)

# サンプル

## テスト対象のソース

**テスト対象のクラス**

```java
import org.springframework.stereotype.Service;

@Service
public class SampleService {
    public String sampleMethod() {
        // staticメソッドを呼ぶ
        return SampleStatic.staticMethod();
    }
}
```

**staticメソッドを持つクラス**

```java
public class SampleStatic {
    public static String staticMethod() {
        return "real method";
    }
}
```

## テストソース

**build.gradleに以下を追加します**  
**バージョンは適宜いい感じに指定してください**

```gradle
dependencies {
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.junit.jupiter:junit-jupiter:5.7.0'
	testImplementation 'org.mockito:mockito-core:3.6.0'
	testImplementation 'org.mockito:mockito-junit-jupiter:3.6.0'

	testImplementation 'org.mockito:mockito-inline:3.6.0'
}
```

**テストクラス**

```java
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;

import static org.mockito.Mockito.mockStatic;

class SampleServiceTest {

    // テスト対象のクラス
    private final SampleService target = new SampleService();

    @Test
    void test1() {
        // 何もせず実行
        System.out.println(target.sampleMethod()); // real methodと出力される

        // staticなメソッドをMockする
        MockedStatic<SampleStatic> mockedStatic = mockStatic(SampleStatic.class);
        mockedStatic.when(SampleStatic::staticMethod).thenReturn("mocked method");

        // Mockしてから実行
        System.out.println(target.sampleMethod()); // mocked methodと出力される

        // closeしとかないと以降のテストで失敗する
        mockedStatic.close();
    }

    /**
     * test1をtry-with-resources使って書いてみる
     */
    @Test
    void test2() {
        System.out.println(target.sampleMethod()); // real methodと出力される

        try (MockedStatic<SampleStatic> mockedStatic = mockStatic(SampleStatic.class)) {
            mockedStatic.when(SampleStatic::staticMethod).thenReturn("mocked method");

            // Mockしてから実行
            System.out.println(target.sampleMethod()); // mocked methodと出力される
        }
    }
}
```

# まとめ

staticなメソッドをMockするには、Mockito 3.4.0以降の`mockStatic()`メソッドと`mockito-inline`依存関係が必要です。重要なポイントは、MockedStaticオブジェクトを必ずclose()すること。try-with-resourcesを使用すると自動的にクリーンアップされるため推奨です。

close()を忘れると以下のエラーが発生します：

```
To create a new mock, the existing static mock registration must be deregistered
```

try-with-resources使って書く方が良さそうっすね😎
