---
layout: post
title: 【GitHub】間違ってpushしたコミットを取り消す
date: 2020-09-06
tags: [git, github, version-control, troubleshooting]
---

# はじめに

お世話になります、hosochinです

今回は
**「GitHubで間違ってpushしたcommitを取り消す」**
です

# 目次

- [サンプル](#サンプル)
  - [リモートブランチのコミットログごと取り消す方法](#リモートブランチのコミットログごと取り消す方法)
  - [間違えたコミットはそのままにして変更だけを取り消す方法](#間違えたコミットはそのままにして変更だけを取り消す方法)

# サンプル

## リモートブランチのコミットログごと取り消す方法

コミット自体なかったことにする方法です

例えば削除しようとした変更を既に他のメンバーがローカルに反映させてた場合とかは、不整合が生じてpushできない！みたいな感じになってメンバーに迷惑かけるかもなので注意です😇

自分だけがいじっているブランチなら問題ないです

```bash
-- コミットログを表示
$ git log
commit 44a1863d7a4536efeb243737d30955eec0fea996 ← 取り消したいコミット
Author: xxx
Date:   xxx

    間違えたコミット

commit 2c7f71b81f7c94e047b23fa05d8efe43fe24bcc1
Author: xxx
Date:   xxx

    ここまで戻したいコミット
...

-- 戻したいコミットIDを指定
$ git push -f origin 2c7f71b81f7c94e047b23fa05d8efe43fe24bcc1:<対象ブランチ名>

-- コミットログを表示
-- コミットID:44a1863d7a4536efeb243737d30955eec0fea996が消えていることを確認
$ git log
commit 2c7f71b81f7c94e047b23fa05d8efe43fe24bcc1
Author: xxx
Date:   xxx

    XXX
...

-- リモートのコミットログは取り消したのでローカルの修正も取り消す
$ git reset --hard HEAD^
```

## 間違えたコミットはそのままにして変更だけを取り消す方法

コミットログは変えずにファイルの変更を取り消す方法です

取り消したいコミットの変更内容を打ち消すコミットを作ってpushするという方法になります

リモートブランチのコミットログごと削除する方法に比べ、不整合が生じてメンバーに迷惑かけるということはないです

が、「間違えたコミット」「間違えたコミットの修正を打ち消すコミット」というように不要なコミットが増えてしまいます

```bash
-- コミットログを表示
$ git log
commit 44a1863d7a4536efeb243737d30955eec0fea996 ← 取り消したいコミット
Author: xxx
Date:   xxx

    間違えたコミット

commit 2c7f71b81f7c94e047b23fa05d8efe43fe24bcc1
Author: xxx
Date:   xxx

    ここまで戻したいコミット
...

-- 取り消したいコミットの変更を打ち消す
$ git revert b1a87cfeb0c2944162550dde56412298ed83c5cc

-- pushする
$ git push origin <対象ブランチ名>

-- コミットログを表示
commit 7948601c95846d0442c375789f3b810e008e6bdc ← revertコミットが追加されている
Author: xxx
Date:   xxx

    Revert "間違えたコミット"

commit 44a1863d7a4536efeb243737d30955eec0fea996
Author: xxx
Date:   xxx

    間違えたコミット

commit 2c7f71b81f7c94e047b23fa05d8efe43fe24bcc1
Author: xxx
Date:   xxx

    ここまで戻したいコミット
```

複数コミットのrevertをしたい場合

```bash
$ git revert -n <取り消したいコミットID>
$ git revert -n <取り消したいコミットID>
$ git revert -n <取り消したいコミットID>
...

$ git commit

$ git push origin <対象リポジトリ>
```