---
title: grok-bot bootstrap 很慢多半是没拉 LFS
tags: [grok-bot, build, git-lfs]
---

# grok-bot bootstrap 很慢多半是没拉 LFS

现象：`npm run bootstrap` 长时间卡在下载。

原因：bootstrap 优先用 Git LFS 里保存的 0.18.0 DMG；没执行 `git lfs pull` 时会回退到公开地址下载原始安装包。

做法：先 `git lfs install && git lfs pull`；本机已经装了原版 app 的话，也可以用 `GROK_BOT_018_APP` 指向它。
