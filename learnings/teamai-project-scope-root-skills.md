---
title: 激活项目后根目录 skill 不再同步
tags: [teamai, skills, projects]
---

# 激活项目后根目录 skill 不再同步

团队仓库有 manifest/projects.yaml 且当前目录激活了项目后，`teamai pull` 只同步已激活命名空间里的 skill，
直接放在 `skills/` 根目录的 skill 会被清理。所有项目都要用的 skill 放进 `skills/common/`，
并在每个项目的 `skills` 列表里加上 `common`。
