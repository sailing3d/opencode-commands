---
description: 正式分组提交 + 推送, 用于成果收尾
---

与 /checkpoint 的区别: checkpoint 是随手中转保存 (一键 add + commit, 不分组不推送).
此命令用于代码完成后的正式提交, 涉及分组审查、submodule 处理、推送.

用 todo 工具组织以下步骤

---

## 1. 检查

运行 `git status --short` 和 `git submodule status`, 检查:

- **submodule 变更**: `git submodule status` 显示:
  - `+` 前缀 — submodule 有本地新提交未推送
  - `-` 前缀 — submodule 缺提交 (落后上游)
  - `M` 前缀 — submodule 有未提交的修改
  - 有 `M` 时, 先进入 submodule 目录 `git status` 确认脏文件, 决定是否先提交
  - 运行 `git diff --submodule` 查看具体变更
- **文件分组**: 根据改动内容将文件分为若干逻辑组 (每组对应一个 commit)
- **归属疑问**: 某些文件是否属于本次改动存疑, 标记为"需用户确认"
- **当前分支**: 确认当前所在分支是否正确
- **无关文件**: 不属于本次改动的文件, 建议跳过
- **上游变更**: 是否需要先 pull/rebase 最新代码, 解决冲突后再提交

## 2. 生成简报

格式:

```
## 提交简报

### Submodule
| 路径 | 新 hash | 变更说明 |
|------|---------|---------|
| packages/@xxx | abc1234 | 修复 xxx |
> 是否提交并推送此 submodule?

### 建议分组

| 组 | 文件 | 建议 commit message |
|----|------|-------------------|
| 1 | src/foo.js, docs/tasks/... | refactor(foo): 实现 xxx |
| 2 | (如需确认) | — |

### 待确认
- 当前分支: xxx 是否确认?
- xxx.js: 是否属于本次改动?
- yyy/ 目录: 是否一并提交?

### 建议跳过
- xxx_file: 非本次引入, 建议跳过
```

## 3. 用户确认

等待用户确认或调整. 用户可回复:

- "ok" / "执行" — 按简报全部提交
- "第 2 组不需要" — 排除某组
- "xxx.js 也要提交" — 补充文件
- "submodule A 提交, B 跳过" — 选择性处理 submodule
- "package-lock 也提交" — 取消跳过

可以用 `question` 工具针对待确认项逐条询问.

## 4. 执行

1. **Submodule**: 如用户确认:
   - 先进入 submodule 目录, 运行 `git status --short` 确认无脏修改
   - 确认当前分支: `git branch --show-current`, 与用户确认目标推送分支
   - 执行 `git add <files>` → `git commit -m "<message>"`
2. **主仓库**: 按确认后的分组: `git add <files>` → `git commit -m "<message>"`
3. **提示推送**: 提交完成后, 请示用户是否执行 `git push` 将所有主仓库和子仓库改动推送到远程

## 5. 注意

- **分组原则**: 每个 commit 应该是一个独立的逻辑单元, 包含相关改动. 不要为了追求完美分组而过度拆分或合并.
- **注意 submodule**: submodule 的提交和推送需要额外注意, 确保先处理 submodule 再提交主仓库, 避免出现主仓库指向一个未推送的 submodule commit 的情况.
- **避免删除**: 不要执行 `git checkout -- <file>` 或 `git reset --hard` 等会丢失改动的命令, 不确定的内容可以先暂存到一个临时分支, 或者用 `git stash` 保存当前改动, 以免误操作导致数据丢失.
- **保持沟通**: 在整个过程中, 与用户保持沟通, 确保每一步操作都得到确认, 避免误解和错误提交.
- **检查大文件**: 如果改动中包含二进制大文件, 确认这些文件是否需要提交, 向用户确实是否要排除或用 lfs 管理.
