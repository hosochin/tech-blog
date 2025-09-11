---
layout: post
title: 【Cloud Firestore】取得したデータをオブジェクトにマッピングする
date: 2021-10-03
tags: [Cloud Firestore, firebase]
---

# はじめに

お世話になってます、hosochinです

今回は  
**「Cloud Firestore でオブジェクトとしてデータを取得する」**  
です

# 目次

- [Cloud Firestoreについて](#cloud-firestoreについて)
- [Cloud Firestoreからデータを取得する](#cloud-firestoreからデータを取得する)

# Cloud Firestoreについて

[Cloud Firestore](https://firebase.google.com/docs/firestore?hl=ja) はGoogleのFirebaseで提供されてるNoSQLのデータベースです

リアルタイムリスナーを介してクライアントアプリ間でデータを同期してくれてるので、更新があった場合にリアルタイムで反映してくれます

そこそこ無料で使えるのもいいですね👍

# Cloud Firestoreからデータを取得する

Cloud Firestoreには、「document」と「collection」という概念が存在します

document = ファイル、collection = ディレクトリってイメージです

今回は以下のような構成とします

さっそくこのuser1とuser2の中身を取得したいと思います

[ここらへん](https://firebase.google.com/docs/firestore/query-data/get-data?hl=ja)参考にやってきます

```java
public void findUser() {
    FirebaseFirestore db = FirebaseFirestore.getInstance();
    db.collection("users")
            .get()
            .addOnCompleteListener(task -> {
                for (QueryDocumentSnapshot document : task.getResult()) {
                    Log.d(TAG, document.getId() + " => " + document.getData());
                }
            });
}
```

* 実行結果

```bash
user1 => {name=taro, id=0001}
user2 => {name=jiro, id=002}
```

いい感じに取得できましたね

ただ、idとnameというフィールドを持ったUserクラスで取得できたらもっと便利ですよね

次は今回のタイトルにある通りオブジェクトにマッピングして取得したいと思います

* User.javaを作ります

```java
public class User {
    private String id;
    private String name;

    public User() {
    }

    public User(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "id=" + this.id + ", name=" +name;
    }
}
```

* データ取得部分

```java
public void findUser() {
    FirebaseFirestore db = FirebaseFirestore.getInstance();
    db.collection("users")
            .get()
            .addOnCompleteListener(task -> {
                List<User> users = new ArrayList<>();
                for (QueryDocumentSnapshot document : task.getResult()) {
                    users.add(document.toObject(User.class));
                }
                Log.d(TAG, users.get(0).toString());
                Log.d(TAG, users.get(1).toString());
            });
}
```

* 実行結果

```bash
id=0001, name=taro
id=002, name=jiro
```

期待通りの結果になりました🍺
