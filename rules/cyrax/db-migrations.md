---
paths:
  - "server/migrations/**"
---

# 数据库迁移

- 不加外键，也不加级联；关联关系和清理在应用层用事务处理
- 所有索引都用 `CREATE [UNIQUE] INDEX CONCURRENTLY`，每个迁移文件只放这一条语句
