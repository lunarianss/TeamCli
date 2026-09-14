# 往 TeamCli 贡献内容

| 想加的东西 | 做法 |
|---|---|
| 所有项目共用的 skill | 在业务仓库写好 `.claude/skills/<name>/`，执行 `teamai push --all --role common` |
| 某个项目专属的 skill | 同上，把 `--role` 换成项目命名空间，例如 `--role axonhub` |
| rules、共享指令、MCP、hooks、docs | 直接改 TeamCli 仓库里对应的文件，走普通 PR |
| env | `teamai env add KEY VALUE -d "说明"`，再 `teamai push --all` |
| 踩坑经验 | 会话结束时运行 `/teamai-share-learnings`，或 `teamai contribute --file <文件>`（直接推 main） |
