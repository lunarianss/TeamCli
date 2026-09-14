---
name: react-query-boundaries
description: 前端状态归属约定：服务端数据用 React Query，客户端视图状态用 Zustand。新增数据请求或 store 时使用。
---

# 状态放哪里

| 数据 | 放在 |
|---|---|
| 接口返回的数据（列表、详情、成员） | React Query，不要复制进 store |
| 纯前端视图状态（筛选条件、弹窗开关、草稿） | Zustand |

- WebSocket 推送的数据更新 React Query 缓存，不直接改 store
- 不要为了「方便取值」把接口数据同步到 Zustand，两份数据迟早不一致
