---
description: 完成任务, 归档到 done/
agent: build
---

## 1. 确认完成

确认任务所有目标已达成. 如有未完成项, 提示用户:
- 先创建 /todo 记录延后项
- 或确认哪些可以接受作为后续任务

## 2. 执行 sync-docs

调用 `/sync-docs` 的步骤: 更新 related 文件, 日期, 进展描述.

## 3. 建议提交

提示用户: "建议先使用 /commit 提交代码, 或使用 /checkpoint 中转保存."

## 4. 移动文件

将任务文件从 `docs/tasks/doing/` 移至 `docs/tasks/done/`.
更新 `TODO.md` 同步变更.

## 5. 输出

```
## 任务已关闭 — task-yyyy-mm-dd-xxx

### 完成摘要
- 目标: <一句话>
- 变更文件: <列表>
- 提交: <commit hash> (如已执行)

### 后续建议
- 执行 /handoff 交接以开启新 session
- 或用 /task 或 /start 开始下一任务
```
