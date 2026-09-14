---
paths:
  - "internal/ent/schema/**"
  - "internal/server/gql/**"
---

# 改 Ent schema 或 GraphQL 之后

- 修改 `internal/ent/schema/` 或 `internal/server/gql/` 后执行 `make generate`（即 `cd internal/server/gql && go generate`）重新生成代码
- 详细约定见仓库自带的 `.agent/rules/ent-graphql.md` 和 `.agent/rules/database-indexes.md`
