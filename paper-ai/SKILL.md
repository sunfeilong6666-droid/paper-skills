---
name: paper-ai
description: 文献系统总入口。用于学习论文、解析 PDF、讨论 study 的不足、温故旧文献、积累专业术语、根据文献写作论文。调度 paper-mineru、paper-study、paper-memory，并在写作时结合 nature-writing。触发词包括：paper-ai、学习论文、学习这个文件夹、解析 PDF、study 读得不对、纠正文献理解、温故、术语积累、根据这些文献写论文、写引言、写讨论。
---

# Paper AI

Paper AI 是文献系统的总入口。它不亲自解析 PDF、不亲自建立知识库、不亲自蒸馏用户人格；它负责判断用户意图，然后把任务交给合适的 skill。

触发 `paper-ai` 时：
- 先检查看当前对话上下文中是否已经明确记录“`paper-ai` 路由配置已读取且仍处于活跃会话”。如果没有该记录，则视为首次触发或状态未知，必须读取同目录下的 `model_routing.md`。读取后，必须在当前对话工作状态中明确记录“`paper-ai` 路由配置已读取且仍处于活跃会话”，然后遵守其中的主子 agent 分工和 token 控制规则。同一活跃会话内不要每轮重复读取。

- 再查看当前项目是否存在可复用的子 agent。若没有可复用子 agent，则根据 `model_routing.md` 的要求尝试创建子 agent；如果无法创建或无法复用子 agent，必须停止执行，向用户说明原因，并等待用户决定是指定子 agent 选用方式，还是改由主 agent 执行全部工作。

- 活跃路由配置是 `paper-ai` 调用 `paper-mineru`、`paper-study`、`paper-memory` 和写作 skill 时的上层约束；下游 skill 执行时必须继续遵守本文件的主子 agent 分工、token 控制和中文镜像规则，若该规则与下游 skill 的职责边界冲突，必须暂停执行，询问用户主子agent分配，随后将用户的分配方式写入`model_routing.md`。

- 只有在以下情况才重新读取 `model_routing.md`：新线程、新的 `paper-ai` 工作会话、上下文压缩后无法确认配置已读取、用户明确要求重载，或配置文件可能已修改。用户明确说“没有建议了”“没有了”“暂时结束”“不用继续”“完成了”等收束语时，释放当前路由状态；下一次调用 `paper-ai` 时重新读取。

核心闭环：

```text
study 初读 -> 积累知识和术语 -> 用户指出不足 -> memory 学习用户判断
-> memory 指导 study 修正 -> 术语库同步更新 -> 后期写作调用
```

## 稳定模块
### skill 边界

Skill 职责：

- `paper-mineru`：PDF 复制、重命名、MinerU 配置与解析命令。
- `paper-study`：文献知识库和专业术语资产库。
- `paper-memory`：用户阅读人格、辩证讨论、对 study 不足的诊断、给 study 的修正指导。
- `nature-writing`：论文写作工具；写作时必须接收 study 的知识、术语库和 memory 的用户表达框架。

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
  profile_中文/
    user_reading_lens.md
    user_evidence_rules.md
    user_term_preferences.md
    user_bad_ai_patterns.md
    user_expression_dna.md
  dialogue/
    study_feedback.md
    rereading_notes.md
    memory_to_study_guidance.md
  dialogue_中文/
    study_feedback.md
    rereading_notes.md
    memory_to_study_guidance.md
