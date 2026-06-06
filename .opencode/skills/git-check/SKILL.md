---
name: git-check
description: Git 状态检查、submodule 规则、分组提交与推送流程. 用于 /commit、/push 命令.
---

## 标准检查序列

```
git branch --show-current
git status --short
git submodule status
```

需要差异时追加 `git diff --submodule`.

## Submodule 状态码

| 前缀 | 含义 | 处理 |
|------|------|------|
| `+` | 有本地新提交未推送 | 推送前先推送 submodule |
| `-` | 落后上游 (缺提交) | 提示用户, 可选先更新 |
| `M` | 有未提交的脏修改 | 先进入 submodule 处理再继续 |
| (空格) | 干净 | 无需处理 |

## 大文件检查

`git diff --cached --stat` 中 >10MiB 文件需确认是否排除或用 LFS 管理.

## 分组提交流程 (commit)

1. 用 todo 工具组织步骤
2. 运行标准检查序列 + `git diff --submodule`
3. 文件分组: 按改动内容分为独立逻辑组, 归属疑问标记 "需用户确认", 无关文件建议跳过
4. 生成简报表格: submodule 变更、建议分组(组/文件/commit message)、待确认项、建议跳过项
5. question 逐条询问待确认项, 用户可调整分组或排除文件
6. 执行: 先 submodule (进入目录 `git status` → 确认分支 → `git add` → `git commit`), 再按分组提交主仓库
7. 完成后提示: "如需推送到远端, 使用 /push"

注意事项:
- 每个 commit 是独立逻辑单元, 不过度拆分/合并
- 先 submodule 后主仓库, 避免主仓库指向未推送的 submodule commit
- 禁止 `git reset --hard` 等丢失改动的操作

## 推送流程 (push)

1. 用 todo 工具组织步骤
2. 运行标准检查序列, 外加:
   - 无上游跟踪: 询问 `git push --set-upstream origin <branch>`
   - 有未提交文件: 提示先 /commit
   - 上游差异: `git fetch origin {branch}` → `git log HEAD..@{u}`, 有新提交则 question 询问 rebase(推荐)/merge/取消
   - 大文件 >10MiB: 询问确认
   - rebase/merge 冲突: 提示手动解决
3. Submodule 推送: 对每个 `+` 前缀 submodule 逐条 question 询问, 确认后 `git push origin {branch}`
4. 主仓库推送: 展示推送清单, question 二次确认. `--force` 额外确认. 执行 `git push origin {branch}`
5. 结果: 表格列出 submodule 和主仓库推送结果
