---
layout: post
title: '【brew】Error: Unknown command: cask って怒られる'
date: 2021-04-01
tags: [brew, mac, macos, homebrew, troubleshooting]
---

## はじめに

お世話になります、hosochinです

今回は
**「brew cask install XXXX したら、Error: Unknown command: cask」**
って怒られた話です

## 目次

- [解決方法](#解決方法)

## 解決方法

結論から言うと brew cask install は廃止されたっぽいです

```bash
brew install XXXXX --cask
```

とすれば行けました🍺