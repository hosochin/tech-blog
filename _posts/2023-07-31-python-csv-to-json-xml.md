---
layout: post
title: 【Python】csvファイルからjson または xmlを生成する
date: 2023-07-31
tags: [Python, json]
---

# はじめに

お世話になります、hosochinです  
今回はcsvファイルを読み込んで、jsonまたはxmlファイルを生成するスクリプトをPythonを使って実装しようと思います

# 目次

- [実装](#実装)
  - [inputとなるcsvファイル](#inputとなるcsvファイル)
  - [csv → json](#csv--json)
  - [csv → xml](#csv--xml)

# 実装

## inputとなるcsvファイル

とりあえず今回はこんな形にしてみました

```csv
id,name
001,Taro
002,Jiro
```

## csv → json

実装

```python
import csv
import json

def csv_to_json(input_file, output_file):
    results = []

    with open(input_file, mode='r', encoding='utf-8') as csvfile:
        reader = csv.DictReader(csvfile)
        for row in reader:
            results.append(row)

            # カラム名を変えたい場合などは以下のように記述する
            # result = {
            #     "ID": row["id"],
            #     "NAME": row["name"]
            # }
            # results.append(result)

    with open(output_file, mode='w', encoding='utf-8') as jsonfile:
        json.dump({"results": results}, jsonfile, indent=2)

if __name__ == "__main__":
    input_file = "input.csv"   # 入力となるCSVファイル名を指定してください
    output_file = "output.json"  # 出力となるJSONファイル名を指定してください
    csv_to_json(input_file, output_file)
```

結果

```json
{
  "results": [
    {
      "id": "001",
      "name": "Taro"
    },
    {
      "id": "002",
      "name": "Jiro"
    }
  ]
}
```

## csv → xml

実装

```python
import csv
import xml.dom.minidom as minidom

def csv_to_xml(input_file, output_file):
    results = []

    with open(input_file, mode='r', encoding='utf-8') as csvfile:
        reader = csv.DictReader(csvfile)
        for row in reader:
            results.append(row)

    # XMLの整形を行う
    xml_data = minidom.Document()
    results_element = xml_data.createElement("results")
    xml_data.appendChild(results_element)

    for result in results:
        result_element = xml_data.createElement("result")
        for key, value in result.items():
            data_element = xml_data.createElement(key)
            data_element.appendChild(xml_data.createTextNode(value))
            result_element.appendChild(data_element)
        results_element.appendChild(result_element)

    # XMLをファイルに書き出す
    with open(output_file, mode='w', encoding='utf-8') as xmlfile:
        xmlfile.write(xml_data.toprettyxml(indent="  "))

if __name__ == "__main__":
    input_file = "input.csv"   # 入力となるCSVファイル名を指定してください
    output_file = "output.xml"  # 出力となるXMLファイル名を指定してください
    csv_to_xml(input_file, output_file)
```

結果

```xml
<?xml version="1.0" ?>
<results>
  <result>
    <id>001</id>
    <name>Taro</name>
  </result>
  <result>
    <id>002</id>
    <name>Jiro</name>
  </result>
</results>
```

# まとめ

PythonでCSVファイルをJSONやXML形式に変換する方法を紹介しました。csvモジュールとjson、xml.dom.minidomを使うことで簡単にデータ変換が可能です。データフォーマットの変換は開発でよく使う処理なので、覚えておくと便利ですね。
