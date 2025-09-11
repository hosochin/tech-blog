---
layout: post
title: 【Spring Data JPA】N+1問題
date: 2022-07-03
tags: [Spring Data JPA, Java]
---

# はじめに

お世話にになります、hosochinです  
**Spring Data JPA** でよくある **N+1問題** の解決方法についてメモします

# 目次

- [JPAのN+1問題](#jpaのn1問題)
- [N+1問題を発生させてみる](#n1問題を発生させてみる)
  - [DB](#db)
  - [実装](#実装)
  - [findAll()してみる](#findallしてみる)
- [解決方法](#解決方法)

# JPAのN+1問題

N+1問題とは、意図しないSQL文が大量に（取得するレコードの数だけ）発行されてしまう問題です  
この問題はO/R MapperによるSQL文の自動生成が原因で、たとえば以下のような現象になります

1. 対象テーブルからN個のレコードを取得するSELECT文を1回発行する
2. 1で取得したN個のレコードの関連データを取得するSELECT文を1回ずつ発行する（N回発行される）

# N+1問題を発生させてみる

## DB

以下のようなテーブルにレコードが入っているとします

**userテーブル**

| user_id (PK) | user_name |
|---|---|
| 1 | Taro |
| 2 | Jiro |
| 3 | Saburo |

**teamテーブル**
- **teamテーブル.user_id** は **userテーブル.user_id** と多対1の関係

| team_id (PK) | team_name | user_id (PK) |
|---|---|---|
| 1 | Aチーム | 1 |
| 1 | Aチーム | 2 |
| 2 | Bチーム | 3 |

## 実装

Lombokいれてます

**userテーブルのEntity**

```java
@Entity
@Table(name="user")
@Data
public class UserEntity {

    @Id
    @Column(name = "user_id")
    private String userId;

    private String userName;

}
```

**teamテーブルのEntity**

```java
@Entity
@Table(name="team")
@IdClass(value=TeamKey.class)
@Data
public class JoinTeamEntity implements Serializable {

    @Id
    @Column(name = "user_id")
    private String teamId;

    private String teamName;

    @Id
    private String userId;

    // userテーブルと結合するキー
    @ManyToOne
    @JoinColumn(name = "user_id", insertable = false, updatable = false)
    private UserEntity user;
}
```

**teamテーブルの複合主キークラス**

```java
@Data
public class TeamKey implements Serializable {

    @Id
    private String teamId;

    @Id
    @Column(name = "user_id")
    private String userId;

}
```

**repositoryクラス**

```java
public interface TeamRepository extends JpaRepository<JoinTeamEntity, TeamKey> {}
```

## findAll()してみる

application.propertiesのspring.jpa.show-sqlをtrueにすると発行されたクエリがログ出力されるようになります  
こいつをtrueにしてから動かします

**実行されたクエリ（見やすいように少し整形してます）**

```sql
select user_id, team_id, team_name from team;
select user_id, user_name from user where user_id='1';
select user_id, user_name from user where user_id='2';
select user_id, user_name from user where user_id='3';
```

最初にチームテーブルをselectして、そこで取得したuser_idの数だけuserテーブルにselectする…N+1問題が発生しています

# 解決方法

JPQLでJOIN FETCHを使って解決するという方法があります  
クエリのJOIN のあとに FETCH をつけるだけです

**repositoryクラス**

```java
public interface TeamRepository extends JpaRepository<JoinTeamEntity, TeamKey> {
        @Query("SELECT t FROM JoinTeamEntity t LEFT JOIN FETCH t.user")
        List<JoinTeamEntity> findAll();
}
```

**実行されるクエリ**

```sql
select
  team.user_id,
  team.team_id,
  team.team_name,
  user.user_id,
  user.user_name
from
  team team
left outer join user user on team.user_id=user.user_id;
```

# まとめ

Spring Data JPAのN+1問題は、関連エンティティの遅延ロードによって発生します。JOIN FETCHを使用したJPQLクエリを定義することで、1回のクエリで関連データを一括取得でき、パフォーマンスの問題を解決できます👍
