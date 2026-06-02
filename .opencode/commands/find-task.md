---
description: 搜索已有任务 (doing/todo)
agent: plan
---

## 1. 搜索

用 grep 在 `docs/tasks/doing/task-*.md` 和 `docs/tasks/todo/task-*.md` 中搜索 `title:` 和 `summary:` 行, 匹配 `$ARGUMENTS`.

## 2. 结果展示

**有匹配** → 表格展示:
```
| # | 状态 | 优先级 | 标题 | 摘要 |
|---|------|--------|------|------|
| 1 | doing | high | task A | 一句话说明 |
| 2 | todo | med | task B | 另一说明 |
```

用 question 工具让用户选择:
- 选择某任务 → "使用 /start <task-id> 开始/继续此任务"
- 不满意 → "先创建 ? /task <desc>"

**无匹配** → 提示用户:
- "创建新任务: /task <描述>"

## 3. Session 命名

建议用户在第一轮消息中附带意图:
```
/find-task <keyword>
我想搜索 <意图描述> 相关的任务
```
这样 session 标题能捕获实际意图, 而非 "find-task".
