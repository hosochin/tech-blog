---
layout: post
title: 【Java】 アノテーションを自分で定義してみる
date: 2020-04-06
tags: [java, annotation, reflection]
---

# はじめに

お世話になります、hosochinです  

さて、今回は  
**アノテーションを自作**  
してみようと思います  

具体的なサンプルコードの前にメタアノテーションについて軽く説明します

# 目次

- [メタアノテーション](#メタアノテーション)
  - [標準メタアノテーション](#標準メタアノテーション)
  - [Targetについて](#targetについて)
  - [Retentionについて](#retentionについて)
- [サンプルコード](#サンプルコード)
  - [アノテーションでコールバックパターン](#アノテーションでコールバックパターン)
- [まとめ](#まとめ)

# メタアノテーション

メタアノテーションとはアノテーションの宣言自体を修飾するアノテーションです  
アノテーションを自分で定義する場合はこのメタアノテーションを使います

### 標準メタアノテーション

標準メタアノテーションについて以下に示します 

| アノテーションの種類 | 説明 |
|---|---|
| Target | アノテーションの対象の要素を指定 |
| Retention | アノテーションの存在期間を指定 |
| Documented | アノテーションを文書化対象するjavadocへの指定 |
| Inherited | アノテーションが自動的に継承されることを指定 |
| Repeatable | 同じ対象要素に同じアノテーションを複数指定可能 |

### Targetについて

アノテーションの対象要素を次のように指定します

```java
@Target(ElementType.METHOD)
public @interface TutorialAnnotation { }
```

ElementTypeには以下の種類があります

| ElementTypeの種類 | 説明 |
|---|---|
| ANNOTATION_TYPE | アノテーション定義に使える |
| CONSTRUCTOR | コンストラクタ定義に使える |
| FIELD | フィールド定義に使える |
| LOCAL_VARIABLE | ローカル変数定義に使える |
| METHOD | メソッド定義に使える |
| PACKAGE | パッケージ定義に使える |
| PARAMETER | メソッドのパラメータ定義に使える |
| TYPE | クラス、インタフェース(アノテーション、enum含む)定義に使える |
| TYPE_PARAMETER | 型パラメータ定義に使える |
| TYPE_USE | 型を使える場所全てに使える |

### Retentionについて

アノテーションの存在期間を次のように指定します

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface TutorialAnnotation { }
```

RetentionPolicyには以下の種類があります

| RetentionPolicyの種類 | 説明 |
|---|---|
| SOURCE | アノテーションはコンパイラによって破棄される |
| CLASS | アノテーションはコンパイラによってクラスファイルに記録される（デフォルトの設定） |
| RUNTIME | アノテーションはコンパイラによってクラスファイルに記録され、実行時に参照可能 |

# サンプルコード

**アノテーションを定義**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    String value();
}
```

**MyAnnotationアノテーションをクラスに付与**

```java
@MyAnnotation(value = "sample")
public class Sample {
}
```

**リフレクションを使って呼び出す**

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // リフレクションでクラス名を探す
        Class<?> targetClass = Class.forName("Sample");
        // MyAnnotationアノテーションを探す
        MyAnnotation myAnnotation = targetClass.getAnnotation(MyAnnotation.class);
        System.out.println(myAnnotation.value());
    }
}
```

**実行結果**

```
sample
```

## アノテーションでコールバックパターン

自作アノテーションをちょっと応用してみて、コールバックパターンを書いてみます  
今回は文字列をCamelケースからSnakeケースに、小文字から大文字に変換するプログラムを例にやってみようと思います

**アノテーションを定義**

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyFilter{}
```

**MyFilterアノテーションをメソッドに付与**

```java
public class CamelToSnake {

    // CamelケースからSnakeケースに変換
    @MyFilter
    public String replace(String str) {
        return CaseFormat.UPPER_CAMEL.to(CaseFormat.LOWER_UNDERSCORE, str);
    }
}
```

```java
public class Capitalize {

    // 大文字に変換
    @MyFilter
    public String replace(String str) {
        return str.toUpperCase();
    }
}
```

**リフレクションを使って呼び出す**

```java
public class TutorialMyAnnotation {

    private static class Filter {
        Object object;
        Method method;

        Filter(Object object, Method method) {
            this.object = object;
            this.method = method;
        }
    }

    private List<Filter> filters = new ArrayList<>();

    public TutorialMyAnnotation(List<String> classNames) throws Exception {
        for (String className : classNames) {
            // リフレクションでクラス名を探す
            Class clazz = Class.forName(className);
            for (Method method : clazz.getMethods()) {
                // MyFilterアノテーションを探す
                if (method.isAnnotationPresent(MyFilter.class)) {
                    filters.add(new Filter(clazz.newInstance(), method));
                }
            }
        }
    }

    public void print(String input) throws Exception {
        String output = input;
        for (Filter filter : filters) {
            output = (String) filter.method.invoke(filter.object, output);
        }
        System.out.println(output);
    }
}
```

```java
public class Main {
    public static void main(String[] args) throws Exception {
        List<String> classNames = Arrays.asList(
                "CamelToSnake", "Capitalize");

        TutorialMyAnnotation tutorialMyAnnotation = new TutorialMyAnnotation(classNames);
        tutorialMyAnnotation.print("replaceString");
    }
}
```

**実行結果**

```
REPLACE_STRING
```

# まとめ

アノテーションを自分で定義することができました  
リフレクションについてもどこかでまとめたいなと思います
