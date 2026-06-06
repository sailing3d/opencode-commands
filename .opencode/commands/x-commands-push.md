---
description: 将本地 toolkit 推送到上游仓库, 覆盖远程文件, 删除远程有而本地无的文件
agent: build
subtask: true
---

同步方向: **本地 → 远程**。本地文件覆盖远程，远程有而本地无的**删除**。不要反向同步。

默认目标仓库: `sailing3d/opencode-commands`（来自 `.opencode/x-commands-README.md` front matter 的 `source-url`）。

1. 用 bash 执行 `gh auth status` 确认 `gh` 可用
2. question 选目标: 默认上游仓库 `sailing3d/opencode-commands` / 自定义仓库
3. 用 bash 执行 `gh repo clone {target} "$env:TEMP\opencode-commands-sync"` 克隆到系统临时目录
4. 用 bash 执行文件同步:
   - 删除克隆的 `.opencode/commands/` 下所有 `.md`
   - 复制本地 `.opencode/commands/*.md` → 克隆 `.opencode/commands/`
   - 删除克隆的 `.opencode/skills/` 下所有内容
   - 复制本地 `.opencode/skills/` → 克隆 `.opencode/skills/` (递归)
   - 删除克隆的 `.opencode/agents/` 下所有 `.md`
   - 复制本地 `.opencode/x-commands-README.md` → 克隆根 `README.md`
5. 进入克隆目录: `git add . && git commit -m "sync: 更新 toolkit" && git push origin {branch}`
6. 提示手动清理 clone 目录
7. 403 → fork 提示; `gh` 不可用 → 手动 fork + PR 提示
