---
layout: post
title: 【Github】空コミットをpushする
date: 2022-11-04
tags: [git, github]
---

# はじめに

お世話になります、hosochinです  
空コミットをpushする方法についてのメモです

# 目次

- [やってみる](#やってみる)

# やってみる

「allow-empty」をつけてやることでコードに変更を加えずにコミット可能です

```bash
# コミットする
git commit --allow-empty -m "空コミット"

# プッシュする
git push origin ブランチ名
```
