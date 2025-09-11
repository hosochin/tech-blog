---
layout: post
title: 【React Native】【Expo】Hello Worldしてみた
date: 2020-04-15
tags: [React Native, Expo, JavaScript]
---

# はじめに

お世話になります、hosochinです  

さて、今回は  
**Expoを使ってReact NativeでHello Worldしてみる**  
です  

前回の「React Native】AndroidアプリでHello Worldしてみた」をやってみて、よしそんじゃあなんか簡単なアプリ作って動かしてみるか〜ってなったのですが、何やら「**Expo**」なる便利なフレームワークがあるそうで・・・

ということで早速Expo使ってみたいと思います

# 目次

- [Expoとは](#expoとは)
- [チュートリアル](#チュートリアル)
  - [1. Expoインストール](#1-expoインストール)
  - [2. プロジェクト作成](#2-プロジェクト作成)
  - [3. 動かしてみる](#3-動かしてみる)
  - [4. Hello Worldしてみる](#4-hello-worldしてみる)
- [まとめ](#まとめ)

# Expoとは

React-Native使ってモバイル開発するぞーってなっても結局ネイティブの部分の知識が必要になったり、iOS/Androidのビルドを自前でやる必要があるのでビルド自体が重かったり、ビルド周りでハマったり・・・

そんな問題を解決してくれるのが **Expo** （らしい）です  

Expoは実装済みのネイティブコードを提供してくれるツールです  
そのため自前でiOS/Androidのネイティブコードを管理する必要がなくなります  
主に次のようなメリットがあります

- JavaScriptの実装のみでアプリを作れる
- 自前でiOS/Androidのビルドが必要なくなる
- ビルドが必要ないのでアプリの審査を待たずにリリース可能

# チュートリアル

以下の流れを[公式](https://docs.expo.io/versions/v37.0.0/get-started/installation/)見ながらやっていこうと思います

1. Expoインストール
2. プロジェクト作成
3. 動かしてみる
4. Hello Worldしてみる

## 1. Expoインストール

```bash
$ npm install --global expo-cli

$ expo --version
3.18.0
```

## 2. プロジェクト作成

```bash
$ expo init tutorial-expo
// テンプレート何使う？って聞かれる
// とりあえずblankを選択
? Choose a template: (Use arrow keys)
  ----- Managed workflow -----
❯ blank                 a minimal app as clean as an empty canvas 
  blank (TypeScript)    same as blank but with TypeScript configuration 
  tabs                  several example screens and tabs using react-navigation 
  ----- Bare workflow -----
  minimal               bare and minimal, just the essentials to get you started 
  minimal (TypeScript)  same as minimal but with TypeScript configuration
```

**作成された中身はこんな感じになっています**

```
App.js
app.json
assets
babel.config.js
node_modules
package-lock.json
package.json
```

React-Nativeに比べてandroidとiosのフォルダがないですね  
これはExpoがネイティブコードの責任を持ってくれるからです

## 3. 動かしてみる

専用のアプリをインストールする必要がありますが、実機でやるのがお手軽だと思います  
- AndroidならGooglePlayから「Expo」をインストール  
- iOSならApp Storeから「Expo Client」をインストール  

もちろんエミュレータでもできます  
ただ若干エミュレータの方がややこしい気がしますが・・・  
うまくいかない時は以下を確認するといいかもです  
- アプリの起動前にエミュレータが起動していること  
- adbのパスが通っていること

**アプリを起動させる**

```bash
npm start
```

するとこんな感じでブラウザに表示されると思います

![Expo開発サーバー](/assets/expo-dev-server.png)

今回は実機でやろうと思うので、Android端末にインストールした専用アプリからQRコードを読み取ります

**実行結果のイメージ（画像はエミュレータ）**

![Expo初期画面](/assets/expo-initial-screen.png)

サーバは起動したままにして次の「Hello Worldしてみる」をやっていきたいと思います

## 4. Hello Worldしてみる

プロジェクト直下の `App.js` を編集します  
Textタグの中の「Open up App.js to start working on your app!」を「Hello World」に変えてみます  
保存するとすぐに反映されます

**App.js**

```javascript
...中略...
export default function App() {
  return (
    <View style={styles.container}>
      <Text>Hello World</Text>
    </View>
  );
}
...中略...
```

**実行結果のイメージ（画像はエミュレータ）**

![Expo Hello World](/assets/expo-hello-world.png)

はやい・・・速攻反映されます、普通におおーーってなりました

# まとめ

React-Nativeで簡単なモバイルアプリ開発するときはExpo使うのが良さそうって話でした  
とりあえずサクサク反映されるのはいいですねー
