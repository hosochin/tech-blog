---
layout: post
title: 【AWS】CloudSearchで全文検索 Javaでアクセスする編
date: 2022-05-05
tags: [AWS, CloudSearch, Java, Spring Boot]
---

# はじめに

お世話になります、hosochinです

今回はCloudSearchをJavaで使いたいと思います

# 目次

- [前提](#前提)
- [やってみる](#やってみる)
  - [環境](#環境)
  - [実装](#実装)
- [まとめ](#まとめ)

# 前提

CloudSearchの環境構築 & データインポートまで終わっているとします

環境構築とデータインポートの手順については、AWSの公式ドキュメントを参照してください

# やってみる

awsが提供しているjavaライブラリの [AmazonCloudSearchDomain](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/cloudsearchdomain/AmazonCloudSearchDomain.html) ってのを使って検索します

今回はfirst_nameとlast_nameを検索する機能を実装してみました

しかし自作したサンプルデータ気が効かなすぎる。。チュートリアル用にCloudSearch側が用意したサンプルデータあるんでそっち使った方がよかったりする（今更）

## 環境

* Java: 1.8
* SpringBoot: 2.6.7
* Gradle: 7.4.1

## 実装

* build.gradleにaws-java-sdk-cloudsearchを追加
  * 最新バージョンは[こちら](https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-cloudsearch)

```gradle
implementation group: 'com.amazonaws', name: 'aws-java-sdk-cloudsearch', version: '1.11.447'
```

* CloudSearchのクライアントクラス

```java
package com.example.tutorialcloudsearch;

import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.client.builder.AwsClientBuilder;
import com.amazonaws.services.cloudsearchdomain.AmazonCloudSearchDomain;
import com.amazonaws.services.cloudsearchdomain.AmazonCloudSearchDomainClientBuilder;
import com.amazonaws.services.cloudsearchdomain.model.Hit;
import com.amazonaws.services.cloudsearchdomain.model.SearchRequest;
import com.amazonaws.services.cloudsearchdomain.model.SearchResult;
import org.springframework.stereotype.Component;

import java.util.ArrayList;
import java.util.List;

@Component
public class CloudSearchClient {
    // アクセスポリシーで設定したアカウントのアクセスキー 
    private static final String ACCESS_KEY = "XXXXXXXXXXXXXX";
    // アクセスポリシーで設定したアカウントのシークレットキー
    private static final String SECRET_KEY = "YYYYYYYYYYYYYY";
    // ダッシュボードで確認できるエンドポイント
    private static final String END_POINT = "https://search-search-test-misbuybhc7wm62ox6m3cvjq2s4.ap-northeast-1.cloudsearch.amazonaws.com";
    // リージョン
    private static final String REGION = "ap-northeast-1";

    private AmazonCloudSearchDomain getAmazonCloudSearchDomainClient() {
        // クレデンシャル情報生成
        BasicAWSCredentials credentials = new BasicAWSCredentials(
                ACCESS_KEY,
                SECRET_KEY
        );
        // エンドポイントの情報を生成
        AwsClientBuilder.EndpointConfiguration endpointConfiguration =
                new AwsClientBuilder.EndpointConfiguration(
                        END_POINT,
                        REGION
                );

        // ビルダーを使ってクライアントを生成する
        return AmazonCloudSearchDomainClientBuilder
                .standard()
                .withEndpointConfiguration(endpointConfiguration)
                .withCredentials(new AWSStaticCredentialsProvider(credentials))
                .build();
    }

    // SearchRequestに設定してある検索条件で検索する
    public List<Hit> search(SearchRequest searchRequest) {
        SearchResult result = getAmazonCloudSearchDomainClient().search(searchRequest);

        if (result.getHits().getFound() == 0) {
            return new ArrayList<>();
        }

        return result.getHits().getHit();
    }
}
```

* サービスクラス
  * 今回はfirst_nameとlast_nameのどちらかに一致するかを検索する

```java
package com.example.tutorialcloudsearch;

import com.amazonaws.services.cloudsearchdomain.model.Hit;
import com.amazonaws.services.cloudsearchdomain.model.QueryParser;
import com.amazonaws.services.cloudsearchdomain.model.SearchRequest;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
@RequiredArgsConstructor
public class CloudSearchService {

    private final CloudSearchClient cloudSearchClient;

    // first_nameとlast_nameを検索する
    public void search(String firstName, String lastName) {
        // 検索条件を設定する
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.setQueryParser(QueryParser.Structured); // 検索タイプを指定
        searchRequest.setQuery(makeQuery(firstName, lastName)); // 検索クエリを指定
        searchRequest.setReturn("first_name,last_name,pr,_score"); // 戻り値のカラムを指定
        searchRequest.setSort("_score desc"); // ソート順をスコアの降順に指定
        searchRequest.setSize(10L); // 返却サイズを最大10件に指定

        List<Hit> result = cloudSearchClient.search(searchRequest);

        result.forEach(
                r -> {
                    System.out.println(r.getFields().get("first_name"));
                    System.out.println(r.getFields().get("last_name"));
                    System.out.println(r.getFields().get("pr"));
                    System.out.println(r.getFields().get("_score"));
                }
        );
    }

    // クエリを生成する
    private String makeQuery(String firstName, String lastName) {
        // 以下のような値が生成される
        // (or first_name: '$firstName' last_name: '$lastName')
        return "(or first_name:'" + firstName + "' " + "last_name:'" + lastName + "')";
    }
}
```

# まとめ

適当なコントローラ作って実行してみるとちゃんととれました！

他にもいろいろと検索条件を指定できますがめんどいんで試してないです

似た名前のライブラリで [AmazonCloudSearchClient](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/cloudsearchv2/AmazonCloudSearchClient.html) ってのもいるんですが、主にインデックス作ったりデータの追加削除などを行えるライブラリでした（最初よく読まずにこっち使って実装してて全然それっぽいメソッドないやんけって少しハマった）
