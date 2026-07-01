---
name: paper-memory
description: |
  用户科研阅读与写作偏好蒸馏 skill。用于从用户对 paper-study 输出的纠错、补充、反驳、术语修正、写作偏好和证据边界要求中，提炼项目内 memory，并生成给 paper-study 的修正指导。触发词包括：paper-memory、study 读得不对、我觉得这篇不是这样、这个术语以后这样翻译、培养 memory、温故而知新、按我的阅读水平、记录我的判断、指导 study。
---

# Paper Memory

Paper Memory 是用户科研判断方式的蒸馏器。它不重新学习论文，不直接改正式 study，不替代原文证据；它只从用户反馈中提炼可复用的阅读、证据、术语和写作偏好，并把修正要求写成 `paper-study` 可以执行的指导。

`paper-memory/SKILLgpt.md` 只是外部草稿参考，不作为正式流程。

## 输出位置

所有记忆写入项目根目录内，不写入 skill 安装目录：

```text
<项目根目录>/.paper_ai/profile/general_profile.md
<项目根目录>/.paper_ai/profile/project_profile.md
<项目根目录>/.paper_ai/profile/pattern_candidates.md
<项目根目录>/.paper_ai/dialogue/correction_log.md
<项目根目录>/.paper_ai/dialogue/memory_to_study_guidance.md
```

可以创建缺失文件。不得覆盖已有内容；追加新条目，或在旧条目下记录状态变化、冲突和废弃原因。

## 核心边界

- 只从用户明确反馈中学习；不要凭空推断用户偏好。
- 区分论文事实、用户判断、用户习惯；不要把用户假设写成论文事实。
- 项目级偏好优先写入 `project_profile.md`；能跨项目迁移且多次验证后才进入 `general_profile.md`。
- 不直接生成修正版 study；只生成 `memory_to_study_guidance.md`，由 `paper-study` 回到 MinerU 输出和已有 breakdown 修正。
- 不把通用科研底线伪装成用户特异偏好，例如“不要编造数据”只算底线，不算用户画像。

## 输入读取

处理用户反馈前，尽量读取：

```text
<项目根目录>/.paper_ai/profile/general_profile.md
<项目根目录>/.paper_ai/profile/project_profile.md
<项目根目录>/.paper_ai/profile/pattern_candidates.md
<项目根目录>/.paper_ai/dialogue/correction_log.md
<项目根目录>/.paper_ai/knowledge/02breakdown/
<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md
<项目根目录>/.paper_ai/knowledge/01mineru/
```

如果无法确认项目根目录，先让 `paper-ai` 确认；不要把项目记忆写到 skill 目录。

## 反馈分类

先判断用户反馈属于哪类，可多选：

| 类型 | 用户表现 | 记忆重点 |
|---|---|---|
| 事实纠错 | “论文不是这样说的”“图 3 不是这个意思” | AI 容易错在哪里，后续 study 如何复核 |
| 遗漏补充 | “引言背景要多看”“漏了统计方法” | 用户阅读重点 |
| 过度解读 | “这个推太远了”“没有证据” | 证据边界和推断禁区 |
| 术语修正 | “这个词以后翻译成...” | 项目术语和概念边界 |
| 结构偏好 | “以后按这个顺序读” | study 输出结构 |
| 写作偏好 | “太像 AI”“讨论要更保守” | 写作表达 DNA |
| 阶段变化 | “现在重点看方法/结果/写作” | 当前项目阶段的检索和回答侧重 |

## Nuwa 式三层蒸馏

每次反馈都先生成一个 memory delta，再判断它属于哪一层。

### 1. 阅读心智模型

提炼用户如何看论文，而不是用户在某篇论文上的单个结论。

可沉淀的模型示例：
- 用户优先检查“方法是否支撑结论”。
- 用户要求图表解读必须超越标题复述，说明数据变化、组间比较和作者实际证明了什么。
- 用户接受机制推断，但要求把“结果、作者讨论、AI 推断、缺失证据”分开。

每条心智模型必须写明：

```markdown
### [模型名]
- 状态：candidate / confirmed / project_specific / conflict / deprecated
- 一句话：
- 用户证据：
- 适用场景：
- 不适用场景：
- 对 paper-study 的约束：
- 最近更新：
```

### 2. 决策启发式

把用户偏好转成下一次能直接触发的读文献规则。

格式：

```markdown
### [规则名]
- 如果遇到：
- 就必须：
- 禁止：
- 来源反馈：
- 状态：
```

例：如果作者只在 discussion 中提出可能机制，就必须标为“作者假设/讨论推断”，禁止写成实验结果。

### 3. 表达 DNA

记录用户偏好的术语、语气、证据标注和写作表达。

