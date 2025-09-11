---
layout: post
title: 【Spring Data JPA】save・saveAllでselectを省略する方法
date: 2020-08-16
tags: [Spring Boot, JPA, Spring Data JPA, Java]
---

# はじめに

お世話になります、hosochinです

さて今回は
**「Spring Data JPAでsave / saveAllでselectを省略する」**
です

Spring Data JPAではsaveメソッドを実行すると

1. 更新対象のキーでselect
2. 同じキーのレコードがあるかどうかチェック
   1. あったらupdate
   2. なかったらinsert

という順で処理してるっぽいです

このselectを省略して処理速度を上げたいなと思ったときのメモになります

# 目次

- [先に結論](#先に結論)
- [サンプル](#サンプル)
  - [ソースコード](#ソースコード)
  - [実行結果](#実行結果)
- [まとめ](#まとめ)

# 先に結論

先に結論を書いとくと、Entityクラスで[Persistable<ID>](https://spring.pleiades.io/spring-data/commons/docs/current/api/org/springframework/data/domain/Persistable.html)の[isNew](https://spring.pleiades.io/spring-data/commons/docs/current/api/org/springframework/data/domain/Persistable.html#isNew--)メソッドをオーバーライドしてあげることで実現できます

このisNewはbooleanを返すメソッドですが、

- リターンがtrue
  - selectを省略、insertを実行する
- リターンがfalse
  - selectを実行、同じキーがあるかどうかでinsert or update を実行する

となっているので、trueを固定で返すようにオーバーライドしてやればselectを省略して強制insertするような挙動にできます

（ただし既に同じPrimaryKeyのレコードが存在する場合はinsertできないので、saveメソッドでエラーが返ります）

# サンプル

user_tableっていうテーブルにデータを投入しようと思います

**user_table**

```sql
mysql> desc user_table;
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| id         | varchar(5)  | NO   | PRI |         |       |
| first_name | varchar(10) | YES  |     | NULL    |       |
| last_name  | varchar(10) | YES  |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
```

## ソースコード

**Controller**

```java
@RestController
@RequestMapping("/sample")
@RequiredArgsConstructor
public class SampleController {
    private final UserRepository01 userRepository01;

    @PostMapping
    public void post() {
        List<UserEntity> entities = Arrays.asList(
                new UserEntity("001", "Taro", "Tanaka"),
                new UserEntity("002", "Jiro", "Suzuki")
        );
        userRepository01.saveAll(entities);
    }
}
```

**Repository**

```java
import com.example.demo.domain.entity.UserEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository01 extends JpaRepository<UserEntity, String> {}
```

**Entity**

```java
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import org.springframework.data.domain.Persistable;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="user_table")
@NoArgsConstructor
@AllArgsConstructor
@Getter
// Persistableをimplementsする
// 型パラメータにはプライマリキーの型を入れる
public class UserEntity implements Persistable<String> {

    @Id
    @Column(name = "id")
    private String id;

    @Column(name = "first_name")
    private String firstName;

    @Column(name = "last_name")
    private String lastName;

    @Override
    public boolean isNew() {
        // trueの場合、selectを省略してinsertのみ
        // falseの場合、select後にupdate or insert
        return true;
    }
}
```

## 実行結果

```java
// ログ確認（show-sqlをtrueにしとく）

// isNewメソッドがfalseの場合
// selectが最初に走ってからinsert(update)を実行してる
Hibernate: select userentity0_.id as id1_0_0_, userentity0_.first_name as first_na2_0_0_, userentity0_.last_name as last_nam3_0_0_ from user_table userentity0_ where userentity0_.id=?
Hibernate: select userentity0_.id as id1_0_0_, userentity0_.first_name as first_na2_0_0_, userentity0_.last_name as last_nam3_0_0_ from user_table userentity0_ where userentity0_.id=?
Hibernate: insert into user_table (first_name, last_name, id) values (?, ?, ?)
Hibernate: insert into user_table (first_name, last_name, id) values (?, ?, ?)

// isNewメソッドがtrueの場合
// selectは省略されて、いきなりinsertしている
Hibernate: insert into user_table (first_name, last_name, id) values (?, ?, ?)
Hibernate: insert into user_table (first_name, last_name, id) values (?, ?, ?)
```

# まとめ

エンティティクラスでPersistableをimplementして、isNewメソッドをtrueにしたらselectが省略されましたね

ただ当たり前ですがすでに同じidのレコードがある場合に実行すると、

```java
java.sql.SQLIntegrityConstraintViolationException: Duplicate entry '001' for key 'PRIMARY'
```

ってエラーになっちゃいますね

Persistableインターフェースについて詳しくは[こちら](https://spring.pleiades.io/spring-data/data-commons/docs/current/api/?org/springframework/data/domain/Persistable.html)です
