---
layout: post
title: 【Spring Boot】リクエストパラメータに日時を渡す
date: 2020-08-16
tags: [Spring Boot, Java]
---

# はじめに

お世話になります、hosochinです  
さて、今回は  
**「Spring Bootのリクエストパラメータに日時を渡してみる」**  
です

# 目次

- [サンプル](#サンプル)
  - [ソースコード](#ソースコード)
  - [実行結果](#実行結果)
- [まとめ](#まとめ)

# サンプル

## ソースコード

リクエストパラメータに@DateTimeFormatをつけてやることでそれぞれの型に変換されます

**コントローラ**

```java
package com.example.demo.controller;

import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;

@RestController
@RequestMapping("/sample")
public class SampleController {

    @GetMapping
    public String get(
            @RequestParam(name = "date") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate date,
            @RequestParam(name = "time") @DateTimeFormat(iso = DateTimeFormat.ISO.TIME) LocalTime time,
            @RequestParam(name = "dateTime") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE_TIME) LocalDateTime dateTime) {
        
        return "日：" + date.toString() + ", " +
                "時間：" + time.toString() + ", " +
                "日時：" + dateTime.toString();
    }
}
```

## 実行結果

```bash
curl "http://localhost:8080/sample?date=2020-08-01&time=10:00:00.001&dateTime=2020-08-01T10:00:00.001"
日：2020-08-01, 時間：10:00:00.001, 日時：2020-08-01T10:00:00.001
```

# まとめ

@DateTimeFormatアノテーションを使うことで、Spring Bootでリクエストパラメータとして渡された文字列を適切な日時型（LocalDate、LocalTime、LocalDateTime）に自動変換できます。ISO形式を指定することで標準的な日時フォーマットを簡単に扱えるようになります。
