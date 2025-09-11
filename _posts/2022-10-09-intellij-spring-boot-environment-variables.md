---
layout: post
title: 【IntelliJ】【SpringBoot】ローカル実行で環境変数を設定, 環境ごとのプロパティファイルを読み込む
date: 2022-10-09
tags: [intellij, spring-boot, environment-variables, active-profiles]
---

# はじめに

お世話になります、hosochinです  
IntelliJでSpringBootアプリケーションを起動するときに、環境変数を設定する方法について簡単なメモです

# 目次

- [手順](#手順)

# 手順

## Run Configurationの編集

上のタブからRun > Edit Configurationsを選択

<img src="{{ '/assets/images/2022_10_09_1_1.png' | relative_url }}" alt="IntelliJ Edit Configurations" class="img-large">

## Active profilesの設定

Active profiles に環境変数を設定

<img src="{{ '/assets/images/2022_10_09_1_2.png' | relative_url }}" alt="Active profiles設定画面" class="img-large">

上記の設定でRunした場合、application-prod.ymlが読み込まれる  
application-prod.ymlがない場合はapplication.ymlが読み込まれる
