---
description: 快速查看工作区状态
agent: build
---

运行以下命令收集状态:

```bash
git branch --show-current
git status --short
git submodule status
```

## 输出格式

```
## 工作区状态

### 分支
2605

### 待提交 (git status)
M  src/renderer/arrange-stage.js
?? packages/@dentalshop/new-pkg/

### Submodule
- packages/@sailing3d/mesh-js (abc1234) — 缺提交
M packages/@dentalshop/c-slice (def5678) — 有脏修改

### 进行中任务
tasks/doing/task-2026-05-30-xxx.md — 描述
```
