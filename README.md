---
source-url: https://github.com/sailing3d/opencode-commands
source-branch: main
---
# opencode-commands

OpenCode 工具包仓库。提供跨项目复用的通用命令、技能和代理，通过 `x-commands-push` 和 `x-commands-pull` 同步到本地项目。

---

## Commands

| 命令 | 用途 | Agent |
|------|------|-------|
| `/open-task <desc>` | 新建任务文档 + 加载上下文 | plan |
| `/update-task` | 将 Plan 讨论决议写入任务文档 | build |
| `/close-task` | 完成任务归档到 done/ | build |
| `/todo-task <desc>` | 记录延后待办 | build |
| `/plan` | 分析需求, 讨论方案 | plan |
| `/build` | 按 Plan 决议执行, 内建 lint/typecheck 验证 | build |
| `/update-docs` | 全局文档同步 (task docs + 知识库 + TODO + AGENTS) | build |
| `/reload-docs` | 重新加载外部可能变更的文档 | plan |
| `/commit` | 分组提交 (不 push, 不 rebase) | build |
| `/push` | 处理 rebase + push (含 submodule) | build |
| `/x-commands-push` | 推送本地 toolkit 到上游 (覆盖+删除远程) | build |
| `/x-commands-pull` | 从上游拉取 toolkit 到本地 (询问后删除) | build |
| `/x-commands-init` | 初始化/更新项目约定文件 | build |

## Skills

| Skill | 用途 | 被命令引用 |
|-------|------|-----------|
| `plan-workflow` | 需求分析与方案制定工作流 | plan |
| `build-workflow` | Plan 决议逐步执行工作流 | build |
| `task-docs` | 任务文档生命周期管理 | open-task, close-task, update-task, todo-task, update-docs |
| `git-check` | Git 检查、分组提交、推送流程 | commit, push |

## Agents

| Agent | 用途 | 位置 |
|-------|------|------|
| `reviewer-pro` | 独立代码审查与审计 | 全局 `~/.opencode/agents/` |

## 快速部署

复制以下内容到 opencode 会话中：

```
阅读并执行 https://raw.githubusercontent.com/sailing3d/opencode-commands/main/README.md 的 "Agent 引导" 章节。
```

部署完成后运行 `/x-commands-init` 完成项目初始化，即可使用全部命令。

## Agent 引导

1. 源仓库: `sailing3d/opencode-commands`, 分支 `main`
2. 调用 GitHub API 获取以下文件和目录：
   - **本文件**: 直接下载 raw 版 `README.md` → 保存到 `.opencode/x-commands-README.md`
   - **commands**: `contents/.opencode/commands` → 遍历每个 `.md`，用 `download_url` 下载 → 保存到 `.opencode/commands/<name>.md`
   - **agents**: `contents/.opencode/agents` → 同上 → `.opencode/agents/<name>.md`
   - **skills**: `contents/.opencode/skills` → 返回子目录列表。对每个子目录 `<name>`，再调 `contents/.opencode/skills/<name>/` 找到 `SKILL.md`，用 `download_url` 下载 → 保存到 `.opencode/skills/<name>/SKILL.md`
3. 用表格输出所有下载的文件
4. 提示: "Session restart 后生效。重启后请运行 /x-commands-init 完成项目初始化"

## 贡献

```
/x-commands-push
```

不直接推送时，fork 仓库后提交 PR。改动涉及：
- 新增/修改命令 → `.opencode/commands/<name>.md`
- 新增/修改技能 → `.opencode/skills/<name>/SKILL.md`
- 新增/修改代理 → `.opencode/agents/<name>.md`
- 更新本文件（source-url 和 source-branch 在 front matter 中）

### 拉取更新

```
/x-commands-pull
```
