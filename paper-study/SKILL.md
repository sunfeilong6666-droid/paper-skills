---
name: paper-study
description: |
  文献知识库与专业术语资产库构建 skill。用于读取 MinerU 输出、对单篇论文建立尽量完整的结构化内容库、维护项目级术语库、生成后续检索和写作可用的证据资产，并根据 paper-memory 的 guidance 回原文修正 study。触发词包括：paper-study、学习 MinerU 结果、建立文献知识库、单篇论文学习、积累术语、更新术语库、修正 study、重读文献、按 memory 指导修正、检索文献内容库。
---

# Paper Study

Paper Study 是文献知识库执行层。它负责把 MinerU 的全文镜像转化为可检索、可讨论、可写作的结构化单篇论文资产。

它不替代 MinerU 全文镜像；全文和版面细节以 `.paper_ai/knowledge/01mineru/` 为准。Study 的职责是尽量完整地概括、分层、标注证据边界、积累术语，并为后续 `paper-ai` 交互提供材料。

## 模型分工

当由 `paper-ai` 调度时：

- `gpt-5.4-mini` 负责文件检索、候选片段定位、批量筛选、判断哪些 study 文件相关。
- 用户当前模型负责正式阅读判断、证据边界判断、观点讨论、写作取材和最终交付。
- 如果必须使用 `gpt-5.4-mini` 但不可用，停止并询问用户，不自动改用其他模型。

Paper Study 自身不要假装切换模型；只按调度层提供的模型和上下文工作。

## 输入来源

优先读取：

```text
<项目根目录>/.paper_ai/knowledge/01mineru/
<项目根目录>/.paper_ai/knowledge/00papers/paper_file_map.md
<项目根目录>/.paper_ai/profile/general_profile.md
<项目根目录>/.paper_ai/profile/project_profile.md
<项目根目录>/.paper_ai/profile/pattern_candidates.md
<项目根目录>/.paper_ai/dialogue/memory_to_study_guidance.md
<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md
```

如果 memory 文件不存在，继续建立基础内容库；不要因此中断初读。

## 输出目录

```text
<项目根目录>/.paper_ai/knowledge/02breakdown/
  journal_articles/
  reviews/
  theses/
  other/
<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md
<项目根目录>/.paper_ai/knowledge/04retrieval_index/index.md
```
### 输出文件名边界
每个输出文件的文件名不允许被压缩，要完全保留

可以创建缺失文件和目录。不得覆盖已有内容；修正旧 study 时追加“Revision”区块，说明来源、日期和 memory guidance，不要无痕改写旧判断。

## 单篇内容库目标

每篇论文都尽量建立完整结构化概括，覆盖：

1. 元信息：Paper ID、标题、作者、年份、期刊、DOI/链接、文献类型、MinerU 来源路径。
2. 研究背景：领域问题、知识缺口、作者提出问题的逻辑。
3. 研究目标和假设：作者明确目标、隐含假设、AI 不能确认的假设。
4. 方法和数据：实验设计、样本、模型、统计方法、关键参数、方法限制。
5. 结果：主要发现、数据关系、图表证据、负结果或不显著结果。
6. 图表解读：每个关键图表说明它证明了什么、没有证明什么、是否支撑作者结论。
7. 讨论和机制：作者讨论、作者推断、AI 推断、缺失证据分开写。
8. 局限和风险：作者承认的局限、AI 识别的证据短板、需要用户复核的点。
9. 可用于用户课题的材料：可借鉴问题、方法、结果表达、讨论角度和不可直接使用的点。
10. 专业术语和表达：同步写入单文件术语库。

不要逐段全文翻译；如果用户要看全文或逐段原文，回到 MinerU 输出。

## 文献类型处理

### Journal Articles

输出到：

```text
<项目根目录>/.paper_ai/knowledge/02breakdown/journal_articles/
```

重点完整覆盖研究问题、方法、结果、图表、讨论、证据边界和可写作素材。

### Reviews

输出到：

```text
<项目根目录>/.paper_ai/knowledge/02breakdown/reviews/
```

综述不当作一手实验证据。重点提炼领域共识、争议、机制框架、术语体系、背景写作素材和原综述引用的证据层级。

### Theses

输出到：

```text
<项目根目录>/.paper_ai/knowledge/02breakdown/theses/
```

学位论文重点学习章节组织、研究问题展开、实验设计、结果表达、讨论写法和可借鉴结构。不要把学位论文当成高强度外部证据。

## Study 文件建议结构-Journal Articles

AI 可根据论文类型调整，Journal Articles 至少包含以下区块：

```markdown
# [Paper ID] [Title]

## Metadata

## One-page Structured Overview

## Background and Research Gap

## Questions / Hypotheses

## Methods and Data

## Results and Figure/Table Evidence

## Discussion, Mechanisms, and Boundaries

## Limitations and Missing Evidence

## Relevance to User Project

## Reusable Writing Materials

## Terms Extracted

## Retrieval Tags

## Revision History
```

