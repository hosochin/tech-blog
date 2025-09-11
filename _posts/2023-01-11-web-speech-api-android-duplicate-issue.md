---
layout: post
title: 【Web Speech API（SpeechRecognition）】Androidで音声認識がダブる
date: 2023-01-11
tags: [web-speech-api, android, javascript, speech-recognition, browser-api]
---

# はじめに

お世話になります、hosochinです  
今回はwebブラウザで簡単に音声認識機能を利用できる **「Web Speech API」** について、Android端末でのみ特定の不具合が発生したのでその対処法についてメモします

# 目次

- [Web Speech APIとは](#web-speech-apiとは)
- [Android端末でのみ音声認識の結果が二重になる](#android端末でのみ音声認識の結果が二重になる)
  - [発生条件と不具合について](#発生条件と不具合について)
  - [解決方法](#解決方法)
- [参考](#参考)

# Web Speech APIとは

以下の記事で取り扱いましたので参照してください！

> [【Web Speech API（SpeechRecognition）】ブラウザで音声認識機能を実装する]({{ '/web-speech-api-browser-voice-recognition' | relative_url }})

# Android端末でのみ音声認識の結果が二重になる

例えば「テスト」とマイクに向かって喋った場合に、[onresult](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/result_event)が2回呼ばれ、2回とも「テスト」という結果が返ってくるという事象にぶつかりました

## 発生条件と不具合について

### 発生条件

- Android端末のwebブラウザを使用する（chromeでもsafaliでも同じように再現した）
- SpeechRecognitionの[interimResults](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/interimResults)というプロパティをtrueに設定
  - 中間結果を返すかどうかの設定
  - 認識途中の音声もユーザビリティ的な観点から画面に表示したかったため

### 発生する不具合

- 1回の音声入力につき、[onresult](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/result_event)が2回呼ばれる
  - 2回とも同じ[transcript](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionAlternative/transcript)が返ってくる（結果がダブる）
  - [isFinal](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionResult/isFinal)=trueで返ってくる
- 音声認識途中の結果が得られない
  - [isFinal](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionResult/isFinal)=falseのレスポンスが一度も返ってこない

## 解決方法

直接的な解決方法は見つかりませんでしたが、[interimResults](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/interimResults)をfalseに設定することで不具合が発生しなくなりました  
ただこれだとAndroid端末では音声認識途中の結果は受け取れないってことにはなっちゃいますが。。解決方法が分かる方いらっしゃったら教えていただきたいです…！

```javascript
const recognition = new webkitSpeechRecognition();

// Androidの場合は音声認識に不具合が発生するためinterimResultsをfalseに設定する
var userAgent = window.navigator.userAgent.toLowerCase();
recognition.interimResults = userAgent.match("android") ? false : true;
```

# 参考

<https://stackoverflow.com/questions/53001942/speech-recognition-onresult-called-twice-on-mobile-and-tablet-deveices>  
<https://stackoverflow.com/questions/39340422/android-webkitspeechrecognition-isfinal-variable-not-showing-correct-value>