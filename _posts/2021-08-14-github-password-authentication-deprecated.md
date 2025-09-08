---
layout: post
title: 【GitHub】パスワード認証のサポートが廃止
date: 2021-08-14
tags: [github, git, authentication, personal-access-token, security, mac]
---

# はじめに

お世話になります、hosochinです

今回は  
**「GitHubのパスワード認証サポートが廃止されたのでパーソナルアクセストークンを使用するようにした」**  
です

いつも通りリポジトリをcloneしようとしたら、

```bash
Cloning into 'repository'...
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: unable to access 'https://github.com/user/repository.git/': The requested URL returned error: 403
```

....😇

原因は2021/8/13にGitHubでパスワード認証が廃止されたためです

今後はHTTPS Git操作する場合はトークン使ってねーってことらしいです

[参考](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)

なのでパーソナルアクセストークンの設定をやっていこうと思います！

# 目次

- [パーソナルアクセストークンの作成](#パーソナルアクセストークンの作成)

# パーソナルアクセストークンの作成

[ここ](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) 見ながらやっていきます

ブラウザでGitHubを開いたら、

1. [右上のプロフィール写真からSettings > Developer settings > Personal access tokens と進みます](https://github.com/settings/tokens)
2. Generate new token をクリック
3. 必要なチェックをしてから Generate token をクリック（今回はrepoとdelete_repoにチェック）
4. 生成されたトークンが表示されるのでクリップボードにコピーします（ページから離れると再度トークンは表示できないので注意）
5. git操作をターミナルから行ってみましょう

```bash
git clone https://github.com/user/repository.git
Cloning into 'repository'...
Username for 'https://github.com': ユーザ名
Password for 'https://user@github.com': コピーしたアクセストークン
```

ちなみに、古いユーザ名とパスワードがキャッシュされている場合はキャッシュを削除しないとです

[このへん](https://docs.github.com/ja/github/getting-started-with-github/getting-started-with-git/updating-credentials-from-the-macos-keychain) が参考になるかと思います！
