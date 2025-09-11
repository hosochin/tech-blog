---
layout: post
title: 【Spring】staticなメソッドからbeanを参照する
date: 2022-03-15
tags: [Spring, Java]
---

# はじめに

お世話にになります、hosochinです

さて、今回は  
**「staticなメソッドからbeanを参照する」**  
です

まああんまりないとは思いますが…最近出会したのでメモです

# やってみる

たとえばこんな感じでSampleComponentをコンストラクタインジェクションしているSampleUtilクラスがあったとして

```java
@Component
@RequiredArgsConstructor
public class SampleUtil {
    private final SampleComponent sampleComponent;

    public String getMessage() {
        return sampleComponent.getSampleComponentMessage();
    }
}
```

getMessageメソッドをstaticなメソッドにしたくなったとき、以下のようにすればいけるよっていう話です👍

```java
@Component
public class SampleUtil {
    // finalは除去, staticを付与
    private static SampleComponent sampleComponent;

    // コンストラクタを追加(lombokだと意図したコンストラクタが生成されないためちゃんと記述する）
    public SampleUtil(SampleComponent sampleComponent) {
        SampleUtil.sampleComponent = sampleComponent;
    }

    // staticメソッドに変更
    public static String getMessage() {
        return sampleComponent.getSampleComponentMessage();
    }
}
```

残念ながらfinalはつけられないです…まあそりゃそうですね

また、staticの初期化 -> bean読み込み -> DI(コンストラクタインジェクション)の順番だと思うんで、SampleUtilのコンストラクタが呼ばれる前にsampleComponent.getSampleComponentMessage()を使えばぬるぽになるはずなので注意が必要ですね

ついでにセッターインジェクションでやる方法も載せときます

```java
@Component
public class SampleUtil {
    private static SampleComponent sampleComponent;

    @Autowired
    public void setSampleComponent(SampleComponent sampleComponent) {
        SampleUtil.sampleComponent = sampleComponent;
    }

    public static String getMessage() {
        return sampleComponent.getSampleComponentMessage();
    }
}
```
