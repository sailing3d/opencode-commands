---
description: 加载已有任务上下文, 输出摘要, 准备开始
agent: plan
---

## 1. 调用方式

通过 `/start <task-id>` 或用户选择任务后自然进入. task-id 可以是:
- `task-yyyy-mm-dd-xxx` 完整文件名
- `yyyy-mm-dd-xxx` 省略前缀
- 关键词模糊匹配 (唯一命中时直接进入, 多命中则列出让用户选)

## 2. 读取任务文件

`read docs/tasks/doing/task-*.md` 完整内容.

## 3. 解析关联文件

从 YAML front matter 提取 `related` 字段, 解析为实际路径:
- `doc-xxx` → `docs/doc-xxx*.md` (glob)
- `task-xxx` → `docs/tasks/*/task-*.md` (glob)
- `src/` / `packages/` 开头 → 直接读取

并行读取全部关联文件.

## 4. 输出摘要

```
## 任务摘要 — task-yyyy-mm-dd-xxx

### 目标
一句话说明

### 当前状态
- 进度描述 (从 task doc 正文提取)
- 阻塞项/待排查项
- 最近进展

### 关键文件
- src/foo.js — 已加载
- packages/bar/src/index.ts — 已加载

### 下一步建议
- 方案未定 → /plan 讨论方案
- 方案已定 → /design 固化方案
- 已有 Plan 决议 → /build 继续执行
```

## 5. Session 命名

此命令作为新 session 首轮时, 任务标题会生成有意义的 session 标题.
