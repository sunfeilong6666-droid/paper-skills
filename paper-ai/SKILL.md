---
name: paper-ai
description: 文献系统总入口。用于学习论文、解析 PDF、讨论 study 的不足、温故旧文献、积累专业术语、根据文献写作论文。调度 paper-mineru、paper-study、paper-memory，并在写作时结合 nature-writing。触发词包括：paper-ai、学习论文、学习这个文件夹、解析 PDF、study 读得不对、纠正文献理解、温故、术语积累、根据这些文献写论文、写引言、写讨论。
---

# Paper AI

Paper AI 是文献系统的总入口。它不亲自解析 PDF、不亲自建立知识库、不亲自蒸馏用户人格；它负责判断用户意图，然后把任务交给合适的 skill。

触发 `paper-ai` 时，先检查当前对话上下文中是否已经明确记录“`paper-ai` 路由配置已读取且仍处于活跃会话”。如果没有该记录，则视为首次触发或状态未知，必须读取同目录下的 `model_routing.md`。读取后，必须在当前对话工作状态中明确记录“`paper-ai` 路由配置已读取且仍处于活跃会话”，然后遵守其中的主子 agent 分工和 token 控制规则。同一活跃会话内不要每轮重复读取。

只有在以下情况才重新读取 `model_routing.md`：新线程、新的 `paper-ai` 工作会话、上下文压缩后无法确认配置已读取、用户明确要求重载，或配置文件可能已修改。用户明确说“没有建议了”“没有了”“暂时结束”“不用继续”“完成了”等收束语时，释放当前路由状态；下一次调用 `paper-ai` 时重新读取。

核心闭环：

```text
study 初读 -> 积累知识和术语 -> 用户指出不足 -> memory 学习用户判断
-> memory 指导 study 修正 -> 术语库同步更新 -> 后期写作调用
```

## 稳定模块
### 主子 agent 与 skill 边界

本模块是不可被格式偏好覆盖的路由边界。`model_routing.md` 是会话级补充配置；如果它与本模块冲突，以本模块和下游 skill 的职责边界为准。

Skill 职责：

- `paper-mineru`：PDF 复制、重命名、MinerU 配置与解析命令。
- `paper-study`：文献知识库和专业术语资产库。
- `paper-memory`：用户阅读人格、辩证讨论、对 study 不足的诊断、给 study 的修正指导。
- `nature-writing`：论文写作工具；写作时必须接收 study 的知识、术语库和 memory 的用户表达框架。

主 agent 职责：

- 负责意图判断、项目根目录确认、skill 路由、跨 skill 顺序控制和最终质量检查。
- 调用 `paper-study` 时，必须要求其主 agent 亲自完成论文学习、证据判断和正式写入；不得把学习任务分派给子 agent。
- 调用 `paper-memory` 时，必须要求其主 agent 亲自完成用户判断提炼、证据边界区分和给 study 的指导；不得把用户阅读人格学习交给子 agent。
- 不要让一个 skill 代替另一个 skill 的职责。尤其不要让 `paper-memory` 把用户观点直接写成文献事实，也不要让 `paper-study` 模仿用户人格。

子 agent 边界：

- 子 agent 只能用于低风险辅助，例如文件扫描、日志汇总、路线检查、格式检查、中文镜像翻译、遗漏项检查。
- 子 agent 不得替代任何 paper skill 的核心职责，不得学习论文，不得判断证据，不得学习用户人格，不得生成正式 study、正式术语库或正式 profile。
- 子 agent 的输出不能直接写入 `.paper_ai/` 的正式知识库、术语库或 profile；必须由对应主 agent 复核。



## 项目根目录与数据目录

先确定<项目根目录>，再展开所有后续工作路径：

- 如果用户提供的是论文所在文件夹的绝对路径，则以该文件夹的上一级目录作为<项目根目录>。
- 如果用户提供的是单个 PDF 的绝对路径，则以该 PDF 所在目录的上一级目录作为<项目根目录>。
- 如果用户没有提供绝对路径，则以当前工作区或用户明确指定的项目目录作为<项目根目录>。

项目级数据统一放在 `<项目根目录>/.paper_ai/`。后续 `paper-mineru`、`paper-study`、`paper-memory` 和写作流程都必须使用同一个项目根目录。

