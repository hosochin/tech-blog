---
layout: post
title: 【bash】コマンド出力結果をdiffコマンドで比較
date: 2021-05-29
tags: [bash, linux, shell, diff, command-line]
---

## はじめに

お世話になります、hosochinです

今回は、**「コマンド出力結果を使ってdiffをとる」**についてです

diffコマンド使うときにいちいちファイルに書き出してから比較してる人いらっしゃいませんか？

## コマンド出力結果を使ってdiffコマンド

`<()` でコマンドを囲ってやるだけです👍

```bash
# 「curl XXXXX」 の結果と 「curl xxxxx」 の結果を比較したい

# コマンド結果でdiff
diff <(curl XXXXX) <(curl xxxxx)

# ファイルに出力してからdiff
curl XXXXX > A.txt
curl xxxxx > B.txt
diff A.txt B.txt
```