---
description: 初始化/更新项目约定文件, 固化工作流
agent: build
---

将 plan-design-build + 任务文档化 工作流协定, 写入项目文件.
首次运行执行完整生成, 已存在项目则增量更新.

## 1. 检测项目状态

检查以下文件是否存在:
- `AGENTS.md`
- `opencode.json`
- `docs/tasks/doing/` 目录
- `docs/instructions/dev-conventions.md`
- `TODO.md`

按现有状态分类: **全新** / **已初始化** / **需升级**.

## 2. 生成/更新 opencode.json

如果缺失或缺少 agent 配置, 生成模板:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "docs/instructions/*.md",
    "AGENTS.md"
  ]
}
```
保留原有 `instructions` 配置, 只补充缺失项.

## 3. 生成日程/任务目录结构

创建 `docs/tasks/doing/`, `docs/tasks/todo/`, `docs/tasks/done/` 目录.

创建 `TODO.md` 骨架:
```markdown
# TODO

## 进行中

(无进行中任务)

## 待办

(无待办)
```

## 4. 检查 AGENTS.md

**新增项目** — 生成 AGENTS.md 并写入工作流约定:
- Plan/Design/Build 流程说明
- Agent 权限边界 (plan 只读, build 读写)
- Session 命名建议
- 主要命令速查表

**已有项目** — 读取后检查是否包含工作流段落:
- 缺失 → 追加约定段落 (不覆盖已有内容)
- 已有 → 对比更新命令列表

## 5. 检查 instructions

检查 `docs/instructions/dev-conventions.md` 是否存在. 缺失则生成基础模板,
包含: 安全边界、代码修改边界、文档沉淀要求.

## 6. 输出变更摘要

```
## /x-init 完成

| 文件 | 操作 | 说明 |
|------|------|------|
| opencode.json | 更新 | 补充 agent 配置 |
| AGENTS.md | 更新 | 追加工作流约定 |
| docs/tasks/doing/ | 已存在 | 跳过 |
| TODO.md | 已存在 | 跳过 |
| docs/instructions/dev-conventions.md | 新增 | 基础模板 |

### 下一步
- 检查生成的 AGENTS.md 是否符合项目需求
- 使用 /task "第一个任务" 开始工作
```
