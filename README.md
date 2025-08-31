# hosochin.com 技術ブログ

GitHub Pages + Jekyll で構築した技術ブログです。

## ローカル開発環境

### 必要なもの
- Ruby 3.0 以上（Homebrew推奨）
- Bundler

### セットアップ
```bash
# Homebrew で Ruby をインストール（未インストールの場合）
brew install ruby

# PATH を設定（セッション中のみ有効）
export PATH="/usr/local/opt/ruby/bin:$PATH"

# 依存関係をインストール
bundle install
```

### 開発サーバー起動
```bash
# PATH を設定してサーバー起動
export PATH="/usr/local/opt/ruby/bin:$PATH" && bundle exec jekyll serve

# またはホスト・ポート指定
export PATH="/usr/local/opt/ruby/bin:$PATH" && bundle exec jekyll serve --host 0.0.0.0 --port 4000
```

アクセス: http://localhost:4000/tech-blog/

### PATH を永続化したい場合
```bash
# ~/.zshrc に追記
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

## ファイル構成
```
├── _config.yml          # Jekyll設定
├── _layouts/            # HTMLテンプレート
│   ├── default.html     # 基本レイアウト
│   └── post.html        # 記事用レイアウト
├── _posts/              # 記事ファイル
├── assets/              # 画像・CSS等
├── index.md             # トップページ
└── Gemfile              # Ruby依存関係
```

## 記事の作成
1. `_posts/YYYY-MM-DD-title.md` 形式でファイル作成
2. Front Matter（記事メタデータ）を記述
3. Markdown で本文を作成

例:
```markdown
---
layout: post
title: "記事タイトル"
date: 2025-08-31
---

# 見出し

記事の内容...
```

## デプロイ
GitHub にプッシュすると自動でビルド・公開されます。

- 開発用URL: https://hosochin.github.io/tech-blog
- 本番予定URL: https://hosochin.com