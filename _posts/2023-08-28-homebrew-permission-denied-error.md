---
layout: post
title: '【brew】Error: Permission denied @ dir_s_mkdir – /private/tmp/homebrew~ って怒られる'
date: 2023-08-28
tags: [homebrew, brew, macos]
---

# はじめに

お世話になります、hosochinです  
今回は  
**「brew install XXXX したら、Error: Permission denied @ dir_s_mkdir – /private/tmp/homebrew~」**  
って怒られた話です

# 解決方法

実行ユーザに **/private/tmp/** への権限がないって怒られてます  
/private/tmp/の権限を当てます

```bash
$ sudo chown -R "ユーザ名" /private/tmp/

e.g.)
$ sudo chown -R "hosochin" /private/tmp/
```

再度、エラーになっていた brew install を実行したら最後まで通りました👍

ちなみにユーザ名を確認するコマンド

```bash
$ who am i
```

# まとめ

Homebrewでパーミッション関連のエラーが発生した場合、/private/tmp/ディレクトリの権限設定を確認することで解決できる場合が多いです。開発環境でよく遭遇するエラーなので、覚えておくと便利ですね。
