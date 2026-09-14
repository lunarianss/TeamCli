# TeamCli · teamai 完整示例团队仓库

这个仓库用 [teamai-cli](https://github.com/Tencent/teamai-cli) 把团队的 AI 工作方式分发到每个人的 Claude Code、Codex、Cursor、Grok。里面放齐了 teamai 支持的所有资源类型，每类都有能直接用的示例。

> 以下行为基于 teamai-cli 0.23.1 实测，文末列出了已知的坑。

## 目录结构

```text
TeamCli/
├── teamai.yaml                 # 团队配置：评审人、共享开关、packages、publicSkills
├── manifest/
│   ├── projects.yaml           # 逻辑项目：axonhub / grok-bot / cyrax
│   └── roles.yaml              # 角色：backend / frontend
├── tags.yaml                   # skill 标签，成员可按标签订阅
├── culture.md                  # 团队使命和协作准则，注入 CLAUDE.md
│
├── skills/                     # 按命名空间分目录，只同步已激活的命名空间
│   ├── common/                 #   所有项目：lavish、show-me、pr-description
│   ├── axonhub/                #   只给 axonhub
│   ├── grok-bot/               #   只给 grok-bot
│   ├── cyrax/                  #   只给 cyrax
│   ├── backend/                #   只给选了 backend 角色的人
│   └── frontend/               #   只给选了 frontend 角色的人
│
├── rules/                      # 根目录的规则所有人都同步；子目录按命名空间过滤
│   ├── commit-message.md       #   强制规则（teamai.yaml 里 enforced）
│   ├── reply-language.md
│   ├── common/test-policy.md
│   ├── axonhub/ent-schema-changes.md   # 带 paths：只在碰到 schema 文件时加载
│   ├── grok-bot/node-version.md
│   ├── cyrax/db-migrations.md          # 带 paths：只在碰到迁移文件时加载
│   └── backend/go-errors.md
│
├── claudemd/                   # 共享指令，必须放在命名空间子目录里
│   ├── common/team.md
│   ├── axonhub/dev-env.md
│   ├── grok-bot/build-input.md
│   └── cyrax/source-of-truth.md
│
├── learnings/                  # 经验库：根目录全员共享，子目录只给对应项目
│   ├── teamai-project-scope-root-skills.md
│   ├── axonhub/…  grok-bot/…  cyrax/…
│
├── docs/                       # 长文档，按需检索，不常驻上下文
├── agents/code-reviewer.md     # 子 agent
├── mcp/mcp.yaml                # MCP server（DeepWiki）
├── hooks/hooks.yaml            # 拦截 force push；grok-bot 里提醒 Node 版本
├── env/env.yaml                # 团队环境变量（明文，不要放密钥）
│
└── members/ stats/ votes/      # teamai 自动维护，不要手改
```

## 每类资源怎么用

| 资源 | 分发到业务仓库的哪里 | 能按项目/角色分 | AI 怎么用 |
|---|---|---|---|
| skills | `.claude/skills` `.codex/skills` `.cursor/skills` | ✅ 命名空间 | 启动时只加载名字和描述，用到时才加载正文 |
| rules | `.claude/rules` `.codex/rules` `.cursor/rules`（Cursor 转成 `.mdc`） | ✅ 子目录按 knowledge 命名空间过滤 | 启动时全文加载；带 `paths` 的在读到匹配文件时才加载 |
| 共享指令 claudemd | 注入 `.claude/CLAUDE.md` | ✅ | 启动时全文加载 |
| culture.md | 注入 `.claude/CLAUDE.md` | ❌ | 启动时全文加载 |
| learnings | 团队仓库里建检索索引 | ✅ 子目录 | 规则提醒模型先检索，由 `teamai-recall` 子 agent 执行 `teamai recall` |
| docs | `<项目>/.claude/team-docs/`，并建检索索引 | ❌ | 按需检索 |
| agents | `.claude/agents` `.codex/agents`（转成 `.toml`）`.cursor/agents` | ❌ | 主模型把任务委派给子 agent |
| MCP | `<项目>/.mcp.json` `.cursor/mcp.json` | ❌（写在项目里，但每个项目都会写） | 变成模型可调用的工具 |
| hooks | 用户主目录下各工具的 settings，按项目根目录自动加判断 | ❌（要分项目，在命令里自己判断 `$PWD`） | 工具在事件发生时执行；可以往上下文加文字，或用退出码 2 拦截 |
| env | 项目分区的 `env.sh`，并在 `~/.zshrc` 里 source | ❌ | 模型执行的命令和 MCP 能读到这些变量 |
| packages | 不自动安装 | ❌ | 成员执行 `teamai packages` 安装 |

各工具实际读到了什么（在 cyrax-cli 里直接问模型）：

| | Claude Code | Codex | Cursor | Grok |
|---|---|---|---|---|
| 根目录 rules | ✅ | ❌ | ✅ | ✅ |
| 子目录 rules | ✅ | ❌ | ✅ | ❌ |
| 共享指令 | ✅ | ❌ | ❌ | 部分项目可以 |
| skills | ✅ | ✅ | ✅ | ✅ |
| 子 agent | ✅ | ❌ | ✅ | ✅ |

## 成员接入

```bash
npm install -g teamai-cli
gh auth login

cd <业务仓库>
teamai init https://github.com/lunarianss/TeamCli --agent claude,codex,cursor --project <axonhub|grok-bot|cyrax>
teamai roles set backend        # 可选：选择角色
teamai tags subscribe visual    # 可选：订阅标签
```

之后打开 AI 工具时会自动同步，也可以手动执行 `teamai pull`。

## 日常贡献

| 想加的东西 | 做法 |
|---|---|
| 所有项目共用的 skill | 在业务仓库写好 `.claude/skills/<name>/`，执行 `teamai push --all --role common` |
| 项目或角色专属的 skill | `teamai push --all --role <命名空间>` |
| rules、共享指令、docs、agents、MCP、hooks | 直接改本仓库文件，走 PR |
| env | `teamai env add KEY VALUE -d "说明"`，再 `teamai push --all` |
| 踩坑经验 | 会话里运行 `/teamai-share-learnings`，或 `teamai contribute --file <文件>`（直接推 main） |
| packages | `teamai packages install <包> --npm --global`，再 `teamai push --all` |

## 已知的坑（teamai-cli 0.23.1）

1. **会改写已提交的 `AGENTS.md`**：注入共享指令时不看 `--agent` 白名单，hermes 的注入路径就是 `AGENTS.md`。接入后在项目分区的 `~/.teamai/projects/<分区>/config.yaml` 里写：
   ```yaml
   disabledAgents:
     - hermes
     - workbuddy
   ```
2. **Codex 读不到 rules 和共享指令**：只有 skills 对 Codex 生效。需要 Codex 遵守的规则，写进业务仓库自己的 `AGENTS.md`。
3. **激活项目后，`skills/` 根目录下的 skill 会被清理**：共用的 skill 放进 `skills/common/`。
4. **没有 `teamai projects` 子命令**：用 `init --project <id> --force`；如果本地已有团队仓库克隆，先 `teamai pull` 再 init。
5. **env 是全局的**：`~/.zshrc` 里只有一段，指向最后一次 pull 的项目。
6. **MCP 的 `${VAR}` 会解析成明文**：业务仓库要把 `.mcp.json` 加进 gitignore。变量没设置的 server 会被直接跳过，没有提示。
7. **hooks 写在用户主目录**：每个关联过的项目都有一份副本；需要只对某个项目生效的，在命令里判断 `$PWD`。
8. **contribute 和用量统计会直接推到 main**：团队仓库克隆里如果没设邮箱，会用全局的 git 邮箱。可以执行 `git -C ~/.teamai/projects/<分区>/team-repo config user.email <noreply 邮箱>`。
9. **recall 检索会跨项目**：learnings 是隔离的，但检索结果里会出现其他项目的 skill。
