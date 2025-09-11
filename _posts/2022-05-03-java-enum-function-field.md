---
layout: post
title: 【Java】Enumの要素に関数を定義する
date: 2022-05-03
tags: [Java]
---

# はじめに

お世話になります、hosochinです  
Enumのフィールドに使用するメソッドを定義したいって思ったことありますよね？  
ね？  
ってことで今回はEnumのフィールドに関数を定義する方法です  
関数型インターフェース定義するだけですごめんなさいやっていきます

# やってみる

Foodテーブルがあるとします  
Foodテーブルには食べ物をキーとして地域ごとの味付けの情報が格納されているとします

**こんな感じ**

| foodName(Primary key) | typeSapporo | typeYokohama | typeFukuoka |
|---|---|---|---|
| ラーメン | 味噌 | 豚骨醤油 | 豚骨 |

今回はラーメンの地域ごとの味付情報を取得する機能を実装するとします  
登場人物はRamenServiceとFoodObjectとFoodEnumです

**FoodObject**

```java
@RequiredArgsConstructor
@Getter
/**
* DBのデータをマッピングするオブジェクト
*/
public class FoodObject {
    /**
     * 食べ物名
     */
    private final String foodName;
    /**
     * 札幌の味付け
     */
    private final String typeSapporo;
    /**
     * 横浜の味付け
     */
    private final String typeYokohama;
    /**
     * 福岡の味付け
     */
    private final String typeFukuoka;
}
```

**FoodEnum**

```java
@RequiredArgsConstructor
@Getter
public enum FoodEnum {
    SAPPORO("札幌", FoodObject::getTypeSapporo),
    YOKOHAMA("横浜", FoodObject::getTypeYokohama),
    FUKUOKA("福岡", FoodObject::getTypeFukuoka);

    private final String prefecture;
    private final Function<FoodObject, String> function;

    /**
     * 地域にヒットしたFoodEnumを返却する
     */
    public static FoodEnum getFoodEnum(String prefecture) {
        return Arrays.stream(FoodEnum.values())
                .filter(e -> e.getPrefecture().equals(prefecture))
                .findFirst()
                .orElse(null);
    }
}
```

**RamenService**

```java
@Service
public class RamenService {

    /**
     * 地域ごとのラーメン情報を取得するメソッド
     */
    public String findRamen(String prefecture) {
        // DBからデータを取ってくる
        FoodObject foodObject = findFood("ラーメン");
        // 地域ごとの属性を取得
        return FoodEnum.getFoodEnum(prefecture).getFunction().apply(foodObject);
    }

    /**
     * サンプルデータを返却
     */
    private FoodObject findFood(String foodName) {
        // foodNameでDBを検索
        // 今回はfoodName = ラーメンを固定で返却するとする
        return new FoodObject(
                "ラーメン",
                "味噌",
                "豚骨醤油",
                "豚骨");
    }
}
```

はい、以上がサンプルになります  
ポイントはFoodEnumにFunctionを定義してやっていることです  
こうすることでたとえば以下のようなswitchやif文をかかずにすむってことです👍

```java
FoodEnum foodEnum = FoodEnum.getFoodEnum(prefecture);
switch (foodEnum) {
    case SAPPORO:
        return foodObject.getTypeSapporo();
    case YOKOHAMA:
        return foodObject.getTypeYokohama();
    case FUKUOKA:
        return foodObject.getTypeFukuoka();
}
```

まあそもそもこうなっちゃってるのはテーブル定義がいけてなかったりすると思うんですが…まあその辺はテーブル定義から見直すってなるとやばいみたいなのが多々あると思うので、上記のようにEnumにFunctionとか定義してやればいい感じに実装できるかもです

# まとめ

Enumに関数型インターフェースを定義することで、switch文やif文を排除しつつ、各列挙値に固有の処理ロジックを持たせることができます。これにより、コードの保守性と拡張性が向上します。

関数型インターフェースについてはこちらにもう少し詳しく載せてます

> [【Java】標準関数型インターフェース]({{ '/java-functional-interfaces' | relative_url }})
