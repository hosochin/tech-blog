---
layout: post
title: '【Docker】コンテナに入ろうとしたら "permission denied: unknown"'
date: 2020-07-24
tags: [docker, mysql, troubleshooting]
---

# はじめに

お世話になります、hosochinです  

今回はDocker使ってて、コンテナに入ろうとしたら  
**「permission denied: unknown」**  
と怒られたのでメモ

# 問題の症状

sample_mysqlというコンテナに入ろうとしてdocker execしたら、以下のエラーが発生：

```bash
docker exec -it sample_mysql /bin/bash
failed to create runc console socket: mkdir /tmp/pty069852850: permission denied: unknown
```

# 解決方法

tmpのパーミッションを修正してやればOK

```bash
sudo chmod 777 /tmp/
```

これでコンテナに正常に入れるようになります。

# まとめ

Dockerでコンテナに入ろうとした際の「permission denied: unknown」エラーは、tmpディレクトリのパーミッション問題が原因でした。chmod 777で解決できます。