---
title: cyrax 迁移里 CONCURRENTLY 索引必须单独一个文件
tags: [cyrax, postgres, migration]
---

# cyrax 迁移里 CONCURRENTLY 索引必须单独一个文件

现象：把 `CREATE INDEX CONCURRENTLY` 和其他语句写进同一个迁移文件，执行时报错。

原因：PostgreSQL 不允许在事务块里执行 `CREATE INDEX CONCURRENTLY`，多条语句的迁移文件通常会被包进事务。

做法：每个 `CONCURRENTLY` 索引单独一个迁移文件，文件里只有这一条语句。
