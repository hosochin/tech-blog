---
layout: post
title: "Jekyll環境構築ガイド"
date: 2021-08-31
tags: [github, Jekyll, Ruby]
---

# Jekyll環境構築ガイド

Jekyll を使った静的サイトの構築方法について説明します。

# 必要な構成ファイル

## _config.yml
サイトの基本設定を行うファイルです。

```yaml
title: サイト名
description: サイトの説明
url: https://example.com
permalink: /:title/
```

## Gemfile
Ruby のパッケージ管理ファイルです。

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
```

# レイアウトファイル

## _layouts/default.html
全ページ共通のHTMLテンプレートです。

## _layouts/post.html
記事ページ専用のテンプレートです。

# 記事の作成

`_posts/YYYY-MM-DD-title.md` の形式でMarkdownファイルを作成します。

```markdown
---
layout: post
title: "記事タイトル"
date: 2025-08-31
---

記事の内容をここに書きます。
```

# ローカルでの確認

```bash
bundle exec jekyll serve
```

これでローカル環境での確認ができます。
