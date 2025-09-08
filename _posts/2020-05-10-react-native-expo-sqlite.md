---
layout: post
title: 【React Native】【Expo】SQLiteを使ってみる
date: 2020-05-10
tags: [react-native, expo, sqlite, javascript, mobile-app, database]
---

## はじめに

お世話になります、hosochinです

さて、今回は
**React Native(Expo)でSQLiteを操作してみた**
です

[公式](https://docs.expo.io/versions/latest/sdk/sqlite/)みながらやっていこうと思います

React NativeとExpoについてはこちらを参照してください〜

> [【React Native】【Expo】Hello Worldしてみた]({{ '/react-native-expo-hello-world' | relative_url }})

## 目次

- [チュートリアル](#チュートリアル)
- [まとめ](#まとめ)

## チュートリアル

**expo-sqliteをインストール**

```bash
expo install expo-sqlite
```

**こんな感じでSQLiteを操作できる**

```javascript
/**
 * SQLiteと接続
 */
const db = SQLite.openDatabase("db");

/**
 * SQLを実行する
 */
db.transaction((tx) => {
  tx.executeSql(
    `〜〜実行したいSQL文〜〜`,
    [`〜〜SQL文の引数〜〜`],
    () => {
      // 成功時のコールバック関数;
    },
    () => {
      // 失敗時のコールバック関数
    }
  );
});
```

**具体的なサンプルコード**

```javascript
import * as SQLite from "expo-sqlite";

/**
 * SQLiteと接続
 */
const db = SQLite.openDatabase("db");
createTable(); // 「sample_table」というテーブル作成
insert(1, "Taro"); // 「id=1, name=Taro」のデータを登録
insert(2, "Jiro"); // 「id=2, name=Jiro」のデータを登録
select(); // データを取得してログに表示

/**
 * テーブルを作成する
 */
function createTable() {
  db.transaction((tx) => {
    tx.executeSql(
      // 実行したいSQL文
      `create table if not exists sample_table (id integer primary key not null, name text);`,
      // SQL文の引数
      // 必要ないときは空のまま
      [],
      // 成功時のコールバック関数
      () => {
        console.log("create table success");
      },
      () => {
        // 失敗時のコールバック関数
        console.log("create table faile");
        return false;
      }
    );
  });
}

/**
 * データを登録する
 */
function insert(id, name) {
  db.transaction((tx) => {
    tx.executeSql(
      // 実行したいSQL文
      // ?のところに引数で設定した値が順番に入る
      `insert into sample_table values (?, ?);`,
      // SQL文の引数
      [id, name],
      // 成功時のコールバック関数
      () => {
        console.log("insert success");
      },
      () => {
        // 失敗時のコールバック関数
        console.log("insert faile");
        return false;
      }
    );
  });
}

/**
 * データを取得する
 */
function select() {
  db.transaction((tx) => {
    tx.executeSql(
      // 実行したいSQL文
      `select * from sample_table;`,
      // SQL文の引数
      [],
      // 成功時のコールバック関数
      (_, { rows }) => {
        console.log("select success");
        console.log("select result:" + JSON.stringify(rows._array));
      },
      () => {
        // 失敗時のコールバック関数
        console.log("select faile");
        return false;
      }
    );
  });
}
```

**実行結果**

```bash
create table success
insert success
insert success
select success
select result:[{"id":1,"name":"Taro"},{"id":2,"name":"Jiro"}]
```

## まとめ

簡単に端末内にデータを保存することができました！