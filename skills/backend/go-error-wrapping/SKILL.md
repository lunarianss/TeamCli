---
name: go-error-wrapping
description: Go 后端错误处理约定：包装、判断和日志。写或审查 Go 代码里的 error 处理时使用。
---

# Go 错误处理

- 往上返回时用 `fmt.Errorf("做什么时失败: %w", err)` 包装，保留原始错误
- 判断错误类型用 `errors.Is` / `errors.As`，不要比较字符串
- 同一个错误只在最外层记一次日志，中间层只包装不打印
- 不要吞掉错误；确实可以忽略时写注释说明原因
