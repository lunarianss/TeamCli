---
name: axonhub-go-test
description: axonhub 跑 Go 测试
---
# axonhub 跑 Go 测试

axonhub 仓库里有两个 Go 模块，跑测试前先确认代码属于哪个模块：

- 仓库根目录是主模块 `github.com/looplj/axonhub`，在根目录执行 `go test ./internal/...`
- `llm/` 是独立模块 `github.com/looplj/axonhub/llm`，必须先 `cd llm` 再执行 `go test ./...`
- 在仓库根执行 `go test ./llm/...` 会报模块边界错误，这不是代码问题

用户没有明确要求时，不要跑 lint 或 build，也不要重启开发服务（后端 8090，前端 5173 已由用户启动）。