```text
<项目根目录>/.paper_ai/
  config/
    source_paths.md
    mineru_config.md
  knowledge/
    00papers/
    01mineru/
    02breakdown/
      journal_articles/
      reviews/
      theses/
    02breakdown_中文/
      journal_articles/
      reviews/
      theses/
    03terminology/
      terminology.md
    03terminology_中文/
      terminology.md
  profile/
    user_reading_lens.md
    user_evidence_rules.md
    user_term_preferences.md
    user_bad_ai_patterns.md
    user_expression_dna.md
  dialogue/
    study_feedback.md
    rereading_notes.md
    memory_to_study_guidance.md
```

如果目录或文件缺失，可以创建。不得覆盖用户已有内容；追加或生成新版本。

## 路由规则

### 学习论文或文件夹

用户说“学习论文”“学习这个文件夹”“解析 PDF”“更新文献库”时：

1. 调用 `paper-mineru`，确认 PDF 来源、MinerU 环境、模型来源、代理和 GPU 配置。
2. 如果 MinerU 尚未完成，只生成配置和命令，等待用户确认或告知解析完成。
3. MinerU 完成后，调用 `paper-study` 读取 `<项目根目录>/.paper_ai/knowledge/01mineru/`。
4. 要求 `paper-study` 同时更新文献知识库和专业术语资产库。

### 用户指出 study 不足

用户说“study 读得不对”“这个理解不对”“这段太空”“这个术语翻译不准”“这篇真正重要的是...”时：

1. 必须先调用 `paper-memory`，让它学习用户为什么认为 study 不足。
2. `paper-memory` 必须区分：原文事实、作者声称、study 错误、用户判断、待验证假设。
3. `paper-memory` 生成给 `paper-study` 的指导，写入 `<项目根目录>/.paper_ai/dialogue/memory_to_study_guidance.md`。
4. 再调用 `paper-study` 按指导重读、修正知识库和术语库。

此路径的目的不是单纯修 study，而是培养 memory，让 memory 后续能更好地指导 study 接近用户的阅读水平。

### 术语统一或术语积累

用户说“这个术语以后统一这样翻译”“积累术语”“这个概念不能混用”时：

1. 调用 `paper-memory` 记录用户偏好和判断依据。
2. 调用 `paper-study` 更新 `<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md`，并按需同步中文镜像 `<项目根目录>/.paper_ai/knowledge/03terminology_中文/terminology.md`。
3. 如果用户偏好与原文语境冲突，保留原文语境，并把用户偏好标成写作偏好而不是文献事实。

### 温故而知新

用户说“温故”“重读之前那几篇”“现在再看能不能读出新的东西”时：

1. 调用 `paper-memory` 读取旧讨论、旧规则、旧术语偏好和已有 study 结果。
2. 让 `paper-memory` 生成新的重读问题和修正方向。
3. 调用 `paper-study` 重读相关条目，更新知识库和术语库。

温故的重点是让 memory 随着用户的新理解更新自身，而不是机械复述旧总结。

### 论文写作

用户要求写引言、讨论、摘要、论文框架或完整论文时：

1. 调用 `paper-study` 提供相关文献知识和术语库。
2. 调用 `paper-memory` 提供用户阅读人格、表达偏好和证据边界提醒。
3. 调用 `nature-writing` 完成具体写作。
4. 写作前必须检查术语一致性，优先使用 `<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md`。

## 调度输出

每次调度时，先用一句话说明当前路线，例如：

```text
路线：paper-memory 先学习你指出的 study 不足，再指导 paper-study 修正文献知识库和术语库。
```

然后执行对应 skill。不要输出冗长路线图。

## 不要做

- 不要恢复旧的“有效交流论文数阈值”系统。
- 不要把所有步骤写成复杂阶段切换。
- 不要让 `paper-memory` 默认同意用户。
- 不要让 paper-study 把用户观点直接写成原文事实。
- 不要让子 agent 代替 paper-study 学习论文，或代替 paper-memory 学习用户判断。
- 不要在写作时绕过术语库。
- 不要把术语脱离原文语境机械翻译。
- 不要为了完整而填充无法从文献或用户材料支持的内容。
- 不要自己判断哪些任务"够简单"可以跳过路由。规则就是规则。

## 质量检查

完成任务前检查：

- 是否调用了正确 skill。
- 是否把用户对 study 的批评先交给 `paper-memory` 学习。
- 是否同步更新或要求更新术语库。
- 是否区分文献事实、作者声称、AI 判断和用户理解。
- 写作任务是否调用术语库。

