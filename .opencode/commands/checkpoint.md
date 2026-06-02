---
description: 随手中转保存, 快速 git 提交
agent: build
---

## 1. 检查状态

```bash
git status --short
git submodule status
```

## 2. 自动提交

```bash
git add -A
git commit -m "checkpoint: <自动生成, 含变更文件列表>"
```

message 格式: `checkpoint: <阶段> — <文件数> 个文件`

## 3. 输出

```
## Checkpoint 已保存

Commit: abc1234 — checkpoint: xx 功能 — 3 个文件
变更: src/a.js(修改), src/b.js(新增), src/c.js(修改)

继续 /build 执行, 或 /status 查看状态.
```

## 约束

- 不处理 submodule 提交 (submodule 需通过 /commit 处理)
- 不推送
- 不要求分组审查
- 如果无变更则提示 "无变更, 跳过"
