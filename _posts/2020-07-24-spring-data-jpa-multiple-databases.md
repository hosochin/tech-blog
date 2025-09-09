---
layout: post
title: 【Spring Data JPA】複数のDBにアクセスする
date: 2020-07-24
tags: [spring, spring-data-jpa, mysql, docker, database, java]
---

# はじめに

お世話になります、hosochinです  

さて、今回は  
**「Spring Data JPAで複数DBにアクセスしてみた」**  
です  

今回はバージョンが異なるMySQLを２台用意して、それぞれのselect結果を返すコントローラを作ってみようと思います  
（Spring Data JPAについては詳しくは[こちら](https://spring.pleiades.io/spring-data/jpa/docs/current/reference/html/#preface)）

# 目次

- [準備](#準備)
- [実装](#実装)
- [実行結果](#実行結果)
- [まとめ](#まとめ)

# 準備

dockerでDB環境を準備しようと思います  
sample01、sample02というデータベースにuser_tableというテーブルを作ります  
データも準備しておきます

**docker-composeはこんな感じ**

```yaml
version: '3.3'
services:
  sample01:
    image: mysql:5.6
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: sample01
    ports:
      - "3315:3306"
    volumes:
      - ./docker/mysql/sample01/initdb.d:/docker-entrypoint-initdb.d
  sample02:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: sample02
    ports:
      - "3316:3306"
    volumes:
      - ./docker/mysql/sample02/initdb.d:/docker-entrypoint-initdb.d
```

**./docker/mysql/sample01/initdb.d/user_table.sql**

```sql
create table user_table (id varchar(5), first_name varchar(10), last_name varchar(10));
insert into user_table values ("001", "Taro", "Tanaka");
```

**./docker/mysql/sample02/initdb.d/user_table.sql**

```sql
create table user_table (id varchar(5), first_name varchar(10), last_name varchar(10));
insert into user_table values ("002", "Jiro", "Suzuki");
```

# 実装

**select結果を返すコントローラ**

```java
package com.example.demo.controller;

import com.example.demo.controller.resource.GetUserTaleResponse;
import com.example.demo.domain.repository.sample01.UserRepository01;
import com.example.demo.domain.repository.sample02.UserRepository02;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/sample")
public class SampleController {
    private final UserRepository01 userRepository01;
    private final UserRepository02 userRepository02;

    public SampleController(UserRepository01 userRepository01, UserRepository02 userRepository02) {
        this.userRepository01 = userRepository01;
        this.userRepository02 = userRepository02;
    }

    @GetMapping("/sample01")
    public GetUserTaleResponse getSample01() {
        return new GetUserTaleResponse(userRepository01.findAll());
    }

    @GetMapping("/sample02")
    public GetUserTaleResponse getSample02() {
        return new GetUserTaleResponse(userRepository02.findAll());
    }
}
```

**コントローラのレスポンスクラス**

```java
package com.example.demo.controller.resource;

import com.example.demo.domain.entity.UserEntity;
import java.util.List;
import lombok.AllArgsConstructor;
import lombok.Getter;

@AllArgsConstructor
@Getter
public class GetUserTaleResponse {
    private List<UserEntity> userEntities;
}
```

**user_tableテーブルのエンティティ**

```java
package com.example.demo.domain.entity;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="user_table")
@NoArgsConstructor
@AllArgsConstructor
@Getter
public class UserEntity {

    @Id
    @Column(name = "id")
    private String id;

    @Column(name = "first_name")
    private String firstName;

    @Column(name = "last_name")
    private String lastName;
}
```

**ここからが重要！**  
configとrepositoryを接続先DBごとに作ってやる必要があります  
そのときにrepositoryのパッケージを分けるのがポイントです  
configファイル内のEnableJpaRepositoriesで指定したパッケージを検索してrepositoryをbean登録しているためです  

ディレクトリ構成はこんな感じになります

```
demo/src/main/java/com/example/demo
　├ controller/
　└ domein
    　├ entity/
    　└ repository
           ├ sample01
           |    └ UserRepository01.java
           |
           ├ sample02
           |    └ UserRepository02.java
           |
           └ config
                ├ Sample01MysqlConfig.java
                └ Sample02MysqlConfig.java
```

**Sample01のconfigファイル**

```java
package com.example.demo.domain.repository.config;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter;

import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;

@Configuration
@EnableJpaRepositories(basePackages = {
        "com.example.demo.domain.repository.sample01" }, entityManagerFactoryRef = "sample01EntityManager")
public class Sample01MysqlConfig {
    @Value("${spring.datasource.sample01.driverClassName}")
    private String driverClassName;
    @Value("${spring.datasource.sample01.url}")
    private String url;
    @Value("${spring.datasource.sample01.username}")
    private String username;
    @Value("${spring.datasource.sample01.password}")
    private String password;

    @Bean("sample01")
    @Primary // @Primaryをどちらか片方(今回の場合ならsample01 or sample02)だけにつける必要がある
    public DataSource createDataSource() {
        return DataSourceBuilder
                .create()
                .driverClassName(driverClassName)
                .url(url)
                .username(username)
                .password(password)
                .build();
    }

    @Bean(name = "sample01EntityManager")
    @Primary // @Primaryをどちらか片方(今回の場合ならsample01 or sample02)だけにつける必要がある
    public EntityManagerFactory mySqlEntityManagerFactory(@Qualifier("sample01") DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
        factory.setDataSource(dataSource);
        factory.setPackagesToScan("com.example.demo.domain.entity");

        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        factory.setJpaVendorAdapter(vendorAdapter);
        factory.afterPropertiesSet();

        return factory.getObject();
    }
}
```

**Sample02のconfigファイル**

```java
package com.example.demo.domain.repository.config;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter;

import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;

@Configuration
@EnableJpaRepositories(basePackages = {
        "com.example.demo.domain.repository.sample02" }, entityManagerFactoryRef = "sample02EntityManager")
public class Sample02MysqlConfig {
    @Value("${spring.datasource.sample02.driverClassName}")
    private String driverClassName;
    @Value("${spring.datasource.sample02.url}")
    private String url;
    @Value("${spring.datasource.sample02.username}")
    private String username;
    @Value("${spring.datasource.sample02.password}")
    private String password;

    @Bean("sample02")
    public DataSource createDataSource() {
        return DataSourceBuilder
                .create()
                .driverClassName(driverClassName)
                .url(url)
                .username(username)
                .password(password)
                .build();
    }

    @Bean(name = "sample02EntityManager")
    public EntityManagerFactory mySqlEntityManagerFactory(@Qualifier("sample02") DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
        factory.setDataSource(dataSource);
        factory.setPackagesToScan("com.example.demo.domain.entity");

        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        factory.setJpaVendorAdapter(vendorAdapter);
        factory.afterPropertiesSet();

        return factory.getObject();
    }
}
```

**sample01のrepository**

```java
package com.example.demo.domain.repository.sample01;

import com.example.demo.domain.entity.UserEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository01 extends JpaRepository<UserEntity, String> {
}
```

**sample02のrepository**

```java
package com.example.demo.domain.repository.sample02;

import com.example.demo.domain.entity.UserEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository02 extends JpaRepository<UserEntity, String> {
}
```

# 実行結果

```bash
curl http://localhost:8080/sample/sample01
{"userEntities":[{"id":"001","firstName":"Taro","lastName":"Tanaka"}]}

curl http://localhost:8080/sample/sample02
{"userEntities":[{"id":"002","firstName":"Jiro","lastName":"Suzuki"}]}
```

# まとめ

今回はテーブル構成が同じだったのでentityクラスは1つでしたが、異なる場合は勿論その分だけ必要になります  
データを旧DBから新DBに移行する必要がでた、その際にデータの持ち方を直したいみたいな案件とかは参考になるかなと思います！