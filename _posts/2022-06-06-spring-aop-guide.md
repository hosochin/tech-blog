---
layout: post
title: 【Spring】Spring AOPについて
date: 2022-06-06
tags: [Spring, AOP, Java]
---

# はじめに

お世話にになります、hosochinです  
さて、今回は  
**「Spring AOP」** について  
です

# 目次

- [Spring AOPとは](#spring-aopとは)
- [使用可能なAdviceのアノテーション](#使用可能なadviceのアノテーション)
- [実装](#実装)
  - [Before](#before)
  - [AfterReturning](#afterreturning)
  - [AfterThrowing](#afterthrowing)
  - [After](#after)
  - [Around](#around)
- [Pointcut](#pointcut)
  - [名前付きPointcut](#名前付きpointcut)

# Spring AOPとは

> 「AOP」とは、Aspect Oriented Programmingの略称で、直訳するとアスペクト指向プログラミングのことです。プログラミングに用いられる手法の概念を意味します。
> 
> https://udemy.benesse.co.jp/development/app/aop.html#:~:text=%E3%80%8CAOP%E3%80%8D%E3%81%A8%E3%81%AF%E3%80%81Aspect,%E5%8F%8A%E3%81%B3%E6%A6%82%E5%BF%B5%E3%81%AE%E3%81%93%E3%81%A8%E3%81%A7%E3%81%99%E3%80%82

ってことらしいです  
ログ出力みたいな共通処理をプログラムから横断的に呼び出せるようになります  
[こちらの記事](https://qiita.com/ughirose/items/a7c66782f93cd1ae0d68) に用語などがまとまっていて分かりやすいかと思います  
コードレベルで言うと、JoinPoint（指定したメソッドの実行前や実行後などのタイミング）に対して、@Aspectアノテーションを付与したクラスで実装した処理を差し込めます

# 使用可能なAdviceのアノテーション

| アノテーション | 説明 |
|---|---|
| @Before | 対象のメソッドの実行前に処理が差し込まれる。 |
| @AfterReturning | 対象のメソッドが正常終了した後に処理が差し込まれる。正常終了時のメソッドの戻り値を取得することもできる。 |
| @AfterThrowing | 対象のメソッドが異常終了(例外がスローされたとき)に処理が差し込まれる。異常終了時の例外を取得することもできる。 |
| @After | 対象のメソッドが正常終了や例外のスローに関わらず、終了時に処理が差し込まれる。 |
| @Around | 最も強いAdvice。対象のメソッドの前後に処理が差し込まれる。 |

# 実装

- Before
- AfterReturning
- AfterThrowing
- After
- Around

についてそれぞれ実装例を載っけときます

## Before

**Before → 対象のメソッドの実行前に処理が差し込まれる。**

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class SampleAspect {
    @Before("execution(public * com.example.demo.controller.*.*(..))")
    public void startLog(JoinPoint jp) {
        System.out.println("メソッド開始：" + jp.getSignature());
    }
}
```

## AfterReturning

**AfterReturning → 対象のメソッドが正常終了した後に処理が差し込まれる。正常終了時のメソッドの戻り値を取得することもできる。**

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;
import com.example.demo.controller.resorces.UserResponse;

@Aspect
@Component
public class SampleAspect {
    @AfterReturning(value = "execution(public * com.example.demo.controller.*.*(..))", returning = "user")
    public void endLog(JoinPoint jp, UserResponse user) {
        System.out.println("メソッド正常終了：" + jp.getSignature());
        System.out.println("戻り値：" + user);
    }
}
```

## AfterThrowing

**AfterThrowing → 対象のメソッドが異常終了(例外がスローされたとき)に処理が差し込まれる。異常終了時の例外を取得することもできる。**

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class SampleAspect {
    @AfterThrowing(value = "execution(public * com.example.demo.controller.*.*(..))", throwing = "e")
    public void endLog(JoinPoint jp, RuntimeException e) {
        System.out.println("メソッド異常終了：" + jp.getSignature());
        e.printStackTrace();
    }
}
```

## After

**After → 対象のメソッドが正常終了や例外のスローに関わらず、終了時に処理が差し込まれる。**

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class SampleAspect {
    @After("execution(public * com.example.demo.controller.*.*(..))")
    public void endLog(JoinPoint jp) {
        System.out.println("メソッド終了：" + jp.getSignature());
    }
}
```

## Around

**Around → 最も強いAdvice。対象のメソッドの前後に処理が差し込まれる。**

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class SampleAspect {
    @Around("execution(public * com.example.demo.controller.*.*(..))")
    public Object log(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("メソッド開始：" + jp.getSignature());
        Object result;

        try {
            // 対象メソッド実行
            result = jp.proceed();
            System.out.println("メソッド終了：" + jp.getSignature());
            System.out.println("戻り値：" + result);
            return result;
        } catch (Throwable e) {
            System.out.println("メソッド異常終了：" + jp.getSignature());
            e.printStackTrace();
            throw e;
        }
    }
}
```

# Pointcut

## 名前付きPointcut

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class SampleAspect {
    @Pointcut("execution(public * com.example.demo.controller.*.*(..))")
    public void demoController() {}

    @Before("demoController()")
    public void startLog(JoinPoint jp) {
        System.out.println("メソッド開始：" + jp.getSignature());
    }
}
```

# まとめ

Spring AOPを使用することで、ログ出力や認証チェックなどの横断的関心事を、ビジネスロジックから分離して実装できます。@Before、@After、@Around等のアノテーションを使い分けることで、メソッドの実行タイミングに応じた処理を柔軟に差し込むことが可能です。
