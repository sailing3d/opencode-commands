---
description: 记录延后待办, 继续当前工作
agent: build
---

## 1. 抽取上下文

从会话中提取:
- 当前在做什么
- 为什么需要延后
- 关键结论/决策
- 后续接手需要知道什么

## 2. 查重

搜索 `docs/tasks/todo/task-*.md` title 是否匹配:
- **匹配** → 更新已有文件, 跳到步骤 4
- **不匹配** → 步骤 3

## 3. 创建待办文件

`docs/tasks/todo/task-yyyy-mm-dd-<主题>.md`

标准 front matter (`status: todo`):
```yaml
---
title: <任务标题>
created: <yyyy-mm-dd>
date: <yyyy-mm-dd>
summary: <含延后原因的一句话说明>
priority: <high | medium | low>
status: todo
branch: <task-yyyy-mm-dd-xxx>
related:
  - docs/tasks/doing/task-xxx.md
  - docs/doc-xxx.md
---
```

正文记录: 目标、来源、延后原因、阻塞/依赖、恢复条件、建议下一步.

## 4. 同步 TODO.md

在 `TODO.md` 的 `## 待办` 区域新增或更新条目.

## 5. 输出确认

```
## 已记录待办 — task-yyyy-mm-dd-xxx

### 记录内容
- 当前工作: 正在处理 xx
- 延后原因: 依赖 xx 尚未就绪
- 恢复条件: xx 完成后再做

### 当前工作继续
继续处理之前的任务.
```
