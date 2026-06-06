---
name: build-workflow
description: 按 Plan 决议逐步执行代码的工作流. 用于 /build 命令.
---

## 1. 读取 Plan 决议

读取当前任务文件的 `## Plan 决议`. 无决议→提示 /plan → /update-task.

## 2. 按步骤执行

每步: 读文件 → 编辑/写入 → 自检 → 运行 lint/typecheck 验证.

## 3. 遇到阻塞

分析原因, question 询问:
- 返回 Plan → 重新分析方案
- 跳过此步 → 记录原因, 继续下一步
- 调整方案 → 记录调整, 建议 /update-task 更新任务文档

## 4. 完成

输出摘要: 已完成步骤 + 变更文件 + 建议 /commit.