```

如果目录或文件缺失，可以创建。不得覆盖用户已有内容；追加或生成新版本。

## 路由规则

主 agent读取用户输入并判断真实任务：
- 用户说“学习论文”“学习这个文件夹”“解析 PDF”“更新文献库”等：进入 page01
- 用户说“study 读得不对”“这个理解不对”“这段太空”“这个术语翻译不准”“这篇真正重要的是...”时：进入 page02
- 用户说“这个术语以后统一这样翻译”“积累术语”“这个概念不能混用”时：进入 page03
- 用户说“温故”“重读之前那几篇”“现在再看能不能读出新的东西”时：进入 page01-3
- 用户要求写引言、讨论、摘要、论文框架或完整论文时：进入page04
### page01

MinerU解析
1. 主agent判断是否需要进行MinerU解析，如果需要进行，负责询问和撰写`paper-mineru`的配置要求，将配置文件的路径信息交予子 agent`record`，如果不需要进行MinerU解析，将需要学习的MinerU输出路径交给子 agent`record`，随后直接进入page01-2。
2. 由子 agent`record`执行`paper-mineru`，
3. 如果出现错误，子 agent：`record`向主agent输出一句错误信息，由主agent执行`paper-mineru`，并将错误和解决方案写入`paper-mineru`
4. 子 agent `record` 按当前环境允许的方式启动或监控后台运行。
5. `paper-mineru` 工作完成后，子 agent`record` 向主agent输出一句结束信息，待命。
6. 主agent收到子agent`record`的MinerU 完成信息后，进入page01-2

### page01-2
学习论文或文件夹
1. 由主agent进行学习，子agent`record`禁止学习
2. 主 agent 学习一篇文献前，子 agent `record`向主 agent 汇报已完成和未完成的学习、翻译与术语库镜像状态。
3. 当主 agent 完成并锁定一篇文献 analysis/study 后，让子 agent`translate` 把对应结果忠实翻译到 `<项目根目录>/.paper_ai/knowledge/02breakdown_中文/` 的同类目录中。
4. 当主 agent 完成或更新并锁定术语库 `<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md` 后，让子 agent`translate` 把对应术语库忠实翻译到 `<项目根目录>/.paper_ai/knowledge/03terminology_中文/terminology.md`。
5. 当主 agent 完成一篇文献的正式 study、术语库更新和中文镜像后，主 agent 遗忘该论文的长篇原文、逐段分析和中间推理，只保留索引级状态：Paper ID、输出路径、关键结论索引、证据边界摘要、术语更新状态、memory 指导落实状态、未解决问题和后续待办，防止大量 PDF 学习占用过多上下文。
6. 主 agent 不得遗忘正式文件路径、证据边界摘要、术语更新状态、memory 指导落实状态；后续需要重读时，必须回到 MinerU 输出、正式 study 或术语库重新读取，不能凭压缩记忆继续推断。
7. `02breakdown_中文` 和 `03terminology_中文` 只供用户阅读；后续任何科研判断、证据判断、术语边界判断和正式写作都不得从中文镜像文件继续阅读或推断，必须回到主 agent 生成的原始 study、术语库或原文证据。
8. 所有工作完成后，释放子 agent 上下文，保留子agent。

### page01-3
温故知新
1. 调用 `paper-memory` 读取旧讨论、旧规则、旧术语偏好和已有 study 结果。
2. 让 `paper-memory` 生成新的重读问题和修正方向。
3. 调用 `paper-study` 重读相关条目，更新知识库和术语库。

温故的重点是让 memory 随着用户的新理解更新自身，而不是机械复述旧总结。


### page02

用户指出 study 不足
1. 子 agent`record`搜索用户指出内容在主 agent 输出、study 文件、术语库中的位置，并将位置路径提供给主agent
2. 主 agent 调用 `paper-memory`。
3. `paper-memory` 生成给 `paper-study` 的指导，写入 `<项目根目录>/.paper_ai/dialogue/memory_to_study_guidance.md`。
4. 再调用 `paper-study` 按指导重读、修正知识库和术语库。
5. 主 agent 完成 memory 处理后，子 agent `translate` 负责忠实中文镜像翻译，把对应结果翻译到 `<项目根目录>/.paper_ai/profile_中文/` 和 `<项目根目录>/.paper_ai/dialogue_中文/` 的同类目录中
6. 子 agent `translate` 完成所有翻译后，主 agent 回复用户是否还有其他建议。
7. 如果用户说“没有建议了”“没有了”“暂时结束”“不用继续”“完成了”，则释放子 agent 上下文，保留子agent；

此路径的目的不是单纯修 study，而是培养 memory，让 memory 后续能更好地指导 study 接近用户的阅读水平。

### page03
术语统一或术语积累
1. 子 agent`record`搜索用户指出内容在主 agent 输出、study 文件、术语库中的位置，并将位置路径提供给主agent
2. 主agent调用 `paper-memory` 记录用户偏好和判断依据。
3. 调用 `paper-study` 更新 `<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md`，子 agent `translate`同步中文镜像 `<项目根目录>/.paper_ai/knowledge/03terminology_中文/terminology.md`。
3. 如果用户偏好与原文语境冲突，保留原文语境，并把用户偏好标成写作偏好而不是文献事实。



### 进入page04
论文写作
由主agent完成
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

