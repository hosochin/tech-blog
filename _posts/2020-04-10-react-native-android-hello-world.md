---
layout: post
title: 【React Native】AndroidアプリでHello Worldしてみた
date: 2020-04-10
tags: [react-native, android, javascript, mobile-app]
---

# はじめに

お世話になります、hosochinです  

さて、今回は  
**React NativeでHello World**  
してみた話です  

と言いつつ環境構築でめちゃめちゃハマりました！  
それでは紹介していきます

# 目次

- [環境構築](#環境構築)
- [チュートリアル](#チュートリアル)
  - [Androidのエミュレータを起動](#androidのエミュレータを起動)
- [Hello Worldやってみる](#hello-worldやってみる)
- [まとめ](#まとめ)

# 環境構築

[公式ドキュメント](https://reactnative.dev/docs/environment-setup)で丁寧に説明されていたのでこれ見ながらやりました（OSはwindows）  
ざっくり言うと、

- AndroidStudioのインストール
- npmのインストール
- react-native-cliをインストール

です

# チュートリアル

プロジェクト作成→動作確認までやっていこうと思います

**React Nativeのプロジェクトをセットアップします**

```bash
react-native init AwesomeProject
```

**作成したプロジェクトを実行します**

```bash
react-native run-android
```

**エラーになりました・・・、以下のようなエラーが出てきました**

```
Execution failed for task ':app:installDebug'.
> com.android.builder.testing.api.DeviceException: No connected devices!
```

ちょっと調べてみたら、どうやら `react-native run-android` を実行するまえにAndroidのエミュレータを起動しておかなければいけないっぽいです（iOSは大丈夫らしい）

## Androidのエミュレータを起動

エミュレータの起動ちょっと分かりづらいです  
まずAndroidStudioを起動します  
このとき開いているプロジェクトは何でもいいです

**赤枠のボタンをクリックする**

![Android エミュレータ起動](/assets/android-emulator-button.png)

**Create Virtual Device…から任意のデバイスを選択し、Actionの実行ボタンをクリックでエミュレータが起動する**

![Android エミュレータ設定](/assets/android-emulator-setup.png)

エミュレータを起動した状態で `react-native run-android` を実行！

**またまたエラー、、、、**

```bash
'adb' は、内部コマンドまたは外部コマンド、
操作可能なプログラムまたはバッチ ファイルとして認識されていません。
```

げふん、 SDK Platform-Toolsをインストールしてパスを通さないといけないようです  
[ここから](https://developer.android.com/studio/releases/platform-tools) ダウンロードします

ポチっとなー

![SDK Platform Tools ダウンロード](/assets/platform-tools-download.png)

・・・・反応しない、、、一通り他のブラウザでも試してみたけど反応しない・・・  
ちょっと調べてみたら、右上の言語設定を英語にするとできるらしい。。

![言語設定変更](/assets/language-setting-change.png)

おお、、、本当だ進めた  
あとはダウンロードしたzip展開してパス通しておきます

**再挑戦！**

```bash
react-native run-android
```

![React Native 成功画面](/assets/react-native-success.png)

やっとできたぁ。。上のように表示されればOKです  
次にコードの一部を修正して「Hello World」を表示しようと思います

# Hello Worldやってみる

**App.jsを以下のように修正**

```javascript
import React, {Component} from 'react';
import {StyleSheet, Text, View} from 'react-native';

const App = () => (
  <View style={styles.container}>
    <Text>Hello World</Text>
  </View>
);

const styles = StyleSheet.create({
  container: {
    fontWeight: 'bold',
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default App;
```

**実行結果**

![Hello World 成功](/assets/hello-world-result.png)

Hello Worldできました～

# まとめ

さくっと出来ると思ってたんですが無駄にハマった・・・  
iOSだともっとすんなり行くんですかね？  
次は簡単なアプリ作ってみたいと思います
