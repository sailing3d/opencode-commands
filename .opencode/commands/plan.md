---
description: 分析需求, 制定方案, 确认后进入 Build
agent: plan
subtask: false
---

## 1. 理解需求

与用户澄清以下问题 (如已明确可跳过):

- 目标: 要解决什么问题 / 实现什么功能
- 范围: 涉及哪些页面 / 模块 / package
- 约束: 是否有已知限制或约定
- 优先级: 紧急程度 / 是否阻塞其他工作

## 2. 影响分析

用 explore agent (thoroughness: "medium") 扫描受影响区域:

- 扫描 `related` 引用链 — 已有相关 task/doc 的, 先读取
- 扫描代码文件 — 识别需要修改的文件分布
- 识别依赖关系 — 是否有跨 package 改动, 是否需要新增包

## 3. 制定方案

输出方案摘要:

```
## 方案

### 目标
一句话说明

### 修改范围
| 文件 | 改动类型 | 说明 |
|------|---------|------|
| src/renderer/foo.js | 修改 | 替换 fs.readFile → fileService.read |
| packages/@dentalshop/bar/ | 新增 | 新建包, 从 2403 复制 |

### 步骤
1. Step 1 — xxx
2. Step 2 — xxx
3. ...

### 风险
- xxx (需要用户确认)
```

## 4. 确认

用 question 工具向用户呈现方案, 确认后进入 Build 阶段.
