---
description: 围绕当前任务阅读文档填充上下文; 无任务时熟悉整个项目
agent: plan
---

尽可能搜索和阅读与当前任务有关的文档, 了解项目全貌和问题上下文, 填充主代理上下文.

- **有当前任务**: 读取任务文件 + 其 related 引用 + 关联知识文档 + AGENTS.md + 相关技能 + git log
- **无当前任务**: 读取 AGENTS.md, TODO.md, docs/doc-*.md, .opencode/x-commands-README.md, git log --oneline -10

用表格输出关键发现: 任务目标、关联文件、相关约定.

如果当前任务含 `needs-investigation: true` 且尚未调查, 按 task-docs skill 的 **调查标记任务** 规则执行.

对发现的冲突、歧义、空缺的知识点进行提问, 让用户澄清.

重述关键的决策和约束, 让用户确认是否仍有效.
