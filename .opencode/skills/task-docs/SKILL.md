---
name: task-docs
description: 任务文档全生命周期管理. 用于 open-task/close-task/update-task/todo-task/update-docs 命令.
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
branch: <task-yyyy-mm-dd-xxx>
related:
  - docs/doc-xxx.md
  - src/path/to/file.ts
---
```

## 创建任务 (open-task)

1. 描述不清时先问 1-2 个问题明确 title 和 summary
2. 在 `docs/tasks/doing/` 和 `docs/tasks/todo/` 中 grep `title:` 和 `summary:` 行查重:
   - doing 命中 → 提示已有进行中任务
   - todo 命中 → 询问是否提升为 doing
   - 无命中 → 继续
3. 创建 `docs/tasks/doing/task-yyyy-mm-dd-<主题>.md`, 填入 front matter (`status: doing`), 正文含目标/背景、约束/风险、初步执行计划
4. 在 `TODO.md` 的 `## 进行中` 区域新增条目
5. 并行读取 `related` 字段中的引用文件
6. 输出: 任务目标、涉及文件、下一步 (方案未定→/plan, 方案已定→/update-task)

## 关闭任务 (close-task)

1. 确认所有目标已达成. 未完成项先 /todo-task 或确认可接受
2. 更新 related 中**文档类引用** (doc-*.md, task-*.md) 的日期和进展描述. **不修改源码文件** (src/, packages/ 等)
3. 将任务文件从 `docs/tasks/doing/` 移至 `docs/tasks/done/`
4. 更新 `TODO.md` 的 `## 进行中` 同步移除
5. 输出: 完成摘要 (目标/变更文件) + 后续 (/update-docs → /commit 或 /open-task 下一任务)

## 写入 Plan 决议 (update-task)

1. 读取 `docs/tasks/doing/task-*.md`
2. 从会话梳理: 最终方案(一句话)、关键决策、执行步骤(每步可验证)、涉及文件、风险/待确认
3. 在任务文件中追加 `## Plan 决议`, 记录上述内容. 如有知识库更新同步 `docs/doc-*.md`
4. 输出表格确认: 任务/方案/涉及文件/执行步骤/风险. 提示 "方案已记录, 使用 /build 开始执行"

## 创建待办 (todo-task)

1. 从会话提取: 当前在做什么、延后原因、关键结论/决策、后续接手需要的信息
2. 在 `docs/tasks/todo/` 中 grep title 查重: 匹配→更新已有; 不匹配→新建
3. 创建/更新 `docs/tasks/todo/task-yyyy-mm-dd-<主题>.md`, 使用 front matter (`status: todo`), 正文含目标、来源、延后原因、阻塞/依赖、恢复条件、建议下一步
4. 在 `TODO.md` 的 `## 待办` 区域新增或更新条目
5. 输出摘要 (当前工作/延后原因/恢复条件), 继续当前任务

## 全局文档同步 (update-docs)

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

并行读取全部关联文件。
