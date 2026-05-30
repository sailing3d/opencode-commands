# opencode-commands

OpenCode commands 的官方仓库。提供项目无关的通用开发辅助命令。

## Commands 一览

| 命令 | 用途 |
|------|------|
| `sailing3d-commands` | 从本仓库同步 / 向本仓库推送 commands |
| `check` | 运行 lint / typecheck / compile 验证 |
| `commit` | 检查改动、分组、提交并推送 |
| `plan` | 分析需求、扫描影响、制定方案、确认后进入 Build |
| `plan-to-build` | 将 Plan 方案写入任务文档，用户确认后进入 Build |
| `warmup` | 预读项目文档和 TODO，快速了解状态 (deep/quick 模式) |
| `status` | 快速查看工作区状态 (分支、git、submodule、任务) |
| `handoff` | 会话交接：同步文档 + 生成上下文摘要 |
| `new-task` | 建立新的进行中或 TODO 任务文件 |
| `close-task` | 关闭当前任务，归档文件，更新 CHANGELOG |
| `sync-docs` | 更新任务相关文档和 TODO 索引 |

## 快速部署

复制以下内容到 opencode 会话中：

```
阅读并执行 https://raw.githubusercontent.com/sailing3d/opencode-commands/main/README.md 中 "Agent 引导" 章节的内容
```

部署后即可使用 `sailing3d-commands fetch` 拉取更新。

## Agent 引导

1. 源仓库: `sailing3d/opencode-commands`, 分支 `main`
2. 调用 GitHub API 获取 `https://api.github.com/repos/sailing3d/opencode-commands/contents/.opencode/commands` 的目录列表
3. 对返回的每个 `.md` 文件, 用 `download_url` 下载原始内容
4. 逐个保存到本地 `.opencode/commands/` 目录
5. 在 `.opencode/` 下创建 `.bootstrap-source`, 内容为:
   ```
   url=https://github.com/sailing3d/opencode-commands
   branch=main
   ```
6. 完成后用表格列出所有命令文件及用途 (从 YAML `description` 提取)
7. 提示: "Session restart 后新 commands 生效"

## 贡献

```bash
sailing3d-commands push
```

将本地 commands 推回本仓库，或用 fork 提交 PR。
