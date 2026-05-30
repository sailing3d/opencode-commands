---
description: 从 sailing3d/opencode-commands 同步本地 commands
agent: build
---

$ARGUMENTS:
- 空 / "fetch" — 从上游拉取更新 (默认)
- "push"       — 将本地 commands 推回上游

首次使用前, 须先完成引导 (见仓库 README.md 快速部署).
源信息存于 `.opencode/.bootstrap-source`.

---

## Fetch (拉取)

### 1. 读取源配置

读取 `.opencode/.bootstrap-source`, 提取 `url` 和 `branch`.
文件不存在则提示用户先完成引导部署.

### 2. 获取上游文件列表

用 webfetch (format: text) 调用:

```
https://api.github.com/repos/sailing3d/opencode-commands/contents/.opencode/commands
```

解析返回 JSON, 对每个条目记录 `name`, `sha` (blob SHA), `download_url`.

### 3. 对比本地

对每个上游文件:

- **本地缺失** → 标记为 **新增**
- **本地存在, 内容相同** → 标记为 **跳过**
  - 内容比较: webfetch download_url 获取上游内容, read 本地文件, 字符串对比
- **本地存在, 内容不同** → 标记为 **冲突**

### 4. 处理冲突

冲突列表用 question 逐文件询问: "覆盖 / 跳过".

### 5. 执行下载

新增 + 用户确认覆盖的文件:
webfetch download_url → write 到 `.opencode/commands/<filename>`

### 6. 输出摘要

```
## sailing3d-commands fetch 结果

| 操作 | 文件 | 说明 |
|------|------|------|
| 新增 | sailing3d-commands.md | — |
| 更新 | warmup.md | 本地不同已覆盖 |
| 跳过 | commit.md | 内容一致 |
```

### 7. 重启提示

**"Session restart 后新 commands 生效。请重启当前会话。"**

---

## Push (推送)

### 1. 检查 gh

```bash
gh --version
gh auth status
```

未安装或未登录则报错中止, 提示先安装 GitHub CLI 并执行 `gh auth login`.

### 2. 确定目标仓库

用 question 提供选项:

- **A. `sailing3d/opencode-commands`** (需要写权限)
- **B. 输入你的仓库** → 用户输入完整 URL

### 3. 确定目标分支

```bash
# 如果选 A:
gh api repos/sailing3d/opencode-commands  --jq ".default_branch"
# 如果选 B:
gh repo view <owner/repo> --json defaultBranch --jq ".defaultBranch"
```

让用户确认或指定其他分支.

### 3. Clone

```bash
gh repo clone <owner/repo> "$env:TEMP\opencode-commands-sync" -- --branch <branch>
```

### 4. 同步文件

```bash
Copy-Item -Path ".opencode/commands/*.md" -Destination "$env:TEMP\opencode-commands-sync\.opencode\commands\" -Force
```

### 5. Commit + Push

```bash
Set-Location "$env:TEMP\opencode-commands-sync"
git add -A
git commit -m "sync(commands): 更新 <N> 个命令文件"
git push origin <branch>
```

### 6. 清理

```bash
Remove-Item "$env:TEMP\opencode-commands-sync" -Recurse -Force
```

### 7. 完成

```
## push 结果

推送到: sailing3d/opencode-commands (main)
Commit: https://github.com/sailing3d/opencode-commands/commit/<hash>
文件: warmup.md, new-task.md, sync-docs.md
```

### Push 错误处理

push 到 `sailing3d/opencode-commands` 因权限失败时 (403):

- 提示用户: "你没有该仓库的写权限。建议先 fork 到个人账户, 再用 push 选择你的 fork。"
- 如果用户选择 fork, 引导: `gh repo fork sailing3d/opencode-commands --clone=false`
- fork 后让用户重新执行 push 并选择新 fork 地址
