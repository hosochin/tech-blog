---
layout: post
title: 【GitHub】【Octokit】コード検索の結果をリポジトリ名一覧で取得する
date: 2022-08-29
tags: [github, octokit, JavaScript, nodeJs]
---

# はじめに

お世話になります、hosochinです  
今回はGitHubの検索APIを使って、  
「 **キーワードにヒットしたリポジトリの一覧を取得する方法** 」  
について紹介しようと思います  
ちなみにこちらの記事でcurlとjqを使った簡易的な方法を紹介しています

> [【GitHub】【curl】コード検索の結果をリポジトリ名一覧で取得する]({{ '/github-curl-search-api' | relative_url }})

が、これだとキーワードにヒットした箇所の101件目以降が取得できないという問題があり、今回はそこらへんを解決する内容になります

# 目次

- [Octokitを使ってみる](#octokitを使ってみる)
  - [前提](#前提)
  - [準備](#準備)
  - [実装](#実装)

# Octokitを使ってみる

[Octokit](https://docs.github.com/ja/rest/overview/libraries) は、GitHubAPIを使用するためのクライアントライブラリをいくつかの言語で提供してくれています  
今回はその中のJavascript用の[octokit/rest](https://github.com/octokit/rest.js) を使った方法を紹介します  
検索APIのドキュメントは[こちら](https://octokit.github.io/rest.js/v18#search)

## 前提

nodejsの開発環境が整っていること

## 準備

プロジェクトを作成していきます

```bash
$ mkdir github-tool
$ cd github-tool

$ npm init

# @octokit/restをインストールする
$ npm install @octokit/rest --save
```

## 実装

### main.js

```javascript
import { Octokit } from "@octokit/rest";

// インスタンス生成
const octokit = new Octokit({
  // gheの場合はbaseUrlを設定する
  // baseUrl: "https://api.ghe.xxxx",
  auth: "Access Token",
});

// 検索処理
function searchCode(keyword, page) {
  return new Promise(function (resolve) {

    const org = "検索対象のorganization";

    octokit
      .request("GET /search/code?q=" + keyword + "+org%3A" + org, {
        per_page: 100,     // 1ページごとのレスポンスの件数
        page: page,        // ページ
      })
      .then((response) => {
        resolve(response);
      })
      .catch((err) => console.error(err));
  });
}
```

### searchCodeを呼ぶ処理

GitHubAPIのレスポンスの「next」という項目がなくなるまでループを回す

```javascript
var page = 1;
var results = [];

// 無限ループ防止
while (page < 100) {
  var result = await searchCode("検索キーワード", page);

  if (!result.headers.link || !result.headers.link.match("next")) {
    // nextが尽きたらbreakする
    results.push(result);
    break;
  }

  results.push(result);
  page++;
}

var repositories = [];
results.map((result) =>
  result.data.items.map((item) => repositories.push(item.repository.full_name))
);
// 重複を削除してから出力
console.log([...new Set(repositories)]);
```

### 実行結果

```bash
$ node main.js

[
  '検索対象のorganization/リポジトリA',
  '検索対象のorganization/リポジトリB',
  '検索対象のorganization/リポジトリC',
]
```

いい感じに取得できました👍
