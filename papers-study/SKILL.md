---
name: papers-study
description: 文献知识库与专业术语资产库构建 skill。用于读取 MinerU 输出、对单篇论文进行可追溯学习、维护 .paper_ai/knowledge/02breakdown/ 和单文件专业术语库、默认同步输出中文翻译版，并根据 paper-chat 的指导重读和修正 study 结果。触发词包括：papers-study、学习 MinerU 结果、建立文献知识库、单篇论文学习、积累术语、更新术语库、修正 study、重读文献、同步 chat 指导。
---

# Papers Study

Papers Study 是知识库和术语库执行层。它负责读文献、积累可复用知识、积累专业术语，并根据 `paper-chat` 的指导修正。

它不负责培养用户人格，不负责模仿用户表达，也不负责总调度。

## 核心目标

默认只建立单篇论文资产：

1. 单篇文献知识库：这篇文献真正做了什么、证据支持到什么程度、对后续研究和写作有什么用。
2. 单文件专业术语资产库：按论文模块积累术语、推荐译法和原句。
3. 中文阅读版：默认同步输出 study 的忠实中文翻译，便于用户阅读。
4. 修正版 study：在 `paper-chat` 指导下，根据用户批评和原文证据修正旧理解。

不要默认做每批次总结、跨论文综合、综述笔记或总览。只有用户明确要求“总结”“综合”“对比这几篇”“生成总览”时，才创建跨论文总结文件。

## 输入来源

优先读取：

```text
.paper_ai/knowledge/01mineru/
.paper_ai/knowledge/00papers/paper_file_map.md
.paper_ai/dialogue/chat_to_study_guidance.md
.paper_ai/profile/user_term_preferences.md
```

如果 MinerU 输出缺失或不可读，标记为无法确认，不要编造。

## 输出目录

文献知识库：

```text
.paper_ai/knowledge/02breakdown/
  journal_articles/
  reviews/
  theses/
```

中文翻译版：

```text
.paper_ai/knowledge/02breakdown_中文/
  journal_articles/
  reviews/
  theses/
```

专业术语资产库只使用一个 Markdown 文件：

```text
.paper_ai/knowledge/03terminology/terminology.md
```

专业术语中文翻译版：

```text
.paper_ai/knowledge/03terminology_中文/terminology.md
```

可以创建缺失文件。不得覆盖用户已有内容；追加新条目或生成新版本。

## 学习方式

不要套用固定大模板。AI 应根据文献类型自行决定阅读深度和输出结构，但必须满足以下底线：

- 事实必须能回到原文或 MinerU 输出。
- 区分原文事实、作者声称、AI 判断、用户理解、未验证假设。
- 记录证据边界：强证据、弱证据、作者推断、AI 推断、需要复核。
- 保留对用户后续讨论和写作有用的信息。
- 同步抽取专业术语和可复用表达。
- 默认只学习单篇论文；不要主动生成批次总结或跨论文综合。

## 中文同步输出

每次生成或更新 study 后，默认同步输出中文版本。中文版本必须忠实翻译/整理 study 结果，不补充新观点、不扩展新内容、不创新机制解释。

如果终端可用 `claude`、`gemini`、`qwen` 等非 Codex CLI，并且用户授权可调用外部模型，则优先让这些工具翻译 Codex 生成的 study 结果。调用时必须明确：

- 只翻译或中文化 Codex 的 study 结果。
- 不补充、不扩写、不新增文献判断。
- 不改变证据边界。
- 保留 Paper ID、来源路径、原文事实、作者声称、AI 判断、未验证假设。
- 输出到中文目录，不覆盖英文/原始 study。

如果外部 CLI 不可用、未授权或调用失败，则由 Codex 本地生成中文版本，并在最终回复中说明。

## 文献类型

### Journal Articles

放入：

```text
.paper_ai/knowledge/02breakdown/journal_articles/
```

重点积累：

- 研究问题。
- 方法和数据。
- 关键结果。
- 作者声称和证据边界。
- 可能过度解释的地方。
- 对用户研究或写作有用的点。
- 专业术语和高质量表达。

