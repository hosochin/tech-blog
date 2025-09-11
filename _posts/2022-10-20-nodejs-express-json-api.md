---
layout: post
title: 【Node.js】【Express】 jsonを返却するシンプルなREST APIの作成
date: 2022-10-20
tags: [nodeJs, Express, json, JavaScript]
---

# はじめに

お世話になります、hosochinです  
今回は  
**「Expressを使って、jsonデータを返却するREST APIを作る」**  
です

# 目次

- [前提](#前提)
- [実装](#実装)
  - [express-generatorのインストール](#express-generatorのインストール)
  - [プロジェクトの作成](#プロジェクトの作成)
  - [jsonを返却する](#jsonを返却する)
  - [おまけ](#おまけ)

# 前提

node.jsの実行環境  
Expressとは何か？の説明は省略

# 実装

## express-generatorのインストール

express-generatorはExpressの雛形を生成してくれるツールです

```bash
npm install express-generator -g
```

## プロジェクトの作成

```bash
# expressの雛形を作成
express myapp

# とりあえず起動してみる
cd myapp
npm install
npm start
```

http://localhost:3000をブラウザで開くと以下のようになれば成功です

<img src="{{ '/assets/images/2022_10_20_1.png' | relative_url }}" alt="Express Welcome画面" class="img-large">

## jsonを返却する

express-generatorで作成した雛形にroutes/users.jsというファイルがあると思うので、一部書き換えます

```javascript
var express = require("express");
var router = express.Router();

/* GET users listing. */
router.get("/", function (req, res, next) {
  // res.send('respond with a resource'); コメントアウト
  res.json({ value: "test" });  // 追加
});

module.exports = router;
```

再度npm start をしてみて、/usersにリクエストすればjsonデータが返却されます👍

```bash
# curlしてみる
curl localhost:3000/users
{"value":"test"}

# ヘッダーを確認
curl http://localhost:3000/users -I
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 18
Date: Thu, 20 Oct 2022 04:35:13 GMT
Connection: keep-alive
Keep-Alive: timeout=5
```

## おまけ

postの場合

```javascript
var express = require("express");
var router = express.Router();

router.post("/", function (req, res, next) {
  console.log(req.body);
  res.send("Received POST Data!");
});

module.exports = router;
```
