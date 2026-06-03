---
description: 命令索引, 查看所有命令及用法
agent: plan
---

# 命令索引

## 每日工作流 (plan-design-build)

| 命令 | 用途 | Agent | 写文件 | 适用阶段 |
|------|------|-------|--------|---------|
| `/task <desc>` | 新建任务文档 + 加载上下文 | build | 是 | 启动/首轮 |
| `/start <task-id>` | 加载已有任务上下文 | plan | 否 | 启动/首轮 |
| `/warmup [deep\|quick]` | 项目全盘扫描, 选择任务 | plan | 否 | 启动 |
| `/find-task <keyword>` | 搜索已有任务 | plan | 否 | 启动 |
| `/todo <desc>` | 记录延后待办 | build | 是 | 启动 |
| `/plan` | 分析需求, 讨论方案 | plan | 否 | Plan |
| `/design` | 方案写入文档, 锁定进入 Build | build | 是 | Plan→Build |
| `/build` | 按 Plan 决议逐步执行代码 | build | 是 | Build |
| `/check` | 静态验证 (lint/typecheck/compile) | build | 是* | Build |
| `/review` | 代码审查 (约定合规性) | plan | 否 | Build |
| `/checkpoint` | 随手中转提交 (WIP) | build | 是 | Build |
| `/commit` | 正式分组提交 + 推送 | build | 是 | 收尾 |
| `/sync-docs` | 同步关联文档 | build | 是 | 收尾 |
| `/close` | 完成任务归档到 done/ | build | 是 | 收尾 |
| `/handoff` | 暂停交接 (保留 doing) | build | 是 | 收尾 |

*`/check` 可能执行 `lint --fix` 修改文件.

## 系统命令

| 命令 | 用途 | Agent | 写文件 |
|------|------|-------|--------|
| `/x-help` | 命令索引 (当前文档) | plan | 否 |
| `/x-status` | 工作区 git 状态 + 进行中任务 | build | 否 |
| `/x-commands` | 同步上游命令模板 (fetch/push) | build | 是 |
| `/x-init` | 初始化/更新项目约定文件 | build | 是 |

## Agent 权限边界

| Agent | 权限 | 适用操作 |
|-------|------|---------|
| **Plan** | 只读 | 讨论、搜索、分析、审查、加载上下文 |
| **Build** | 读写 | 文档写入、代码编辑、命令执行、git 操作 |
| **reviewer-pro** | subagent (只读) | 独立代码审查 + 审计, 绑定 DeepSeek V4 Pro |

使用 reviewer-pro:
```
subagent_type: "reviewer-pro"
```
在 Task 中指定此 subagent 即可独立审查当前任务变更或审计历史代码质量。

## Session 命名约定

新 session **首轮命令**决定自动生成的 session 标题:

| 首轮命令 | 标题效果 | 建议 |
|----------|---------|------|
| `/task <desc>` | 以 desc 为标题 | 推荐新建任务 |
| `/start <task-id>` | 以任务 title 为标题 | 推荐继续已有任务 |
| `/find-task <kw>` — 附带意图 | 以附带意图为标题 | 可接受 |
| `/find-task <kw>` 单独 | 标题 "find-task" | 不推荐作为首轮 |
| `/warmup` | 标题 "warmup" | 不推荐作为首轮, 用临时 session |

## 完整流程参考

```
新建任务:      /task "xxx" → (选) /plan → /design → /build → /review → /commit → /close
继续已有:      /start <id> → (选) /plan | /build → /checkpoint → ...
快速修复:      /task "xxx" → /build → /check → /commit → /close
临时中断:      /checkpoint → /handoff    (下次 /start <id> 恢复)
发现方向:      /warmup → 选择任务 → 新 session: /start <id>
```
