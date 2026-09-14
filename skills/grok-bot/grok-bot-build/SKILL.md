---
name: grok-bot-build
description: 在 grok-bot-0.18-reconstructed 仓库里从零构建并打包 macOS app 的步骤与约束。用户要求 bootstrap、构建、打包或排查构建失败时使用。
---
# grok-bot-0.18-reconstructed 构建流程

Node 版本必须在 26.5.0 到 27 之间（`.node-version` 是 26.5.0）。

按顺序执行：

```sh
git lfs install && git lfs pull   # 取回原版安装包的 LFS 保存副本
npm ci
npm run bootstrap                 # 校验 0.18.0 DMG 和 app.asar 的 SHA-256，填充被忽略的 src/app/dist
npm run check                     # typecheck + source:typecheck + node --test
npm run package                   # 产物在 dist/Grok Bot 0.18 Reconstructed.app
```

- bootstrap 优先用 Git LFS 里的 DMG，没有时回退到公开下载地址；可以用 `GROK_BOT_018_APP` 指向本机已有的 app。
- 不要提交解包出来的上游 app、构建产物或本地凭证，仓库刻意不收这些。
- 打包出来的 app 用独立的 bundle id 和 ad-hoc 签名，不会覆盖本机装的原版。
