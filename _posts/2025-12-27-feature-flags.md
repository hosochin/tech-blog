---
layout: post
title: 【Spring Boot】機能フラグ（Feature Flags）を実装する
date: 2025-12-27
tags: [Java, Spring Boot]
---

# はじめに

お世話になります、hosochinです。
今回はJava/Spring Bootで機能フラグ（Feature Flags）を導入する方法についてです。
機能フラグを使うことで、コードを変更せずに機能のON/OFFを切り替えることができます。

# 目次

- [機能フラグとは](#機能フラグとは)
- [機能フラグのメリット・デメリット](#機能フラグのメリットデメリット)
- [Spring Bootでの実装方法](#spring-bootでの実装方法)
- [実装例](#実装例)
- [まとめ](#まとめ)

# 機能フラグとは

機能フラグ（Feature Flags / Feature Toggles）は、実行時に機能のON/OFFを切り替えるための仕組みです。
環境変数や設定ファイルを使って、コードを変更せずに新機能の有効化/無効化を制御できます。

# 機能フラグのメリット・デメリット

## メリット

- **段階的なリリースが可能**: 新機能を特定のユーザーやステージング環境でのみ有効化できる
- **リスクの低減**: 問題が発生した場合、コードを戻さずに機能を無効化できる
- **並行開発の支援**: 複数の機能を同時に開発しながら、本番環境では無効にできる
- **A/Bテストの実施**: 異なる実装を切り替えて比較検証できる

## デメリット

- **コードの複雑化**: 分岐処理が増えるため、コードが読みにくくなる可能性がある
- **技術的負債**: 古い機能フラグが残り続けると、メンテナンス性が低下する
- **テストケースの増加**: ON/OFF両方のパターンをテストする必要がある

# Spring Bootでの実装方法

今回は、`@ConfigurationProperties`を使った実装方法を紹介します。
重要なポイントは以下の通りです：

1. **環境変数で制御**: `application.yml`で設定し、環境変数で上書き可能にする
2. **Beanごとの差し替えをしない**: 複数案件が同時並行で進んでいる場合、同じクラスに修正を加えたいケースを考慮
3. **Staticメソッドでも利用可能**: `@PostConstruct`を使って静的インスタンスを保持する

# 実装例

## 1. 機能フラグ設定クラス

`@ConfigurationProperties`と`record`を使ってシンプルに実装します。
`@PostConstruct`で静的インスタンスを保持することで、staticメソッドからも参照可能にします。

機能フラグを1つのクラスで一元管理することで、どの機能フラグが現在使われているかが一目でわかります。
これにより、不要になった機能フラグの削除忘れを防ぎ、コードベース全体への汚染を最小限に抑えることができます。

```java
package com.example.demo.config;

import jakarta.annotation.PostConstruct;
import org.springframework.boot.context.properties.ConfigurationProperties;

/** 機能フラグを一元管理するクラス */
@ConfigurationProperties(prefix = "feature")
public record FeatureFlags(boolean newUiEnabled) {
  private static FeatureFlags staticFeatureFlags;

  @PostConstruct
  private void init() {
    staticFeatureFlags = new FeatureFlags(newUiEnabled);
  }

  public static FeatureFlags getInstance() {
    return staticFeatureFlags;
  }
}
```

## 2. application.yml

環境変数で上書き可能な設定として定義します。

```yaml
# 機能フラグ設定
feature:
  # 新しいUI機能
  new-ui-enabled: ${FEATURE_NEW_UI_ENABLED:false}
```

## 3. サービスクラスでの利用

DIでも静的参照でも両方使えます。

```java
package com.example.demo.service;

import com.example.demo.config.FeatureFlags;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class SampleService {

  private final FeatureFlags featureFlags;

  public String execute() {
    // パターン1: DIして使う場合
    if (featureFlags.newUiEnabled()) return "機能フラグON";
    return "機能フラグOFF";

    // パターン2: static参照（staticメソッド内でも利用可能）
    // if (FeatureFlags.getInstance().newUiEnabled()) return "機能フラグON";
    // return "機能フラグOFF";
  }
}
```

## 環境変数での上書き

本番環境では環境変数で機能フラグを制御できます：

```bash
# 環境変数で機能を有効
export FEATURE_NEW_UI_ENABLED=true

# またはアプリケーション起動時に指定
java -jar app.jar --feature.new-ui-enabled=true
```

# まとめ

Spring Bootで機能フラグを実装する方法を紹介しました。

**ポイント**:
- `@ConfigurationProperties`を使ってシンプルに実装
- 環境変数で簡単にON/OFF切り替え可能
- `@PostConstruct`を使うことでstaticメソッドからも参照可能

機能フラグは便利な仕組みですが、使い終わった機能フラグは定期的に削除し、コードをクリーンに保つことが重要です。
