---
name: paper-study
description: |
  文献知识库与专业术语资产库构建 skill。用于读取 MinerU 输出、对单篇论文建立结构化 study、维护术语库、生成检索索引，并根据 paper-memory guidance 回原文修正 study。新版输出统一写入 .paper_ai/03study/。
---

# Paper Study

Paper Study 是文献知识库执行层。

它把 MinerU 输出转化为可检索、可讨论、可写作的单篇论文资产。

公共目录、模型分工和安全边界见：

```text
../paper-ai/references/runtime-contract.md
```

详细输出模板见：

```text
references/study-output-template.md
```

## 输入

正式学习来源必须来自：

```text
<项目根目录>/.paper_ai/01mineru/01mineru/
```

不得用中文镜像或翻译稿替代证据判断。

## 读取 memory

学习时读取：

```text
<项目根目录>/.paper_ai/02memory/project/project.md
<项目根目录>/.paper_ai/02memory/profile/read.md
<项目根目录>/.paper_ai/02memory/profile/skill.md
```

默认不读取：

```text
<项目根目录>/.paper_ai/02memory/profile/candidate.md
```

除非用户正在测试候选规律。

## 输出目录

```text
<项目根目录>/.paper_ai/03study/breakdown/
  journal_articles/
  reviews/
  theses/
  other/
<项目根目录>/.paper_ai/03study/terminology/terminology.md
<项目根目录>/.paper_ai/03study/retrieval_index/index.md
```

可以创建缺失文件和目录。

不得覆盖已有内容；修正旧 study 时追加 `Revision` 区块。

## 单篇内容库目标

每篇论文必须精读，覆盖：

1. 元信息；
2. 研究背景；
3. 研究目标和假设；
4. 方法和数据；
5. 结果；
6. 图表解读；
7. 讨论和机制；
8. 局限和风险；
9. 对用户课题的价值；
10. 专业术语和写作表达。

不要逐段全文翻译。

## 子 agent 执行边界

paper-study 可以由 paper-ai 创建的独立子 agent 执行。

无论由主 agent 还是子 agent 执行，单篇精读标准不变。

不得因为批量学习、队列较长或上游要求“快一点”而省略背景、方法、结果、图表、讨论、局限、术语和写作材料。

做不到完整精读时，明确标记失败或待复核，不得输出伪完整 study。

paper-study 内部不要再创建子 agent 分包阅读同一篇文章；单篇证据判断必须保持统一。

## 文献类型

| 类型 | 输出目录 | 重点 |
|---|---|---|
| Journal Articles | `03study/breakdown/journal_articles/` | 问题、方法、结果、图表、讨论、证据边界 |
| Reviews | `03study/breakdown/reviews/` | 领域共识、争议、机制框架、证据层级 |
| Theses | `03study/breakdown/theses/` | 章节组织、实验设计、结果表达、讨论写法 |
| Other | `03study/breakdown/other/` | 按材料性质记录，不拔高证据强度 |

综述和学位论文不当作一手实验证据。

## 证据边界

每个关键判断必须归入一种来源：

| 标签 | 含义 |
|---|---|
| 原文事实 | 论文明确写出的方法、数据、结果 |
| 作者声称 | 摘要、讨论或结论中的解释 |
| 作者推断 | 基于结果提出但未直接证明的机制或意义 |
| AI 判断 | 对证据强弱、结构和写作价值的判断 |
| 用户偏好 | 来自 paper-memory 的项目/稳定偏好 |
| 待复核 | MinerU 解析不清、图表缺失、统计细节不明 |

禁止把作者讨论中的假设写成实验结果。

## 术语库

术语库只维护一个主文件：

```text
<项目根目录>/.paper_ai/03study/terminology/terminology.md
```

每篇论文一个区块。

同一论文内同一术语不重复积累，除非语境差异会影响翻译。

优先采用：

```text
<项目根目录>/.paper_ai/02memory/profile/read.md
<项目根目录>/.paper_ai/02memory/profile/skill.md
```

## 检索索引

每篇论文追加到：

```text
<项目根目录>/.paper_ai/03study/retrieval_index/index.md
```

记录 Paper ID、主题标签、方法标签、结果标签、图表标签、可用于写作的位置、study 路径和 MinerU 路径。

## 接收 memory guidance

当存在：

```text
<项目根目录>/.paper_ai/04dialogue/memory_to_study_guidance.md
```

或用户要求“按 memory 修正 study”时：

1. 读取 guidance、相关 memory、原 study 和 MinerU 输出。
2. 判断用户反馈哪些被原文支持，哪些只是用户假设或偏好。
3. 在对应 study 文件追加 `Revision` 区块。
4. 同步更新术语库和检索索引。

不要只复述用户意见，必须回到证据。

## 写作前材料整理

当 paper-ai 或用户要求为写作提供材料时，输出材料包：

- 可写事实；
- 作者观点与可引用证据；
- 只能作为讨论假设的内容；
- 用户偏好的术语和表达；
- 可复用原文表达及中文说明；
- 不建议写入正文或需要复核的内容。

优先交给 `nature-writing`。

## 不要做

- 不要替代 MinerU 保存全文镜像。
- 不要默认生成跨论文综述。
- 不要把综述当作一手实验结果。
- 不要补不存在的方法、数据、图表、结论或引用。
- 不要覆盖旧 study。
- 不要在 paper-study 内部再创建子 agent 分包生成正式 study。
- 不要粗略阅读。

## 质量检查

输出前检查：

- 是否建立或更新单篇结构化内容库。
- 是否覆盖背景、方法、结果、图表、讨论、局限、术语和写作材料。
- 是否保留 Paper ID、study 路径和 MinerU 来源路径。
- 是否区分证据标签。
- 是否更新术语库。
- 是否更新检索索引。
- 如果有 memory guidance，是否回原文复核并记录 revision。



