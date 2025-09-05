---
layout: post
title: 【Firebase】StorageをAndroidアプリで使ってみた
date: 2021-07-24
tags: [firebase, storage, android, authentication, mbaas, google]
---

## はじめに

お世話になります、hosochinです

今回は、  
**「FirabaseのStorageを使ってみた」**  
です

チュートリアル見ながら進めたはずなんですが、いくつか躓いたのでそこら辺を中心にまとめようと思います

## 目次

- [Firebase、Storageについて](#firebasestorageについて)
- [公式手順に従ってやってみる](#公式手順に従ってやってみる)
- [よし、アプリ起動して接続するぜ...ん？](#よしアプリ起動して接続するぜん)
- [まとめ](#まとめ)

## Firebase、Storageについて

**「[Firebase](https://firebase.google.com/docs)」**はGoogleが提供しているmBaaS(mobile backend as aService)です

モバイル・Webアプリケーションを構築するにあたって便利な機能が揃ってます👍

今回はその中でも**「[Storage](https://firebase.google.com/docs/storage?authuser=0)」**という機能を使ってみようと思います

StorageはAWSでいうところのS3みたいなやつで、写真や動画などを保管・提供可能なオブジェクトストレージサービスになってます

## 公式手順に従ってやってみる

さっそくチュートリアルに従って進めようと思います

今回はAndroidアプリで試してみました

前提としてGoogleアカウントは作成済みとします

「[Android プロジェクトに Firebase を追加する](https://firebase.google.com/docs/android/setup?authuser=0)」の通りに進めていきます

オプション１とオプション２でやり方が２通り書かれてますが、オプション１が推奨ってあるんでオプション１でやっていきましょう

[クイックスタートサンプル](https://github.com/firebase/quickstart-android/blob/master/storage/README.md)があるんでこいつを使ってStorageに接続したいと思います

## よし、アプリ起動して接続するぜ...ん？

手順に従って設定し終えたんで、クイックスタートサンプルのAppを起動します

が、ログインボタンを押すとエラーになりました

```
API key not valid. Please pass a valid API key
```

ふむ・・・APIキーがないって怒られる

たしかにプロジェクトの設定の全般にあるウェブAPIキーってのが空白になってる

ここではAuthenticationを設定しないとダメなようです

Authenticationを選択して、始めるをクリックしましょう

で、再度ログインボタン押します

```
E/Storage#MainActivity: signInAnonymously:FAILURE
    com.google.firebase.auth.FirebaseAuthException: This operation is restricted to administrators only.
```

むう…権限で怒られました

Sign-in methodの設定を有効にしてやる必要があります

今回はとりあえず接続確認が取れればいいんで匿名のところを有効にしちゃいましょう

再度エミュレータからログインボタン押します

```
D/Storage#MainActivity: signInAnonymously:SUCCESS
```

これで接続が確認できました！

アップロードボタン押して画像がアップロードできることも確認できましたー

## まとめ

公式手順に従ってすすめましたが、Authenticationの設定もしないとログインできないってのにちょっと手こずってしまいました😅
