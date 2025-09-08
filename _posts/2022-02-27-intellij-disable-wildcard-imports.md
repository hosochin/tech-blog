---
layout: post
title: 【IntelliJ】インポート文をまとめないようにする
date: 2022-02-27
tags: [intellij, java, ide, code-style, import]
---

## はじめに

お世話になります、hosochinです

タイトルのとおりですが、IntelliJはデフォルト設定だとimport文を勝手にアスタリスクでまとめてくれます

が、プロジェクトによってはコーディング規約などでワイルドカードのimportを禁止している場合があり、この機能を無効にする手順をまとめました

## 手順

ItelliJ IDEA > Preference

検索ボックスで「code style」と検索 > Code StyleのJavaを選択 > importsタブを選択

「Class count to use import with '*':」がデフォルトで5とかになってると思います

同じクラスのインポートが5行以上あるとまとめちゃうよっていう設定ですね

ここを100とかにしてやれば結果的に無効にできます

「Names count to use static import with '*':」も同様です👍