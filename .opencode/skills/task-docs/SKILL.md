---
name: task-docs
description: 任务文档全生命周期管理. 用于 open-task/close-task/update-task/todo-task/sync-docs 命令.
---

## Front Matter 模板

```yaml
---
title: <任务标题>
created: <yyyy-mm-dd>
date: <yyyy-mm-dd>
summary: <一句话说明>
priority: <high | medium | low>
status: <todo | doing | done>
needs-investigation: true   # todo-task 快速记录标记, 待调查后完善
branch: <task-yyyy-mm-dd-xxx>
related:
  - docs/doc-xxx.md
  - src/path/to/file.ts
---
```

## 打开/开始任务 (open-task)

1. 如果 `$ARGUMENTS` 为空:
   - 列出 `doing/` 和 `todo/` 中所有任务, question 让用户选择或新建
2. 如果 `$ARGUMENTS` 是关键词:
   - 在 `doing/`、`todo/`、`done/` 中 grep `title:` 和 `summary:` 行匹配
   - doing 命中 -> "已在进行中, 是否继续?"
   - todo 命中 -> "有待办, 是否提升为 doing 开始?"
   - done 命中 -> "已完成, 是否重开?"(确认后移回 doing/ 并更新 date)
   - 无命中 -> 创建新任务
3. 如果 `$ARGUMENTS` 是 task-id: 直接加载, 无论当前 status
4. **调查检查**: 如果加载的任务 front matter 含 `needs-investigation: true`:
   - 提示用户 "此任务标记了待调查, 是否现在进行调查?"
   - 确认后按 **调查标记任务** 规则执行
   - 不确认则仅加载, 不做调查
5. 新建任务流程:
   - 描述不清时先问 1-2 个问题明确 title 和 summary
   - 创建 `docs/tasks/doing/task-yyyy-mm-dd-<主题>.md`, 填入 front matter (`status: doing`), 正文含目标/背景、约束/风险、初步执行计划
   - 在 `TODO.md` 的 `## 进行中` 区域新增条目
   - 并行读取 `related` 中的全部关联文件 (含代码), 了解背景和现状
   - 如果发现问题或异常, 记录下来, 但**不讨论方案** — 留给 /plan
6. 输出: 任务目标、涉及文件、下一步 (方案未定->/plan, 方案已定->/update-task)

## 调查标记任务

当任务 front matter 含 `needs-investigation: true` 时调用 (供 open-task/warmup 共用):
- 读取 related 中全部关联文件 (含代码), 了解背景和现状
- 删除正文中的 `## 待调查` 章节, 将发现写入正文
- 将 `needs-investigation` 设为 `false`, `status` 改为 `doing`
- 更新 TODO.md (从 todo 移至 doing)
- **不讨论方案** (留给 /plan)

## 关闭任务 (close-task)

1. 确认所有目标已达成. 未完成项先 /todo-task 或确认可接受
2. 更新 related 中**文档类引用** (doc-*.md, task-*.md) 的日期和进展描述. **不修改源码文件** (src/, packages/ 等)
3. 将任务文件从 `docs/tasks/doing/` 移至 `docs/tasks/done/`
4. 更新 `TODO.md` 的 `## 进行中` 同步移除
5. 输出: 完成摘要 (目标/变更文件) + 后续 (/sync-docs -> /commit 或 /open-task 下一任务)

## 更新任务文档 (update-task)

1. 扫描 `docs/tasks/doing/task-*.md` 获取进行中任务列表
2. 如果只有一个进行中任务 -> 默认更新它
3. 如果有多个 -> question 询问更新哪个或是否全部更新
4. 如果没有 -> question "没有进行中任务, 是否创建一个?" 是则跳到打开/开始任务流程
5. 从会话中提取需要记录的内容:
   - 有方案讨论 -> 追加/更新 `## Plan 决议` (最终方案/关键决策/步骤/文件/风险)
   - 有执行进展 -> 追加/更新 `## 进展` (已完成/进行中/阻塞)
   - 有决策记录 -> 追加/更新 `## 决策记录` (时间/决策/理由)
   - 有风险或问题 -> 追加/更新 `## 风险/问题`
   - 无明确分类但有关键信息 -> 更新正文相关段落
   - 至少更新 date 字段为当天
6. 输出: 更新摘要 (文件/新增或修改的章节内容) + 下一步建议

## 创建待办 (todo-task)

快速记录, 不做深度调查.

1. 在 `docs/tasks/todo/` 中 grep title 查重: 匹配->更新已有; 不匹配->新建
2. 创建/更新 `docs/tasks/todo/task-yyyy-mm-dd-<主题>.md`, 使用 front matter (`status: todo`, `needs-investigation: true`), 正文简要说明延后原因并追加 `## 待调查` 章节
3. 在 `TODO.md` 的 `## 待办` 区域新增或更新条目

## 全局文档同步 (sync-docs)

1. 读取当前会话涉及的任务文件
2. 遍历 related 中的 doc-*.md, 检查反向引用和描述
3. 检查内容相关的公共知识文档是否需要更新
4. 更新 TODO.md: 扫描 `docs/tasks/doing/` 和 `docs/tasks/todo/` 重建 `## 进行中` 和 `## 待办` 索引
5. 检查 AGENTS.md 是否需要更新文档引用或注意事项
6. 输出变更摘要, 提示 /commit

## related 字段解析

从 YAML front matter 提取 `related` 列表, 按前缀解析:

| 前缀 | 解析方式 | 示例 |
|------|---------|------|
| `doc-*` | glob `docs/doc-*.md` | `docs/doc-foo.md` |
| `task-*` | glob `docs/tasks/*/task-*.md` | `docs/tasks/doing/task-foo.md` |
| `src/` / `packages/` | 直接读取路径 | `src/foo.ts` |

并行读取全部关联文件.
