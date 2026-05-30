---
description: 更新当前任务 related 文档并提交推送
agent: build
---

1. 读取当前进行中的任务文件 (tasks/doing/task-*.md)
2. 检查本次 session 所有改动的文件是否已列入 related 字段, 未列入的先补充
3. 遍历 related 中的 doc-*.md, 更新各文件的 date 和进展描述
4. 更新 TODO.md: 扫描 tasks/todo/ 目录重建索引, 检查是否有新散落待办
5. 检查 AGENTS.md 是否需要新增文档引用或避坑记录
6. 输出变更摘要, 提示用户执行 commit 命令提交
