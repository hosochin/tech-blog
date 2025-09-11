---
layout: post
title: 【Web Speech API（SpeechRecognition）】ブラウザで音声認識機能を実装する
date: 2023-01-11
tags: [web-speech-api, javascript, speech-recognition, browser-api, voice-recognition]
---

# はじめに

お世話になります、hosochinです  
今回はwebブラウザの音声認識機能を利用する方法についてまとめてみます

まず代表的な音声認識のAPIについてですが、

- Google Cloud Speech-to-Text
- Watson Speech to Text
- Bing Speech API
- Amazon Transcribe

らへんが主流のようでした  
が、こいつら基本有料でして、なるべく無料でやりたいなあと探していたら、 **「Web Speech API」** なるものを見つけました

# 目次

- [Web Speech APIとは](#web-speech-apiとは)
- [先に成果物](#先に成果物)
- [Hello World](#hello-world)
  - [実装](#実装)
  - [解説](#解説)
- [認識途中の結果も取得しながら、それっぽく表示してみる](#認識途中の結果も取得しながらそれっぽく表示してみる)
  - [実装](#実装-1)
  - [解説](#解説-1)
    - [プロパティ](#プロパティ)
    - [メソッド](#メソッド)
    - [イベント](#イベント)
- [参考](#参考)

# Web Speech APIとは

Web Speech APIとはwebブラウザで簡単に音声認識機能を実現できるAPIになります

<https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API>

こいつはブラウザの音声認識機能になりまして、対応ブラウザなら誰でも無料で利用可能です（音声合成機能も提供しています）  
早速こいつを使って音声認識機能を実装していきたいと思います  
対応ブラウザについては[こちら](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API#browser_compatibility)を参照

# 先に成果物

web speech apiを活用して、音声によるシンプルなTODOリストを作成するツールを作ってみました  
<https://voicetodolist.net/>  
よかったら見てやってくださいmm

# Hello World

まずは簡単なコードで動きをみてみたいと思います

## 実装

### index.html

```html
<script>
  speechRecognition = new webkitSpeechRecognition();
  speechRecognition.onresult = console.log;
  speechRecognition.start();
</script>
```

## 解説

web speech apiを使うためにwebkitSpeechRecognitionのインスタンスを作成してやります  
上記のhtmlをブラウザで開くとマイクの使用許可を求めるポップアップがでるはずです  
許可をクリックするとマイクがオンになるので、何か喋ってみてください  
そうするとコンソールログが出力されます

<img src="{{ '/assets/images/2023_01_10_1_1.png' | relative_url }}" alt="コンソール出力結果" class="img-large">

「テスト」と喋ってみたんですが、results[0][0].transcriptに'テスト'と出力されていますね！  
音声認識の解析結果がここに入ってくるわけです  
[transcript](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionAlternative/transcript)の詳細はこちら

# 認識途中の結果も取得しながら、それっぽく表示してみる

音声認識中のテキストを表示しながら、認識が完了した結果をリスト形式で表示してみたいと思います

## 実装

### index.html

```html
<div>
  <button id="start-btn">START</button>
  <button id="stop-btn">STOP</button>
</div>

<div>
  音声認識中のテキスト
  <p id="process"></p>
</div>
<div>
  認識結果
  <ol id="results"></ol>
</div>

<script>
  const startBtn = document.querySelector("#start-btn");
  const stopBtn = document.querySelector("#stop-btn");
  const process = document.querySelector("#process");
  const results = document.querySelector("#results");

  const recognition = new webkitSpeechRecognition();

  // 日本語を指定
  recognition.lang = "ja-JP";
  // 認識途中でも結果を得る
  recognition.interimResults = true;
  // 認識を終わらせない
  recognition.continuous = true;

  recognition.onresult = (event) => {
    let interimTranscript = ""; // 認識中のテキスト
    for (let i = event.resultIndex; i < event.results.length; i++) {
      let transcript = event.results[i][0].transcript;
      if (event.results[i].isFinal) {
        // 音声認識が完了した場合にisFinalがtrueになる
        var result = document.createElement("li");
        result.innerText = transcript;
        results.appendChild(result);
      } else {
        interimTranscript += transcript;
      }
    }
    process.innerHTML = interimTranscript;
  };

  startBtn.onclick = () => {
    recognition.start();
  };

  stopBtn.onclick = () => {
    recognition.stop();
  };
</script>
```

## 解説

startボタンを押して、マイクを許可し、テキトーに喋ってみました

<img src="{{ '/assets/images/2023_01_10_1_2.png' | relative_url }}" alt="音声認識結果画面" class="img-large">

画像なので分からないんですが、認識中のテキストも表示されるので、認識してくれてる感でてると思います笑

### プロパティ

設定したプロパティについて説明します

- [lang](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/lang)
  - 言語設定
  - 日本語を設定しています
- [interimResults](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/interimResults)
  - 中間結果を返すかどうかの設定（デフォルトはfalse）
  - これをtrueにしてやることで、認識中のテキストを表示する仕掛けに利用します
- [continuous](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/continuous)
  - 連続した結果を返すかどうかの設定（デフォルトはfalse）
  - これをtrueにしてやることで、単一の音声の認識結果が返ってきたあとも音声認識を継続できます

### メソッド

利用しているメソッドについて説明します

- [start()](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/start)
  - 音声認識を開始します
  - ブラウザからマイクを許可しますか？ってポップアップが出てくると思います
- [stop()](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/stop)
  - 音声認識を終了します

### イベント

利用したのはonresultのみです

- [onresult](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/result_event)
  - 音声認識の結果が返ってきたときに発生します
  - 今回はinterimResultsプロパティをtrueにしているので、音声認識途中でもonresultが呼び出されます
  - [isFinal](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionResult/isFinal)を見て、trueなら音声認識が完了、falseなら音声認識途中であることを判定しています

# 参考

<https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition>  
<https://qiita.com/hmmrjn/items/4b77a86030ed0071f548>  
<https://monomonotech.jp/kurage/iot/webspeechapi_voice_recognition.html>
