---
title: axonhub 的 llm 模块测试要在子目录里跑
tags: [axonhub, go, testing]
---

# axonhub 的 llm 模块测试要在子目录里跑（teamai demo · axonhub 私有 learning）

现象：在仓库根目录执行 `go test ./llm/...` 报 module 边界错误，看起来像代码坏了。

原因：`llm/` 是独立的 Go module（`github.com/looplj/axonhub/llm`），根目录的主 module 管不到它。

做法：`cd llm && go test ./...`。