### Reviews

放入：

```text
.paper_ai/knowledge/02breakdown/reviews/
```

综述不当作一手实验事实。重点积累：

- 领域共识。
- 争议和未解决问题。
- 机制框架。
- 背景写作素材。
- AI 原本不了解或容易混淆的知识。
- 综述中的术语体系。

### Theses

放入：

```text
.paper_ai/knowledge/02breakdown/theses/
```

学位论文不做过细事实分析。重点学习：

- 章节组织。
- 研究问题展开顺序。
- 方法、结果、讨论的排版和逻辑。
- 可借鉴的论文结构。

## 术语库规则

专业术语是核心长期资产，和文献知识同等重要。术语库只维护一个文件：`.paper_ai/knowledge/03terminology/terminology.md`。

单个术语库文件按论文模块组织：

```markdown
# Terminology

## Paper ID / Title

### 引言
| Term | 中文翻译 | 原句 |
|---|---|---|

### 方法
| Term | 中文翻译 | 原句 |
|---|---|---|

### 结果讨论
| Term | 中文翻译 | 原句 |
|---|---|---|

### 结论
| Term | 中文翻译 | 原句 |
|---|---|---|
```

规则：

- 每篇论文在一个 `Paper ID / Title` 区块内积累术语。
- 每个区块必须包含“引言、方法、结果讨论、结论”四个模块。
- 每个模块单独统计专业术语；同一论文内已出现过的术语不要重复积累。
- `Term` 如果有缩写，写成 `full term (ABBR)`；没有缩写就只写原术语，不要强行加括号。
- 必须保留中文翻译和原句，原句用于追溯语境。
- 不再默认维护 `core_terms.md`、`term_contexts.md`、`translation_choices.md`、`confusable_terms.md`、`writing_phrases.md` 多文件术语库；除非用户明确要求拆分。
- 不要把术语脱离原文语境机械翻译。

## 接收 paper-chat 指导

当 `.paper_ai/dialogue/chat_to_study_guidance.md` 存在或用户要求“按 chat 指导修正 study”时：

1. 读取指导文件。
2. 回到 MinerU 输出或已有 breakdown 复核。
3. 判断用户意见哪些被原文支持，哪些只是用户假设。
4. 修正文献知识库。
5. 同步修正单文件术语库，尤其是翻译、概念边界和写作表达。
6. 在新条目中记录“根据 chat 指导修正”，不要无痕覆盖旧判断。

## 写作前提供材料

当 `paper-ai` 或用户要求为写作提供材料时：

1. 汇总相关文献知识。
2. 汇总相关术语、推荐译法、不可混用概念。
3. 汇总可复用写作表达。
4. 明确哪些结论可写成事实，哪些只能写成作者观点或讨论假设。

这些材料交给 `nature-writing` 使用；不要自己替代写作 skill。

## 不要做

- 不要默认生成每批次总结、跨论文综合、总览或综述笔记。
- 不要规定 AI 必须按固定章节模板学习每篇论文。
- 不要把用户观点直接写成原文事实。
- 不要忽略术语积累。
- 不要默认拆分多个术语库文件。
- 不要把综述当作一手实验结果。
- 不要把学位论文当作高强度证据来源。
- 不要为了完整而补不存在的方法、数据、图表、结论或引用。
- 不要让术语脱离原文语境。
- 不要覆盖旧 study 结果而不记录修正来源。
- 不要让 Claude 或其他外部 CLI 对 study 进行补充、创新或重新解读；它们只负责忠实中文翻译。

## 质量检查

输出前检查：

- 是否建立或更新了单篇文献知识库。
- 是否避免了用户未要求的批次总结或跨论文综合。
- 是否建立或更新了单文件术语库 `terminology.md`。
- 是否同步输出中文翻译版。
- 如果使用外部 CLI 翻译，是否明确禁止补充和创新。
- 是否保留来源 Paper ID 或可追溯路径。
- 是否区分事实、作者声称、AI 判断和用户理解。
- 是否把 `paper-chat` 的指导落实到 study 修正，而不是只复述用户意见。
