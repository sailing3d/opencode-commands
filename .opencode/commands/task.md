---
description: 新建任务文档, 初始化上下文
agent: build
---

## 1. 澄清任务

如果用户描述不清, 先问 1-2 个问题明确 title 和 summary.

## 2. 查重

用 grep 搜索 `docs/tasks/doing/task-*.md` 和 `docs/tasks/todo/task-*.md` 的 `title:` 行.

- **命中 doing** → 提示用户 "此任务已在进行中: <title>. 是否用 `/start <task-id>` 继续?"
- **命中 todo** → 提示用户 "已有待办: <title>, 是否提升为 doing? 是: 移至 doing/ 并 /start; 否: 继续建新"

## 3. 创建任务文件

`docs/tasks/doing/task-yyyy-mm-dd-<主题>.md`

标准 front matter:
```yaml
---
title: <任务标题>
created: <yyyy-mm-dd>
date: <yyyy-mm-dd>
summary: <一句话说明>
priority: <high | medium | low>
status: doing
branch: <task-yyyy-mm-dd-xxx>
related:
  - docs/doc-xxx.md
  - src/path/to/file.ts
---
```

正文至少包含: 目标/背景、已知约束/风险、初步执行计划.

## 4. 同步 TODO.md

在 `TODO.md` 的 `## 进行中` 区域新增条目.

## 5. 加载关联文档

读取 `related` 字段中的引用, 并行加载.

## 6. 输出确认

```
## 任务已创建 — task-yyyy-mm-dd-xxx

### 目标
一句话说明

### 涉及文件
- docs/doc-xxx.md — 已加载
- src/foo.js — 已加载

### 下一步
- 方案未定 → 使用 /plan 讨论方案
- 方案已明确 → 使用 /design 固化方案
- 已有 task 可继续 → 使用 /start <task-id>
```

## 7. Session 命名

此命令作为新 session 首轮时, 描述会生成有意义的 session 标题.