`Retrieval Tags` 用于后续 mini 检索，写关键词、方法名、物种/材料、机制、图表主题、研究阶段标签。

## Study 文件建议结构-Reviews、Theses

Reviews和Theses根据用户的研究方向可进行多次学习覆盖。

Reviews不可用Journal Articles的建议格式




## 证据边界规则

每个关键判断必须归入一种来源：

| 标签 | 含义 |
|---|---|
| 原文事实 | 论文明确写出的方法、数据、结果 |
| 作者声称 | 作者在摘要、讨论或结论中提出的解释 |
| 作者推断 | 作者基于结果提出但未直接证明的机制或意义 |
| AI 判断 | AI 对证据强弱、结构和写作价值的判断 |
| 用户偏好 | 来自 paper-memory 的项目/通用偏好 |
| 待复核 | MinerU 解析不清、图表缺失、统计细节不明 |

禁止把作者讨论中的假设写成实验结果。禁止把用户观点写成论文事实。

## 术语库规则

术语库只维护一个主文件：

```text
<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md
```

每篇论文一个区块，按模块组织：

```markdown
## [Paper ID] [Title]

### 引言/背景
| Term | 中文推荐 | 原句/语境 | 解释 | 来源 |
|---|---|---|---|---|

### 方法
| Term | 中文推荐 | 原句/语境 | 解释 | 来源 |
|---|---|---|---|---|

### 结果/讨论
| Term | 中文推荐 | 原句/语境 | 解释 | 来源 |
|---|---|---|---|---|

### 写作表达
| Expression | 可用场景 | 原句 | 中文说明 |
|---|---|---|---|
```

规则：

- `Term` 有缩写时写成 `full term (ABBR)`；没有缩写不强行加括号。
- 同一论文内同一术语不重复积累，除非语境差异会影响翻译。
- 优先采用 `project_profile.md` 中的用户术语偏好。
- 保留原句或语境，避免脱离上下文机械翻译。
- 若用户明确要求拆分术语库，先暂停询问格式与范围。

## 检索协作

当用户后续通过 `paper-ai` 提问或写作时：

1. 由 `gpt-5.4-mini` 先检索 `02breakdown/`、`03terminology/`、`04retrieval_index/`，找出相关 Paper ID、区块和候选片段。
2. 当前模型读取候选片段、必要时回看 MinerU 输出。
3. 当前模型完成正式分析、讨论或写作材料整理。

`paper-study` 需要维护 `04retrieval_index/index.md`，追加每篇论文的检索摘要：

```markdown
## [Paper ID] [Title]
- 文献类型：
- 主题标签：
- 方法标签：
- 结果标签：
- 图表标签：
- 可用于写作：背景 / 方法 / 结果 / 讨论 / 局限
- study 路径：
- MinerU 路径：
```

## 接收 paper-memory 指导

当存在：

```text
<项目根目录>/.paper_ai/dialogue/memory_to_study_guidance.md
```

或用户要求“按 memory 修正 study”时：

1. 读取 guidance、相关 memory、原 study 和 MinerU 输出。
2. 判断用户反馈哪些被原文支持，哪些只是用户假设或偏好。
3. 在对应 study 文件追加 `Revision` 区块，说明修正点、证据、仍不确定的地方。
4. 同步修正术语库、retrieval index 和相关标签。
5. 在最终回复中说明已按 memory 指导落实了哪些修改。

不要只复述用户意见；必须回到证据。

## 写作前材料整理

当 `paper-ai` 或用户要求为写作提供材料时，输出给写作 skill 的材料包：

- 相关文献的可写事实。
- 作者观点与可引用证据。
- 只能作为讨论假设的内容。
- 用户偏好的术语和表达。
- 可复用原文表达及中文说明。
- 不建议写入正文或需要复核的内容。

优先交给 `nature-writing`。如果需要 `nature-writing` 但不可用，停止并询问用户。

## 不要做

- 不要替代 MinerU 保存全文镜像。
- 不要默认生成跨论文综述；只有用户明确要求时才综合多篇。
- 不要把综述当作一手实验结果。
- 不要为了完整而补不存在的方法、数据、图表、结论或引用。
- 不要覆盖旧 study 而不记录修正来源。
- 不要让中文镜像或翻译稿成为后续证据判断来源。
- 不要调用子 agent 学习论文、判断证据或生成正式 study；子 agent 最多用于翻译镜像或格式辅助。

## 质量检查

输出前检查：

- 是否建立或更新了单篇结构化内容库。
- 是否覆盖背景、方法、结果、图表、讨论、局限、术语和写作材料。
- 是否保留 Paper ID、study 路径和 MinerU 来源路径。
- 是否区分原文事实、作者声称、作者推断、AI 判断、用户偏好。
- 是否更新 `terminology.md`。
- 是否更新 `04retrieval_index/index.md`。
- 如果有 memory guidance，是否已回原文复核并记录 revision。
