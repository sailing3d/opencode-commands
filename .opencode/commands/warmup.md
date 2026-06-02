---
description: 项目全盘扫描, 呈现状态, 选择任务
agent: plan
subtask: false
---

`$ARGUMENTS`:
- 空 / "deep" — 完整扫描 (TODO, 进行中, 待办, 知识库, 包文档)
- "quick" / "simple" — 快速扫描, 仅读 TODO.md + docs/tasks/doing/

> 如需搜索任务, 请使用 `/find-task <keyword>`.

## 阶段 1: 项目状态扫描

用 explore agent (thoroughness: "medium") 批量读取以下文档, 返回结构化摘要:

**完整扫描** ($ARGUMENTS 为空或 "deep"):
- `TODO.md` — 全局待办概览
- `docs/tasks/doing/task-*.md` — 当前进行中的任务 (所有)
- `docs/tasks/todo/task-*.md` — 待处理任务 (所有)
- `docs/doc-*.md` — 所有知识库文档
- `packages/**/README.md` — 自研包文档

**快速扫描** ($ARGUMENTS 为 "quick" 或 "simple"):
- `TODO.md` — 全局待办概览
- `docs/tasks/doing/task-*.md` — 当前进行中的任务 (所有)

**以上两种模式都不读取**:
- `docs/tasks/done/` — 已完成的任务
- `docs/legacy/` — 过时的参考文档
- 代码文件 — 此阶段仅通过文档了解状态

agent 返回: 每个任务文件的 title, status, summary (从 YAML front matter 提取), 以及关键约定 (代码修改边界, 避坑要点).

## 阶段 2: 呈现状态 + 任务选择

收到阶段 1 结果后, 用表格呈现给用户:

### 当前进行中

| #                            | 任务 | 概要 |
| ---------------------------- | ---- | ---- |
| (if empty -> "无进行中任务") |      |      |

### 待办

| #   | 优先级 | 任务 | 概要 |
| --- | ------ | ---- | ---- |

然后调用 `question` 工具, 让用户选择要继续的任务:

- header: "选择任务"
- options: 每条任务一个选项 (label: 任务简称, description: 一句话概要)
- 额外选项: "只查看状态 (不选任务)"

## 阶段 3: 深度准备选中的任务 (仅 "deep" 模式)

用户选择具体任务后 (quick 模式在阶段 2 输出后直接结束):

1. **读取任务文件** — 完整内容
2. **解析 `related` 字段** — 从 YAML front matter 提取 `related` 列表
3. **读取关联文件** — 对每个 related 引用, 解析为实际路径:
   - `task-yyyy-mm-dd-xxx` → `docs/tasks/doing/task-*.md` 或 `docs/tasks/todo/task-*.md` (glob 匹配)
   - `doc-xxx` → `docs/doc-xxx*.md` (glob 匹配)
   - 直接路径 (以 `src/` 或 `packages/` 开头) → 直接读取
   - 并行读取全部关联文件
4. **输出任务摘要:**
   - 任务目标 (一句话)
   - 当前状态 + 阻塞项/待排查项
   - 关联文件清单 (已读取)
   - 关键代码路径 (来自 related 字段中的 `src/` 路径)
5. 建议用户将当前 Session 重命名为 <任务简称> 以便后续管理

## 阶段 4: 确认启动

输出任务摘要后, 询问用户:

- "是否开始此任务?"
- 或提出澄清性问题 (如果任务描述中有未决事项)
- 或询问用户想从哪个具体方面开始 (如果任务有多个子项)
