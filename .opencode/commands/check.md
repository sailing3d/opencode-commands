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

## 4. 自动修复循环

失败项按以下顺序处理:
1. **可自动修复的** (如 `lint --fix`): 执行修复 → 重新运行验证 → 记录修复内容
2. **不能自动修复的**: 向用户说明错误项, 建议通过 /build 手动修复后再次 /check
3. **最大重试次数**: 自动修复循环最多 2 轮, 仍有失败则输出剩余问题供用户决策

## 5. 输出最终结果

```
## 验证结果

| 命令 | 结果 | 说明 |
|------|------|------|
| npm run typecheck | ✅ 通过 | — |
| npm run lint | ✅ 已修复 | lint --fix 自动修复 2 处 |
| npm run build | ❌ 失败 | 编译错误, 见详情 |

### 自动修复
- src/foo.ts:42 — 未使用变量, 已删除

### 剩余问题 (需手动修复)
- src/bar.ts:15 — 类型错误, 需手动修复
```
