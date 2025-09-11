---
layout: post
title: 【Python】urllibを使ったリクエストでSSL証明書のエラー
date: 2022-05-03
tags: [Python]
---

# はじめに

お世話になります、hosochinです

タイトルのとおりで、以下のようなエラーが発生して困ってました

python3使ってます

* 実行コード

```python
from urllib import request

url = "https://xxxxxxxx"
get_req = request.Request(url)
request.urlopen(get_req)
```

* エラー内容

```bash
...省略
urllib.error.URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1129)>
```

SSL証明書の検証に失敗した的なエラーが発生

ちょっとハマってました

結論からいうと以下を追加して、エラーを無視してうまくいきました

```python
import ssl

ssl._create_default_https_context = ssl._create_unverified_context
```

# ほかに試したこと

モジュールのアップデートなど試しましたが今回はうまくいかなかったです

一応試したことを載せておきます

```bash
pip3 install --upgrade pip

pip3 install --upgrade certifi

pip3 install --upgrade requests
```
