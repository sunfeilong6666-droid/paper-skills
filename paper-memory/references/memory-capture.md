# Memory Capture

本文件用于 paper-memory 高频日常记录。不要执行深度 candidate 测试，不要晋升 `skill.md`。

## 目标

把用户反馈写入正确位置：

| 类型 | 写入位置 |
|---|---|
| 项目事实 | `.paper_ai/02memory/project/project.md` |
| 阅读偏好 | `.paper_ai/02memory/profile/read.md` |
| 写作偏好 | `.paper_ai/02memory/profile/write.md` |
| 可能长期规律 | `.paper_ai/02memory/profile/candidate.md` |
| 已验证稳定规则 | `.paper_ai/02memory/profile/skill.md`，仅深度蒸馏可写 |

## 分类规则

### 项目事实

用户提供研究方向、研究对象、实验设计、实验结果、论文框架、当前问题、下一步计划、导师意见或重要约束时，写入 `project.md`。

记录时区分：

```text
已确认事实 / 合理推断 / 待确认问题 / 已废弃内容
```

### 阅读偏好

用户指出 AI 读文献时应该增加、减少或改变某类关注点时，写入 `read.md`。

常见类型：

```text
背景问题 / 方法本质 / 结果提取 / 判断分析 / 证据链 / 结论强度 / 课题映射 / 术语表达 / 反模式
```

### 写作偏好

用户指出论文写作、润色、修改中的偏好时，写入 `write.md`。

常见类型：

```text
术语强度 / 句式结构 / 逻辑组织 / 结论强度 / 期刊风格 / 证据边界 / 修改习惯 / 反模式
```

### candidate 轻量更新

满足任一条件时，可以轻量更新 `candidate.md`：

1. 用户明确说“以后都要这样”；
2. 用户多次纠正同类问题；
3. 该偏好可迁移到多篇文献或多次写作；
4. 该偏好影响 evidence boundary；
5. 该偏好影响 paper-study 或 nature-writing 的默认输出。

只追加候选摘要、证据来源和状态。不要读取深度蒸馏流程。

默认状态：

```text
seed / accumulating / retained / conflicted
```

## 写入模板

### project.md

```markdown
## [YYYY-MM-DD] 项目更新：[简短标题]

### 来源
用户本次对话 / 用户上传文件 / 用户纠正 / 论文材料

### 类型
已确认事实 / 合理推断 / 待确认问题 / 已废弃内容

### 内容
- ...

### 与当前项目的关系
- ...

### 备注
- ...
```

### read.md

```markdown
## [YYYY-MM-DD] 阅读偏好：[简短标题]

### 用户原始反馈
> ...

### 偏好类型
背景问题 / 方法本质 / 结果提取 / 判断分析 / 证据链 / 结论强度 / 课题映射 / 术语表达 / 反模式

### 以后执行方式
- ...

### 适用场景
- ...

### 是否可能形成 candidate
是 / 否
```

### write.md

```markdown
## [YYYY-MM-DD] 写作偏好：[简短标题]

### 用户原始反馈
> ...

### 偏好类型
术语强度 / 句式结构 / 逻辑组织 / 结论强度 / 期刊风格 / 证据边界 / 修改习惯 / 反模式

### 推荐写法
- ...

### 避免写法
- ...

### 适用场景
- ...

### 是否可能形成 candidate
是 / 否
```

### candidate.md

```markdown
## C-[TYPE]-[编号] [候选规律名称]

### 状态
seed / accumulating / retained / conflicted

### 候选规律
一句话说明可能的长期规律。

### 触发场景
- ...

### 具体执行方式
- ...

### 证据来源
- [YYYY-MM-DD] read.md / write.md / project.md / 用户纠正：...

### 处理决定
继续积累 / 暂时保留 / 标记冲突
```

## 10 次反馈提醒

当 `read.md` 与 `write.md` 累计新增 10 条记录后，提醒用户：

```text
最近已累计 10 条阅读/写作反馈，是否需要执行一次 memory 蒸馏？
如果确认，我将读取 references/memory-distillation.md，对 read.md、write.md、candidate.md 和 skill.md 进行候选规律整理与测试。
```

未经用户明确同意，不自动执行深度蒸馏。