重点包括：
- 推荐译法、禁用译法、容易混淆的术语。
- 喜欢结论先行还是背景铺垫。
- 讨论部分要保守、批判、机制化还是写作化。
- 是否要求保留英文术语、物种名、基因名、统计名。
- 用户当前项目阶段：初期偏背景，中期偏方法，后期偏结果讨论，末期偏写作表达。

表达 DNA 只指导呈现和写作，不改变原文事实。

## Nuwa 三重验证

一条 memory 进入长期画像前，按三重验证判断：

| 验证 | 问题 | 通过标准 |
|---|---|---|
| 可迁移性 | 能不能指导下一篇文献或下一次写作？ | 能形成明确行动约束 |
| 重复性 | 用户是否多次表达，或明确说“以后都这样”？ | 多次出现，或一次明确长期指令 |
| 排他性 | 这是不是用户自己的偏好，而非所有科研任务的常识？ | 能体现用户研究阶段、领域或表达品味 |

三项全过可写 confirmed。只过一到两项写入 `pattern_candidates.md`。只对某个课题有效写入 `project_profile.md` 并标 `project_specific`。

## Darwin 式迭代机制

每次用户纠错后执行一个小型棘轮循环：

1. **生成 delta**：记录用户反馈、原 AI 问题、候选 memory、对 study 的约束。
2. **评分**：按可迁移性、重复性、排他性、改进价值各 0-2 分评估。
3. **决定去向**：
   - 7-8 分：写入 confirmed。
   - 4-6 分：写入 candidate。
   - 1-3 分：只写 correction log。
   - 与旧规则冲突：写 conflict，不覆盖。
4. **生成指导**：把可执行修正写入 `memory_to_study_guidance.md`。
5. **等待 study 执行**：由 `paper-study` 回原文复核并修正知识库。

改进价值评分看这条规则是否能减少下一次同类错误。不能改善后续 study 的反馈，只记录，不升级。

## 文件写法

### general_profile.md

只收跨项目、长期稳定的用户习惯：

```markdown
# General Paper Memory

## Reading Mental Models

## Evidence Standards

## Writing DNA

## Terminology Preferences

## Deprecated / Conflicted Rules
```

### project_profile.md

记录当前课题、当前阶段和领域偏好：

```markdown
# Project Paper Memory

## Project Scope
- 项目名称：
- 研究方向：
- 当前阶段：
- 最近更新：

## Project-Specific Reading Priorities

## Project-Specific Evidence Standards

## Project Writing DNA

## Terminology and Naming

## Known Weaknesses in Previous Study Outputs
| 日期 | 论文/任务 | 错误类型 | 用户反馈 | 后续约束 |
|---|---|---|---|---|
```

### pattern_candidates.md

记录尚未确认的候选偏好：

```markdown
# Pattern Candidates

## [候选偏好]
- 状态：candidate
- 分数：
- 来源反馈：
- 为什么暂不升级：
- 下一次如何验证：
```

### correction_log.md

每次用户纠错都追加：

```markdown
## [日期] [论文/任务]

### 原 AI 问题
- 错误类型：
- 原问题摘要：

### 用户反馈
> [用户关键原话或摘要]

### Memory Delta
- 候选规则：
- Nuwa 验证：
- Darwin 评分：
- 写入位置：

### 对 paper-study 的约束
- [可执行指导]
```

### memory_to_study_guidance.md

只写给 `paper-study` 的操作指令：

```markdown
# Memory to Study Guidance

## Task
- 论文/任务：
- 触发反馈：
- 需要复核的原 study 位置：
- 需要回看的 MinerU 输出：

## Required Corrections
- [必须修正什么]

## Evidence Rules
- [哪些说法必须找原文、图表、方法或数据支持]

## Terminology Rules
- [术语、译法、概念边界]

## Output Rules
- [修正时如何标注 memory 来源、如何保留旧判断]
```

## 冲突与降级

新反馈和旧 memory 冲突时，不直接覆盖。记录：

```markdown
### Conflict: [规则名]
- 旧规则：
- 新反馈：
- 判断：
- 当前适用范围：
- 后续验证：
```

当用户明确否认旧规则、项目阶段变化、或旧规则被错误推广时，将旧规则标为 `deprecated`，并说明原因。

## 输出给用户

完成后简短说明：

1. 已识别的用户反馈类型。
2. 新增或更新了哪些 memory。
3. 已生成哪些 `paper-study` 修正指导。
4. 哪些内容仍是 candidate，等待后续验证。

不要输出大段论文重写内容。不要声称已经完成 study 修正，除非 `paper-study` 已执行。

## 质量检查

结束前检查：

- memory 是否有用户反馈依据。
- 是否区分论文事实、用户判断和用户习惯。
- 是否正确放入项目级或通用级。
- 是否按 Nuwa 三重验证和 Darwin 评分决定状态。
- 是否生成 `memory_to_study_guidance.md`。
- 是否避免把单次反馈升级成长期规则。
- 是否没有直接改正式 study。
