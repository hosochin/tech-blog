---
layout: post
title: 【Java】日時の取得と変換について軽くまとめ
date: 2021-05-28
tags: [java, date-time, datetime, api, timezone]
---

## はじめに

お世話になります、hosochinです

今回は
Javaの「**日時の取得と変換**」
についてです

よく忘れます👍

## 目次

- [Date and Time APIについて](#date-and-time-apiについて)
  - [3つの時間表現](#3つの時間表現)
  - [現在日時の取得](#現在日時の取得)
  - [時間を指定して初期化](#時間を指定して初期化)
  - [文字列 > 日時の変換](#文字列--日時の変換)
  - [相互変換](#相互変換)
- [まとめ](#まとめ)

## Date and Time APIについて

Javaでは日時を扱うライブラリとして、`java.util.Date`, `java.util.Calendar`などのライブラリも存在しますが、Java8以降は「**Date and Time API**」の使用が推奨されています

なので今回は「**Date and Time API**」を取りあげます

### 3つの時間表現

| [java.time.LocalDateTime](https://docs.oracle.com/javase/8/docs/api/java/time/LocalDateTime.html) | オフセット、タイムゾーンを持たない日時<br>例：2021-05-28T10:30:29.275 |
|---|---|
| [java.time.OffsetDateTime](https://docs.oracle.com/javase/8/docs/api/java/time/OffsetDateTime.html) | オフセットを持つ日時<br>例：2021-05-28T10:30:29.275+09:00 |
| [java.time.ZonedDateTime](https://docs.oracle.com/javase/8/docs/api/java/time/ZonedDateTime.html) | タイムゾーンを持つ日時<br>例：2021-05-28T10:30:29.275+09:00[Asia/Tokyo] |

- オフセット
  - 協定世界時（UTC）からの時刻の差、日本なら+09:00
- タイムゾーン
  - 指定した地域のサマータイムなども考慮される、日本なら[Asia/Tokyo]

### 現在日時の取得

```java
// 2021-05-28T10:30:29.273
LocalDateTime.now();

// 2021-05-28T10:30:29.274+09:00
OffsetDateTime.now();

// 2021-05-28T10:30:29.275+09:00[Asia/Tokyo]
ZonedDateTime.now();
```

### 時間を指定して初期化

```java
// 2021-05-27T10:30:10
LocalDateTime.of(2021, 5, 27, 10, 30, 10, 0); 

// 2021-05-27T10:30:10+09:00
OffsetDateTime.of(2021, 5, 27, 10, 30, 10, 0, ZoneOffset.ofHours(9)); 

// 2021-05-27T10:30:10+09:00[Asia/Tokyo]
ZonedDateTime.of(2021, 5, 27, 10, 30, 10, 0, ZoneId.of("Asia/Tokyo"));
```

### 文字列 > 日時の変換

```java
// 2021-05-27T10:30:10
LocalDateTime.parse("2021-05-27T10:30:10");
// 2021-05-27T10:30:10
LocalDateTime.parse("20210527103010", DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));

// 2021-05-27T10:30:10+09:00
OffsetDateTime.parse("2021-05-27T10:30:10+09:00");
// 2021-05-27T10:30:10+09:00
OffsetDateTime.parse("2021/05/27 10:30:10.000+09:00", DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss.SSSxxxxx"));

// 2021-05-27T10:30:10+09:00[Asia/Tokyo]
ZonedDateTime.parse("2021-05-27T10:30:10+09:00[Asia/Tokyo]");
// 2021-05-27T10:30:10+09:00[Asia/Tokyo]
ZonedDateTime.parse("2021/05/27 10:30:10 Asia/Tokyo", DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss VV"));
```

### 相互変換

```java
// LocalDateTime -> OffsetDateTime
System.out.println(LocalDateTime.now().atOffset(ZoneOffset.of("+09:00")));
// LocalDateTime -> ZonedDateTime
System.out.println(LocalDateTime.now().atZone(ZoneId.of("Asia/Tokyo")));

// OffsetDateTime -> LocalDateTime
System.out.println(OffsetDateTime.now().toLocalDateTime());
// OffsetDateTime -> ZonedDateTime
System.out.println(OffsetDateTime.now().atZoneSameInstant(ZoneId.of("Asia/Tokyo")));

// ZonedDateTime -> LocalDateTime
System.out.println(ZonedDateTime.now().toLocalDateTime());
// ZonedDateTime -> OffsetDateTime
System.out.println(ZonedDateTime.now().toOffsetDateTime());
```

## まとめ

とりあえずZonedDateTime使っとけばいいらしい🍺
