---
layout: post
title: "【Java】java.lang.NoClassDefFoundError: javax/annotation/Generated"
date: 2021-07-15
tags: [java, android, gradle, annotation, error-fix]
---

# はじめに

お世話になります、hosochinです

最近AndroidStudioをアップデートしたら、

```java
Error:java: java.lang.NoClassDefFoundError: javax/annotation/Generated
```

って急に出るようになっちゃって困ったので解決策をメモっときます🍺

# 目次

- [解決方法](#解決方法)

# 解決方法

javaxのライブラリを追加してやる必要があるぽい  
以下をbuild.gradleに追加する

```gradle
implementation 'javax.annotation:javax.annotation-api:1.3.1'
annotationProcessor("javax.annotation:javax.annotation-api:1.3.2")
```

バージョンとかの詳細は[こちら](https://mvnrepository.com/artifact/javax.annotation/javax.annotation-api)ご覧ください  

annotationProcessorも入れてやる必要あり
