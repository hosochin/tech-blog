---
layout: post
title: 【GitHub】【curl】コード検索の結果をリポジトリ名一覧で取得する
date: 2022-08-16
tags: [github, curl]
---

# はじめに

お世話になります、hosochinです  
GitHub(GUI)の検索って少々使いにくくないですか？てことで今回は **「GitHubのRest API」** を使ってみようと思います  
たとえばキーワードに一致したリポジトリ名の一覧の取得…みたいなのをAPI使って実現しようと思います👍

# 目次

- [GitHubのSearch APIについて](#githubのsearch-apiについて)
- [検索APIのエントリポイント一覧](#検索apiのエントリポイント一覧)
- [特定Organization内のコードを検索する](#特定organization内のコードを検索する)
- [検索キーワードに一致したリポジトリ一覧を取得する](#検索キーワードに一致したリポジトリ一覧を取得する)
- [ただこのままだと…](#ただこのままだと)

# GitHubのSearch APIについて

公式ドキュメントはこちら  
<https://docs.github.com/ja/rest/search>

# 検索APIのエントリポイント一覧

| エントリポイント | 検索内容 |
|---|---|
| /search/code | デフォルトブランチで様々な条件でコードを検索する |
| /search/commits | デフォルトブランチで様々な条件でコミットを検索する |
| /search/issues | 状態とキーワードでissueを検索する |
| /search/labels | 検索キーワードに一致する名前または説明を含むラベルをリポジトリで検索する |
| /search/repositories | 様々な基準でリポジトリを検索する |
| /search/topics | 様々な基準でトピックを検索する |
| /search/users | 様々な基準でユーザを検索する |

# 特定Organization内のコードを検索する

サンプルリクエスト

```bash
curl -H "Authorization: token <TOKEN>" \
-H 'Accept: application/vnd.github.text-match+json' \
'https://api.github.com/search/code?q=<検索キーワード>+org:<特定のorganization>'
```

使用可能なラベルについては以下を参照  
<https://docs.github.com/ja/search-github/searching-on-github/searching-code>

text-matchを指定することで検索結果の詳細を取得することができます

# 検索キーワードに一致したリポジトリ一覧を取得する

jqコマンドを使って結果をいい感じに整形したいと思います

## jqコマンドをインストール

公式ドキュメントは[こちら](https://stedolan.github.io/jq/manual/#containselement)

```bash
brew install jq
```

## リポジトリ一覧を取得する

```bash
curl -H "Authorization: token <TOKEN>" \
-H 'Accept: application/vnd.github.text-match+json' \
'https://api.github.com/search/code?q=<検索キーワード>+org:<特定のorganization>' \
| jq '[.items[].repository.name] | unique'
```

```json
[
  "repositoryA",
  "repositoryB",
  "repositoryC"
]
```

# ただこのままだと…

API側の制限で最大100件までしかレスポンス返してくれないんです泣  
101件目以降はpage=2みたいにリクエストする必要があり、次ページが尽きるまでリクエストし続ける必要が。。  
こちらについてはまた今度書きたいなと思ってます😎
