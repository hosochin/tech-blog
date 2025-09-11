---
layout: post
title: 【Junit】モックで本物のメソッドを呼ぶ方法
date: 2022-05-17
tags: [Junit, テスト, Java]
---

# はじめに

お世話になります、hosochinです

今回はJunitでモックしたメソッドについて、本物のメソッドの処理を実行したいときについてです

環境はJunit5系 + Mockito3系です

# 目次

- [thenCallRealMethodを使う](#thencallrealmethodを使う)
- [@Spyを使う](#spyを使う)

# thenCallRealMethodを使う

thenCallRealMethodを使うと本物のメソッドの処理が実行されます

* テスト対象のクラス

```java
@RestController
@RequiredArgsConstructor
public class SampleController {

    private final SampleService service;

    public String getSample() {
        return service.getStr();
    }
}
```

* モックするクラス

```java
@Service
public class SampleService {
    public String getStr() {
        return "Real Method";
    }
}
```

* テストクラス

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class)
public class SampleControllerTest {

    @Mock
    private SampleService sampleService;

    @InjectMocks
    private SampleController target;

    @Test
    void test() {
        when(sampleService.getStr()).thenReturn("Mock Method");
        System.out.println(target.getSample()); // Mock Method

        when(sampleService.getStr()).thenCallRealMethod();
        System.out.println(target.getSample()); // Real Method
    }
}
```

# @Spyを使う

もちろんSpyでも同じことができます

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Spy;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class)
public class SampleControllerTest {

    @Spy
    private SampleService sampleService;

    @InjectMocks
    private SampleController target;

    @Test
    void test() {
        // 何もモックしない
        System.out.println(target.getSample()); // Real Method

        // モックする
        when(sampleService.getStr()).thenReturn("Mock Method");
        System.out.println(target.getSample()); // Mock Method
    }
}
```
