---
description: 建立新的进行中或 TODO 任务文件
agent: build
---

根据上下文判断任务类型:
- **进行中任务 (doing)** — 用户正在处理, 需要跨轮次协作或审计决策
- **TODO 任务 (todo)** — 当前无法立即处理, 需要延后记录

## 1. 匹配已有任务

用 explore agent 扫描 `TODO.md` 和 `tasks/todo/task-*.md` / `tasks/doing/task-*.md` 的标题, 检查是否有与当前问题相似的任务:

- 匹配: 更新已有 task 文件, 补充新信息, 同步更新 `TODO.md`, 结束
- 不匹配: 进入步骤 2

## 2. 创建新任务

在 `tasks/<doing|todo>/` 下创建, 命名: `task-yyyy-mm-dd-<主题>.md`.
Agent 根据问题描述自行决定文件名, title, summary.

使用以下 front matter 模板:

```yaml
---
title: <任务标题>
created: <yyyy-mm-dd>
date: <yyyy-mm-dd>
summary: <一句话说明核心内容>
priority: <high | medium | low>
status: <doing | todo>
branch: <task-yyyy-mm-dd-xxx>

# 可选
related:
  - docs/doc-xxx.md
  - tasks/doing/task-xxx.md
  - src/path/to/file.ts
---
```

## 3. 补全任务内容

至少包含以下内容:

- 任务目标 / 背景
- 当前现象或待处理事项
- 已知约束 / 风险
- 执行计划 / 下一步
- 如有必要, 添加 `related` 字段关联相关 task / doc / 代码路径

## 4. 同步 TODO.md (仅 todo 任务)

更新 `TODO.md` 中对应条目或新增条目, 保持优先级排序.
