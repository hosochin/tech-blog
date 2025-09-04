---
layout: post
title: 【Git】 既存リポジトリを新規リポジトリに移動する方法
date: 2020-03-30
tags: [git, github]
---

## はじめに

お世話になります、hosochinです  
昨日は雪降りましたね雪  
いいですね雪  

さて、今回は  
**Githubで既存リポジトリのソースを新規リポジトリにpushする方法**  
についてまとめます  

## サンプル

今回はold-repositoryの中身をnew-repositoryに移すのを例にやってみます 

### 準備

まずGithubのUI上から新規のリポジトリを作成しておきます  
次にコピー元になる既存リポジトリをcloneしてきます

```bash
$ git clone https://github.com/hosochin/old-repository.git
$ cd old-repository
```

cloneしたリソースのディレクトリにある`.git`を削除します

```bash
$ ls -a
$ rm -rf .git
```

これでold-repositoryとの紐づけが消えたので、あとはgit initしなおしてnew-repositoryにpushします

```bash
$ git init
$ git remote add origin https://github.com/hosochin/new-repository.git
$ git push -u origin master
```

これで既存リポジトリからcloneしてきたローカルソースを新規リポジトリにそのままpushすることができます

・・・しかしこれだとmasterブランチしかpushできていません。。  
全branchを一括でpushする方法も紹介します

### ブランチごと新規リポジトリにpushする

まずは cloneしたリソースのディレクトリ上で、既存リポジトリのブランチを全てpullしてきます  
`for remote in git branch -r; do git branch --track ${remote#origin/} $remote; done`で全てのブランチを取得しています

```bash
$ for remote in `git branch -r`; do git branch --track ${remote#origin/} $remote; done
$ git fetch --all
$ git pull --all
```

あとは新規リポジトリにpushするだけです

```bash
$ git remote set-url origin https://github.com/hosochin/new-repository.git

// 向き先あってるよね確認
$ git remote -v
origin  https://github.com/hosochin/new-repository.git (fetch)
origin  https://github.com/hosochin/new-repository.git (push)

$ git push --all origin
```

これで全てのブランチを新規ブランチにpushすることができました！