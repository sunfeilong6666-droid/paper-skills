---
name: paper-memory
description: |
  Paper Memory 是项目事实、阅读偏好、写作偏好和用户科研判断习惯的长期记忆入口。日常高频调用只执行 references/memory-capture.md；只有用户明确要求蒸馏、更新 skill、审查 candidate 或构建 skill 时，才读取 references/memory-distillation.md。
---

# Paper Memory

Paper Memory 不替代 paper-ai 路由。

它只负责：

1. 记录项目事实；
2. 记录阅读偏好；
3. 记录写作偏好；
4. 轻量维护 candidate；
5. 在用户明确要求时执行深度蒸馏。

公共目录和安全边界见：

```text
../paper-ai/references/runtime-contract.md
```

## 运行文件

```text
<项目根目录>/.paper_ai/02memory/project/project.md
<项目根目录>/.paper_ai/02memory/profile/read.md
<项目根目录>/.paper_ai/02memory/profile/write.md
<项目根目录>/.paper_ai/02memory/profile/candidate.md
<项目根目录>/.paper_ai/02memory/profile/skill.md
```

## 入口分流

| 用户意图 | 路径 | 读取文件 |
|---|---|---|
| 日常记录 | capture | `references/memory-capture.md` |
| 构建 skill | distillation | `references/memory-distillation.md` |
| 更新 skill | distillation | `references/memory-distillation.md` |
| 审查 candidate | distillation | `references/memory-distillation.md` |

默认执行 capture。

只有当用户明确说以下含义时，才执行 distillation：

```text
构建 skill
更新 skill
蒸馏我的科研习惯
审查 candidate
看看哪些 candidate 可以晋升
测试候选规律
```

## 核心边界

- 记录不等于蒸馏。
- 单次反馈不得直接写入 `skill.md`。
- 项目事实只写入 `project.md`。
- 阅读偏好优先写入 `read.md`。
- 写作偏好优先写入 `write.md`。
- 可能形成长期规律时，轻量更新 `candidate.md`。
- `candidate.md` 默认不参与 paper-study 或 nature-writing 的稳定调用。
- `skill.md` 只保存验证通过的用户科研心智模型。

## 输出规范

记录类任务输出简短摘要：

```markdown
已更新 paper-memory：

- project.md：新增/更新 ...
- read.md：新增/更新 ...
- write.md：新增/更新 ...
- candidate.md：新增/更新 ...
- skill.md：无变化

本次没有直接修改 skill.md，因为该偏好仍需积累证据。
```

蒸馏类任务按 `references/memory-distillation.md` 的输出规范执行。


