---
layout: post
title: 【GitHub】【Octokit】特定のOrganization下のリポジトリを一括でcloneする
date: 2022-09-05
tags: [github, octokit, javascript, nodejs, git, clone]
---

# はじめに

お世話になります、hosochinです  
今回は **「GitHubのリポジトリを一括でclone」** してみます  
GitHubのAPIを使って特定Organizationのリポジトリ一覧を取得後、git cloneするスクリプトを作ってみた感じです🍺

# 目次

- [Octokitを使ってみる](#octokitを使ってみる)
- [前提](#前提)
- [実装](#実装)
- [動かしてみる](#動かしてみる)

# Octokitを使ってみる

[Octokit](https://docs.github.com/ja/rest/overview/libraries) は、GitHubAPIを使用するためのクライアントライブラリをいくつかの言語で提供してくれています  
今回はその中のJavascript用の[octokit/rest](https://github.com/octokit/rest.js) を使った方法を紹介します

# 前提

nodejsの開発環境が整っていること

プロジェクトを作成していきます

```bash
$ mkdir github-tool
$ cd github-tool

$ npm init

# @octokit/restをインストールする
$ npm install @octokit/rest --save
# child_processをインストールする
$ npm install child_process --save
```

# 実装

## clone.js

```javascript
import { execSync } from "child_process";
import { Octokit } from "@octokit/rest";

const octokit = new Octokit({
  // baseUrl: "https://api.ghe.xxxx",
  auth: "Access Token",
});

octokit
  .paginate(octokit.repos.listForOrg, {
    org: "特定のOrganization",   // Organizationを指定
    sort: "updated",            // ソート順を更新日時順に
    per_page: 100,
  })
  .then((response) => {
    for (const data of response) {
      if (process.argv[2]) {
        if (
          data.language &&
          process.argv[2].toUpperCase() === data.language.toUpperCase()
        ) {
          // 言語でフィルターあり
          clone(data.name, data.ssh_url);
        }
      } else {
        // 言語でフィルターなし
        clone(data.name, data.ssh_url);
      }
    }
  })
  .catch((err) => console.error(err));

function clone(name, ssh_url) {
  var cmd = "git clone " + ssh_url + " " + "./tmp/" + name;

  console.log(name + "のクローンを開始");

  try {
    const result = execSync(cmd).toString();
    console.log(result);
    console.log(name + "のクローンを完了");
  } catch (err) {
    console.log(name + "のクローンを失敗");
    console.error(err);
  }
}
```

# 動かしてみる

```bash
# 言語フィルターなし
node clone.js

# 言語フィルターあり
node clone.js java
```