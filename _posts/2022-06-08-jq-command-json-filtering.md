---
layout: post
title: 【jqコマンド】jsonの要素を絞り込み
date: 2022-06-08
tags: [jq, json, linux, bash, command-line]
---

# はじめに

お世話にになります、hosochinです  
さて、今回は  
**「jqコマンド」** でjsonを検索する  
です

jqコマンドはjsonデータをデータ抽出, 変換, 集計してくれるツールです  
今回はjqコマンドを使ったjsonデータの検索について、よく使いそうだなと思ったコマンドを載っけておこうと思います👍  
[公式ドキュメント](https://stedolan.github.io/jq/manual/#containselement) はこちら

# 目次

- [やってみる](#やってみる)
  - [JSONデータ](#jsonデータ)
- [まとめ](#まとめ)

# やってみる

## JSONデータ

**今回はこんな感じのjsonデータを用意しておきます**  
**動物園jsonデータです**
- "名前", "好物", "匹数", "肉食動物か否か", "草食動物か否か" のデータを保持しています

```json
{
  "zoo": [
    {
      "name": "ライオン",
      "favoriteFood": "生肉",
      "number": 1,
      "carnivore": true,
      "herbivore": false
    },
    {
      "name": "チーター",
      "favoriteFood": "生肉",
      "number": 1,
      "carnivore": true,
      "herbivore": false
    },
    {
      "name": "ゾウ",
      "favoriteFood": "草",
      "number": 2,
      "carnivore": false,
      "herbivore": true
    },
    {
      "name": "サル",
      "favoriteFood": "バナナ",
      "number": 5,
      "carnivore": true,
      "herbivore": true
    }
  ]
}
```

## 各種フィルタリング例

```bash
# zooの配列を取得
cat test.json | jq '[.zoo[]]'

# nameがライオンの動物を取得
# zooの配列の中からnameがライオンの要素を絞り込む
# mapは任意のフィルターについてjsonを返却する
cat test.json | jq '[.zoo[]]' | jq 'map(select( .name == "ライオン" ))'
```

**出力結果:**

```json
[
  {
    "name": "ライオン",
    "favoriteFood": "生肉",
    "number": 1,
    "carnivore": true,
    "herbivore": false
  }
]
```

```bash
# 好物が生肉の動物を取得
cat test.json | jq '[.zoo[]]' | jq 'map(select( .favoriteFood == "生肉" ))'
```

**出力結果:**

```json
[
  {
    "name": "ライオン",
    "favoriteFood": "生肉",
    "number": 1,
    "carnivore": true,
    "herbivore": false
  },
  {
    "name": "チーター",
    "favoriteFood": "生肉",
    "number": 1,
    "carnivore": true,
    "herbivore": false
  }
]
```

```bash
# 2匹以上の動物を取得
# 数値の比較
cat test.json | jq '[.zoo[]]' | jq 'map(select( .number >= 2 ))'
```

**出力結果:**

```json
[
  {
    "name": "ゾウ",
    "favoriteFood": "草",
    "number": 2,
    "carnivore": false,
    "herbivore": true
  },
  {
    "name": "サル",
    "favoriteFood": "バナナ",
    "number": 5,
    "carnivore": true,
    "herbivore": true
  }
]
```

```bash
# 肉食の動物を取得
# booleanの比較
cat test.json | jq '[.zoo[]]' | jq 'map(select( .carnivore == true ))'
```

```bash
# 肉食かつ草食の動物を取得
# and　でAND条件で絞り込み
# or　にすればOR条件で絞り込み
cat test.json | jq '[.zoo[]]' | jq 'map(select( .carnivore == true and .herbivore == true))'
```

**出力結果:**

```json
[
  {
    "name": "サル",
    "favoriteFood": "バナナ",
    "number": 5,
    "carnivore": true,
    "herbivore": true
  }
]
```

```bash
# 部分一致
# 名前に「イ」を含む動物を取得
cat test.json | jq '[.zoo[]]' | jq 'map(select( .name | contains("イ") ))'
```

**出力結果:**

```json
[
  {
    "name": "ライオン",
    "favoriteFood": "生肉",
    "number": 1,
    "carnivore": true,
    "herbivore": false
  }
]
```

# まとめ

jqコマンドを使うことで、JSONデータから条件に応じた要素を柔軟に絞り込むことができます。文字列の完全一致・部分一致、数値の比較、booleanの判定、AND/OR条件など、様々な検索パターンに対応可能です。

よく使いそうなコマンドだけ載せときました  
jqコマンド自体よく使いますが、正直jsonを整形して出力する程度でしか使ってなかったです。。めっちゃ便利なんでもっと有効活用していきたいっすね〜