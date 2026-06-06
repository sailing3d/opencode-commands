---
description: 从上游仓库同步 toolkit 到本地, 远程有而本地无的文件需询问后删除
agent: build
subtask: true
---

同步方向: **远程 → 本地**。远程文件覆盖本地。远程有而本地无的**先询问用户**再删除。

1. 用 Read 工具读取 `.opencode/x-commands-README.md` 的 YAML front matter, 提取 `source-url` 和 `source-branch`
2. 从 `source-url` 解析 owner/repo, 调 GitHub API 获取远程文件列表:
   - `contents/.opencode/commands` → 返回远程所有 `.md` 文件名
   - `contents/.opencode/agents` → 同上
   - `contents/.opencode/skills` → 返回子目录列表
3. 对比本地文件:
   - **远程有、本地无** → question 询问 "远程有 xxx, 是否删除本地同名文件?" 确认后删除
   - **远程有、本地有、内容同** → 跳过
   - **远程有、本地有、内容不同** → question 询问 "远程和本地 xxx 不同, 是否覆盖?" 确认后覆盖
   - **本地有、远程无** → 保留 (不影响)
4. 下载远程文件覆盖本地:
   - `commands`: 每个 `.md` 的 `download_url` → webfetch 下载 → Write 到 `.opencode/commands/<name>.md`
   - `agents`: 同上 → `.opencode/agents/<name>.md`
   - `skills`: 对每个远程子目录名 `<name>`，再调 `contents/.opencode/skills/<name>/` 找到 `SKILL.md` → 下载 → Write 到 `.opencode/skills/<name>/SKILL.md`
   - `raw.githubusercontent.com/{owner}/{repo}/{branch}/README.md` → webfetch 下载 → Write 到 `.opencode/x-commands-README.md`
5. 输出摘要, 提示 "Session restart 后生效, 然后运行 /x-commands-init"
