---
layout: post
title: 【Google Maps API】webブラウザに地図を表示してみる
date: 2020-12-13
tags: [google-maps-api, javascript, web-api, frontend, gcp]
---

## はじめに

お世話になります、hosochinです

今回は
**「Google Maps APIを使ってブラウザに地図を表示」**
を試してみたいと思います

## 目次

- [やってみる](#やってみる)

## やってみる

前提としてgoogleアカウント作成済みです👍

ログイン状態から[google Cloud Platform](https://console.cloud.google.com/getting-started?hl=ja)にアクセスします

[ライブラリ検索画面](https://console.cloud.google.com/apis/library?hl=ja)から「Maps JavaScript API」を検索、有効にするボタンをクリック

![Google Cloud Platform Maps JavaScript API有効化](/assets/google-maps-api-enable.png)

Google Maps PlatformからMaps JavaScript APIが有効になっていることを確認します

![Maps JavaScript API有効確認](/assets/google-maps-api-enabled.png)

次にAPI Keyを作成します

「APIとサービス」から「認証情報」を選択

![API認証情報画面](/assets/google-api-credentials.png)

「認証情報を作成」からAPIキーを選択、「APIキーを作成しました」と出るので右下の「キーを制限」を選択します

今回は「APIの制限」からMaps JavaScript APIを選択し保存をクリック

![APIキー制限設定](/assets/google-api-key-restrict.png)

ここまでで一旦APIにリクエストできるようになりますが、請求先情報を設定しないと正しくマップが表示されなので請求先情報を設定していきます

左メニューから「お支払い」をクリックし、「請求先アカウントを管理」を選択

![Google Cloud請求設定](/assets/google-cloud-billing.png)

「請求先アカウントを追加」をクリックし、住所とかクレジット情報とか入力していきます

![請求先アカウント追加](/assets/google-billing-account.png)

追加し終えたらマイプロジェクトというタブに切り替えて「アクション」>「お支払い情報を変更」>先ほど作成した請求先アカウントを紐づければ完了です😀

さあこれで準備はだいたい整いました

[Maps JavaScript APIの概要](https://developers.google.com/maps/documentation/javascript/overview)を参考にwebブラウザで表示してみます

sample.htmlのYOUR_API_KEYを作成したAPIキーに置き換えるだけで他は全部コピペでOKです

![Google Maps API表示成功](/assets/google-maps-display-success.png)

Maps JavaScript APIのレスポンスを表示できました！

ちなみに…請求先情報を設定しないとこんな感じになります😅

![Google Maps API表示失敗](/assets/google-maps-display-failed.png)