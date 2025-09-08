---
layout: post
title: 【Cloud Firestore】取得したデータをリターンする
date: 2021-10-07
tags: [cloud-firestore, firebase, android, java, async, callback]
---

## はじめに

お世話になります、hosochinです

今回は  
**「Cloud Firestoreで取得したデータを扱う」**  
です

## 目次

- [Cloud Firestoreとは](#cloud-firestoreとは)
- [取得したデータを表示してみたい](#取得したデータを表示してみたい)
  - [原因](#原因)
  - [解決方法](#解決方法)
- [まとめ](#まとめ)

## Cloud Firestoreとは

こちらの記事を参照してください

> [【Cloud Firestore】取得したデータをオブジェクトにマッピングする]({{ '/cloud-firestore-object-mapping' | relative_url }})

データの取得部分の実装サンプルも載せてあります

今回は上記の記事の続きになります

## 取得したデータを表示してみたい

データ取得部分の実装はこんな感じです

* データ取得メソッドの呼び元

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    findUser();
}
```

* データ取得部分

```java
public void findUser() {
    FirebaseFirestore db = FirebaseFirestore.getInstance();

    // usersコレクションのuser1というドキュメントを取得
    // 中身はidとnameっていうフィールドを定義してるけど今回はあんま関係ない
    // Userクラスにidとnameっていうフィールド持たせてそこにマッピングしてる今回はあんま関係ない
    // toStringはいい感じに出力できるようにオーバーライドしてる今回はあんま関係ない
    db.collection("users").document("user1")
            .get()
            .addOnCompleteListener(task -> {
                User user = task.getResult().toObject(User.class);
                Log.d(TAG, user.toString());
            });
}
```

よしログに出してデータ取得できてることは確認できたな！

じゃあ呼び元でfindUser()からデータ受け取ってゴニョゴニョしますか〜

・・・

….あれ、無理だなこれってなります😇

### 原因

[get()](https://firebase.google.com/docs/reference/android/com/google/firebase/firestore/DocumentReference?hl=ja#get())は非同期処理だからですね、戻り値の型はTask<QuerySnapshot>になってます

なのでパッと以下のような方法が思いつきましたが、IllegalStateExceptionが発生します

```java
public User findUser() {
    FirebaseFirestore db = FirebaseFirestore.getInstance();

    // Task is not yet completeって怒られる
    return db.collection("users")
            .document("user1")
            .get()
            .getResult().toObject(User.class);
}
```

### 解決方法

解決方法としては、

1. コールバック関数を仕込む
2. 非同期処理を待機する

の2通りが良さそうでした

今回はコールバックさせる方法について紹介します

androidアプリだったら画面に表示させるだけならこれで済みそうですし、大抵は1のやり方で済むんじゃないでしょうか😎

ではコールバックを利用して、MainActivityのtextViewに取得結果を表示してみたいと思います

* コールバックのインターフェース

```java
public interface UserCallback {
    void onGetUser(Context context, User user);
}
```

* データ取得部分

```java
public void findUser(Context context, UserCallback callback) {
    FirebaseFirestore db = FirebaseFirestore.getInstance();
    db.collection("users").document("user1")
            .get()
            .addOnCompleteListener(task -> {
                callback.onGetUser(context, task.getResult().toObject(User.class));
            });
}
```

* データ取得メソッドの呼び元

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    findUser(this, new UserCallback() {
        @Override
        public void onGetUser(Context context, User user) {
            TextView textView = ((MainActivity) context).findViewById(R.id.text_view);
            textView.setText(user.toString());
        }
    });

    // ラムダ式で書く場合
    findUser(this, (context, user) -> {
        TextView textView = ((MainActivity) context).findViewById(R.id.text_view);
        textView.setText(user.toString());
    });
}
```

いかがでしょうか、R.id.text_viewに取得結果が表示されたと思います👍

## まとめ

この辺参考にさせていただきました、リンク貼っておきます

・[How to read data from Firebase Realtime Database using get()?](https://medium.com/firebase-tips-tricks/how-to-read-data-from-firebase-realtime-database-using-get-269ef3e179c5)

・[teratail Firestore 取得後の処理について](https://teratail.com/questions/230349)

次は非同期処理を待機する方法も記事にしたいなーって思ってます🍺