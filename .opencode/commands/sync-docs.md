---
description: 更新任务关联文档, 保持引用一致性
agent: build
---

1. 读取当前进行中的任务文件 (docs/tasks/doing/task-*.md)
2. 检查本次 session 所有改动的文件是否已列入 related 字段, 未列入的先补充
3. 遍历 related 中的 doc-*.md, 更新各文件的 date 和进展描述
4. 更新 TODO.md: 扫描 docs/tasks/todo/ 目录重建索引, 检查是否有新散落待办
5. 检查项目约定文件是否需要新增文档引用或注意事项
6. 输出变更摘要, 提示用户执行 /commit 或 /checkpoint 提交

/close 和 /handoff 都会调用此命令的步骤, 无需单独执行.
