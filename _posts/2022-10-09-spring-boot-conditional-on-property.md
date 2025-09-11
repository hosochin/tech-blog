---
layout: post
title: 【SpringBoot】読み込むプロパティファイルごとに処理を変える
date: 2022-10-09
tags: [java, spring-boot, conditional-on-property, configuration]
---

# はじめに

お世話になります、hosochinです  
今回はJavaのSpringBootで環境ごとに処理を変えたい場合の方法についてです  
[ConditionalOnProperty](https://spring.pleiades.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/condition/ConditionalOnProperty.html)というアノテーションを使うことで、プロパティファイルの設定値ごとに処理を変更することができます  
こいつを使って生成するbeanを出し分けることも可能です

# 目次

- [実装例](#実装例)
- [解説](#解説)

# 実装例

本番用の設定ファイル（application-prod.yml）と開発環境用の設定ファイル（application-dev.yml）を用意してあげて、設定ファイルの値を読み取って生成するbeanを変更します

## 本番用の設定ファイル（application-prod.yml）

```yaml
env: prod
```

## 開発環境用の設定ファイル（application-dev.yml）

```yaml
env: dev
```

## サービスクラスのConfiguration

今回のキモであるConditionalOnPropertyに注目

```java
@Configuration
public class SampleServiceConfig {

    @Bean
    @ConditionalOnProperty(value = "env", havingValue = "dev", matchIfMissing = true)
    public SampleService devService() {
        return new DevSampleService();
    }

    @Bean
    @ConditionalOnProperty(value = "env", havingValue = "prod")
    public SampleService prodService() {
        return new ProdSampleService();
    }

}
```

## サービスクラス

```java
public interface SampleService {
    String getToken();
}

public class DevSampleService implements SampleService {
    @Override
    public String getToken() {
        return "dev token";
    }
}

public class ProdSampleService implements SampleService {
    @Override
    public String getToken() {
        return "prod token";
    }
}
```

# 解説

SampleServiceConfigでプロパティファイルの「env」という値を読み取ってbeanを出し分けています  
「value」でプロパティファイルで定義したプロパティ名を指定します  
「havingValue」で期待値を設定し、一致した場合にその処理が読まれます  
「matchIfMissing」でデフォルトの処理を設定しています  
各オプションについての詳細は[こちら](https://spring.pleiades.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/condition/ConditionalOnProperty.html)を参照

IntelliJで環境変数を設定してアプリケーションを起動する方法

> [【IntelliJ】【SpringBoot】ローカル実行で環境変数を設定, 環境ごとのプロパティファイルを読み込む]({{ '/intellij-spring-boot-environment-variables' | relative_url }})