---
description: 初始化/更新项目约定文件
agent: build
---

## 1. 检测

检查 `.opencode/x-commands-README.md`, `AGENTS.md`, `opencode.json`, `docs/tasks/doing/`, `TODO.md` 是否存在.

无 `x-commands-README.md` → 提示 "工具包未部署, 先通过 README 引导或 /x-commands-pull 部署后再运行 x-init"

## 2. 目录 + TODO.md

创建 `docs/tasks/doing/`, `docs/tasks/todo/`, `docs/tasks/done/`.

TODO.md 缺失则创建:

```markdown
# TODO

## 进行中

(无进行中任务)

## 待办

(无待办)
```

## 3. AGENTS.md

缺失则写入骨架:

```markdown
# AGENTS.md

## 项目介绍

（由你填写: 项目名称, 技术栈, 目录结构等）

## 重要提醒

- 禁止不可恢复的操作
  禁止删除文件夹, 禁止 git reset --hard 等. 高危操作先询问用户.

- 操作安全边界
  非本次任务产生的修改默认共存; 缺少上下文时明确说明阻塞项; 文件/数据操作代码编写后、执行前进行一次安全检查.

- 文档语言约定
  文档统一使用中文 (英文标点). 长期知识写入 docs/doc-*.md, 保持不过时、不重复、不互相冲突.

## 代码修改边界

| 区域 | 策略 |
|------|------|
| （由你填写） | — |
```

已有则跳过, 不覆盖.

## 4. opencode.json

缺失则创建:

```json
{
  "$schema": "https://opencode.ai/config.json"
}
```

已有则保留原有, 不覆盖.

## 5. 输出

表格列出各文件操作 (新增/更新/跳过) + 引导:

- 新项目: "编辑 AGENTS.md 填入项目介绍和代码修改边界, 重启后生效"
- 已有项目: "无变更"
