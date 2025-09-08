---
layout: post
title: 【SQL】演算子の優先順位について
date: 2021-11-10
tags: [sql, mysql, oracle, spring-data-jpa, database, operator-precedence]
---

## はじめに

お世話になります、hosochinです

今回は  
**「【SQL】演算子の優先順位について」です**

SQLの演算子に優先順位があるってことを知らなかったのでメモ

spring data jpaのspecificationでクエリを生成しようとしていたところ

* 生成したいクエリ

```sql
SELECT * FROM WHERE 条件A OR (条件B AND 条件C)
```

* 実際に生成されたクエリ

```sql
SELECT * FROM WHERE 条件A OR 条件B AND 条件C
```

どう頑張っても期待するクエリにならずハマった。。

が、上記の２つのクエリを叩いてみると・・・取得結果は変わらず、「？」ってなった。

## SQLの演算子の優先順位

演算子の優先順位的に括弧が無くても AND が先に処理されるということでした。。必要ないからspecification側で生成されたクエリには括弧が入ってないってだけだったんですね。

参考欄にMySQLとOracleの演算子の優先順について載せておきます。

[Mysqlの演算子の優先順位](https://dev.mysql.com/doc/refman/5.6/ja/operator-precedence.html)

[Oracleの演算子の優先順位](https://docs.oracle.com/cd/E53235_01/server.761/es_eql/src/ceql_expr_precedence_rules.html)