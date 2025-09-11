---
layout: post
title: 【bash】curlした結果をdiffコマンドで比較
date: 2022-06-18
tags: [bash, curl]
---

# はじめに

お世話にになります、hosochinです  

bashでコマンドの結果を直接比較する場合は以下でいけます

```bash
diff <(コマンドA) <(コマンドB)
```

なのでAPIのレスポンスのdiffをとりたい場合などは以下でいけます

```bash
# WEB-APIのレスポンスがjson形式の例
diff <(curl -X GET http://xxxxxx.com/A | jq) <(curl -X GET http://xxxxxx.com/B | jq)
```

# まとめ

bashのプロセス置換（Process Substitution）`<()` を使用することで、コマンドの実行結果を一時的にファイルのように扱い、diffコマンドで直接比較できます。APIの開発・テスト時に異なるエンドポイントのレスポンスを比較したい場合に便利です。
