---
description: 工作区状态: git 分支, 文件改动, submodule, 进行中任务
agent: build
---

## 1. 收集状态

```bash
git branch --show-current
git status --short
git submodule status
```

## 2. 读取进行中任务

用 glob 扫描 `docs/tasks/doing/task-*.md`, 读取 `title:` 行.

## 3. 输出

```
## 工作区状态

### 分支
main

### 待提交 (git status)
M  src/foo.js
?? packages/new-pkg/

### Submodule
- packages/vendor/lib (abc1234) — 缺提交
M packages/vendor/lib2 (def5678) — 有脏修改

### 进行中任务
- task-yyyy-mm-dd-xxx — 任务描述 /doing
```
