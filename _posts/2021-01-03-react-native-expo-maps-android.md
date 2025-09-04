---
layout: post
title: 【React Native】【Expo】Androidアプリでマップを表示してみる
date: 2021-01-03
tags: [react-native, expo, android, google-maps, typescript, mobile-app]
---

## はじめに

お世話になります、hosochinです

今回は
**「React Native+Expoでマップを表示」**
を試してみたいと思います

なお、取り扱うのはAndroidアプリについてです

## 目次

- [やってみる](#やってみる)
  - [1. Expoインストール](#1-expoインストール)
  - [2. プロジェクト作成](#2-プロジェクト作成)
  - [3. Maps SDK for Androidを有効にする](#3-maps-sdk-for-androidを有効にする)
  - [4. APIキーをapp.jsに追加](#4-apiキーをappjsに追加)
  - [5. 動かしてみる](#5-動かしてみる)

## やってみる

[react-native-maps](https://github.com/react-native-maps/react-native-maps)というiOS,Androidアプリでマップを表示するコンポーネントがあるのでそいつを使っていきます！

Androidの場合はGoogle Mapを表示してくれる感じですねー

なので今回はGoogle Cloud PlatformでマップAPIも使っていきます！

基本的な流れは[こちら](https://docs.expo.io/versions/latest/sdk/map-view/)に沿ってやっていきたいと思います

ちなみに、React NativeとExpoについてはこちらの記事で軽く触れました

> [【React Native】【Expo】Hello Worldしてみた]({{ '/react-native-expo-hello-world/' | relative_url }})

### 1. Expoインストール

```bash
-- インストール
$ npm install --global expo-cli

-- バージョン確認
$ expo --version
3.18.0
```

### 2. プロジェクト作成

```bash
-- プロジェクト作成、今回はテンプレートはblank (TypeScript)を選択
$ expo init tutorial-react-native-maps

-- react-native-mapsをインストール
$ cd tutorial-react-native-maps
$ expo install react-native-maps
```

### 3. Maps SDK for Androidを有効にする

Google Cloud Platformから「Maps SDK for Android」を有効にします

やり方は[【Google Maps API】webブラウザに地図を表示してみる]({{ '/google-maps-api-web-display/' | relative_url }})を参考にしてみてください

上記の記事だとwebブラウザに表示しようとしているので「Maps JavaScript API」を使っていますが、「Maps SDK for Android」に置き換わる感じです

### 4. APIキーをapp.jsに追加

3で作ったAPIキーをapp.jsに追加します

App.tsxも変更します

**tutorial-react-native-maps/app.js**

```json
...省略...
    },
    "android": {
      "config": {
        "googleSignIn": {
          "apiKey": "<APIキー>"
        },
        "googleMaps": {
          "apiKey": "<APIキー>"
        }
      },
...省略...
```

**tutorial-react-native-maps/App.tsx**

```typescript
// https://docs.expo.io/versions/latest/sdk/map-view/
// ↑のページ内のコードをコピペ
import * as React from "react";
import MapView from "react-native-maps";
import { StyleSheet, Text, View, Dimensions } from "react-native";

export default function App() {
  return (
    <View style={styles.container}>
      <MapView style={styles.map} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center",
  },
  map: {
    width: Dimensions.get("window").width,
    height: Dimensions.get("window").height,
  },
});
```

### 5. 動かしてみる

以下のコマンドをtutorial-react-native-maps配下で実行してやると、ブラウザが立ち上がってQRコードが表示されると思います

```bash
$ expo start
```

エミュレータ使うより実機使う方が手っ取り早い気がしてるので自分はいつも実機で動かしてます

ただ専用のアプリをインストールする必要がありますが

・AndroidならGooglePlayから「Expo」をインストール
・iOSならApp Storeから「Expo Client」をインストール

↑のアプリ内からQRコードを読み取ってやるとアプリが起動します

これで無事地図が表示されたかと思います！

![React Native Expo Maps表示結果](/assets/react-native-expo-maps-android.png)