---
description: 运行验证: lint, typecheck, compile
agent: build
subtask: true
---

## 1. 确定验证范围

用 explore agent 快速扫描, 确定当前改动涉及的模块和技术栈:

- 查看最近的 git diff — 哪些文件被修改
- 查看根 package.json 的 scripts — 有哪些可用验证命令
- 查看修改文件的目录 — 是否属于某个 package

## 2. 运行验证

按顺序运行相关验证命令, 记录结果:

- **TypeScript**: 如果有 `tsc` 或 `typecheck` script, 先运行
- **Lint**: 如果有 `lint` script, 运行
- **Compile**: 如果有 `build` 或相关 compile script, 运行

package 级别: 如果改动在某个 package 内, 先 cd 到该 package 目录运行其验证命令.

## 3. 输出结果

```
## 验证结果

| 命令 | 结果 | 说明 |
|------|------|------|
| npm run typecheck | ✅ 通过 | — |
| npm run lint | ❌ 失败 | src/foo.ts:42: 未使用变量 |
```

失败项先尝试自动修复, 不能修复的向用户说明.
