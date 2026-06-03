---
description: 从上游模板仓库同步命令文件与 agent 文件
agent: build
---

$ARGUMENTS:
- 空 / "fetch" — 从上游拉取更新 (默认)
- "push"       — 将本地 commands 和 agents 推回上游

源信息存于 `.opencode/.bootstrap-source`.

---

## Fetch (拉取)

### 1. 读取源配置

读取 `.opencode/.bootstrap-source`, 提取 `url` 和 `branch`.
文件不存在则提示用户先完成引导部署.

### 2. 获取上游文件列表

分别调用两个 GitHub API:
```
# 命令文件
https://api.github.com/repos/<owner>/<repo>/contents/.opencode/commands
# agent 文件
https://api.github.com/repos/<owner>/<repo>/contents/.opencode/agents
```
解析返回 JSON, 记录 `name`, `sha`, `download_url`.

### 3. 对比本地

对每个上游文件 (commands + agents):
- **本地缺失** → 标记为 **新增**
- **本地存在, 内容相同** → 标记为 **跳过**
- **本地存在, 内容不同** → 标记为 **冲突**

### 4. 处理冲突

冲突列表用 question 逐文件询问: "覆盖 / 跳过".

### 5. 执行下载

新增 + 用户确认覆盖的文件: webfetch download_url → write 到本地 (commands 写入 `.opencode/commands/`, agents 写入 `.opencode/agents/`).

### 6. 输出摘要

```
## x-commands fetch 结果

| 操作 | 文件 | 说明 |
|------|------|------|
| 新增 | review.md | — |
| 更新 | build.md | 本地不同已覆盖 |
| 跳过 | plan.md | 内容一致 |
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

### 2. 确定目标仓库 + 分支

用 question 提供选项:
- **A. 上游仓库** (需要写权限)
- **B. 输入你的仓库**

### 3. Clone → 同步 → Commit → Push

```bash
gh repo clone <owner/repo> "$env:TEMP\opencode-commands-sync" -- --branch <branch>
# 同步命令文件
Copy-Item -Path ".opencode/commands/*.md" -Destination "$env:TEMP\opencode-commands-sync\.opencode\commands\" -Force
# 同步 agent 文件
Copy-Item -Path ".opencode/agents/*.md" -Destination "$env:TEMP\opencode-commands-sync\.opencode\agents\" -Force
# cd → git add → git commit → git push
```

### 4. 清理

```bash
Remove-Item "$env:TEMP\opencode-commands-sync" -Recurse -Force
```

### 5. Push 错误处理

push 因权限失败时 (403):
- 提示用户 fork → `gh repo fork <owner>/<repo> --clone=false`
- 重新执行 push 选择新 fork 地址
