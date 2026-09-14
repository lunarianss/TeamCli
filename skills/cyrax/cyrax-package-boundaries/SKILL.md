---
name: cyrax-package-boundaries
description: cyrax-cli（Multica 分支）前端 monorepo 的包边界与状态管理硬规则。改 packages/core、packages/ui、packages/views 或新增 store 前使用。
---
# cyrax-cli 包边界检查

动手前先确认改动放在哪个包里：

| 包 | 禁止 |
|---|---|
| `packages/core/` | react-dom、localStorage、process.env |
| `packages/ui/` | 任何 `@multica/core` 引用 |
| `packages/views/` | `next/*`、`react-router-dom`（路由用 `NavigationAdapter`） |

- Next.js API 只能出现在 `apps/web/platform/`。
- 服务端数据归 React Query 管；Zustand 只放客户端视图状态，而且 store 只能建在 `packages/core/`。
- 数据库迁移不加外键；索引一律 `CREATE [UNIQUE] INDEX CONCURRENTLY`，每个迁移文件只放一条。
