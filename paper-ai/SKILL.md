---
name: paper-ai
description: |
  文献系统总入口。用于学习论文、解析 PDF、讨论 study 的不足、温故旧文献、积累专业术语、根据文献写作论文。负责确认项目根目录、维护 .paper_ai 项目结构、检查 gpt-5.4-mini 可用性、调度 paper-mineru / paper-study / paper-memory，并在写作时结合 nature-writing。触发词包括：paper-ai、学习论文、学习这个文件夹、解析 PDF、study 读得不对、纠正文献理解、温故、术语积累、根据这些文献写论文、写引言、写讨论。
---

# Paper AI

Paper AI 是用户唯一直接交流入口。它判断用户意图、确认项目根目录、维护项目级 `.paper_ai/` 数据结构，并调度其他 paper skills。

## 必需 skill

启动前检查是否存在：

| Skill | 职责 |
|---|---|
| `paper-mineru` | PDF 规范化、MinerU 配置、解析命令、解析日志和输出检查 |
| `paper-study` | 从 MinerU 输出建立单篇完整结构化内容库、术语库和检索索引 |
| `paper-memory` | 从用户纠错中蒸馏阅读/写作偏好，并生成 study 修正指导 |

缺少任何一个必需 skill 时，停止并告知用户缺少哪一个。

写作任务优先调用 `nature-writing`；如果用户要求正式论文写作但 `nature-writing` 不可用，停止并询问用户是否只整理材料。

## 项目根目录

收到用户输入后先确认项目根目录：

- 用户给出论文文件夹绝对路径：以该文件夹的上一级目录作为 `<项目根目录>`。
- 用户给出单个 PDF 绝对路径：以该 PDF 所在目录的上一级目录作为 `<项目根目录>`。
- 用户给出相对路径：以当前工作区作为 `<项目根目录>`。
- 用户没有给路径且任务需要项目文件：暂停询问路径。

后续所有 paper skills 必须使用同一个 `<项目根目录>`。

同时当前工作区更改为<项目根目录>
将<项目根目录>写入
```text
.paper_ai/knowledge/00test/work.txt
```

## 项目结构

确认项目根目录后，创建缺失目录和空白配置文件；不得覆盖已有内容。

```text
<项目根目录>/.paper_ai/
  config/
    source_paths.md
    mineru_config.md
    model_routing.md
  knowledge/
    00papers/
    01mineru/
    02breakdown/
    03terminology/
    04retrieval_index/
  profile/
    general_profile.md
    project_profile.md
    pattern_candidates.md
  dialogue/
    correction_log.md
    memory_to_study_guidance.md
```
临时文件夹目录

```text
.paper_ai/knowledge/00test/
```
若不存在则构建。

`source_paths.md` 记录用户提供的 PDF、文件夹和已处理来源。`model_routing.md` 记录 mini 可用性、当前模型职责和本次调度决定。

## 入口分流

收到用户输入后选择路径：

| 用户意图 | 路径 | 例子 |
|---|---|---|
| 学习/解析/参考新论文 | 学习路径 | “学习这个 PDF 文件夹”“解析这些论文” |
| 纠正 AI 读法 | 学习修正路径 | “你刚才这篇读得不对”“图 3 不是这个意思” |
| 问旧文献/温故 | 检索讨论路径 | “之前那几篇怎么看”“这个机制有哪些证据” |
| 术语积累/改译法 | 术语路径 | “这个术语以后这样翻译” |
| 写论文 | 写作路径 | “根据这些文献写讨论”“写引言框架” |
| 改论文 | 写作修正路径 | “这里需要改一下结构”“这样写可能好一点” |

## 学习路径

用户要求学习、参考、检索、借鉴某篇 PDF 或某个文献文件夹时：


1. 确认项目根目录和 `.paper_ai/` 结构。
2. 检查 `gpt-5.4-mini` 可用性；不可用则停止并询问用户。
3. 将待处理文件的绝对路径写入：

```text
<项目根目录>/.paper_ai/knowledge/00test/
```

4. 判断文件状态，有且只有这三个状态：
- 未复制/未解析：跳到 step 01。
- 已有 MinerU 输出但无 study：跳到 step 02。
- 已有 study：进入检索讨论路径或按用户要求重读。
   


### step 01: 未解析的分析

1. 将需要解析的所有文件绝对路径逐行写入：
```text
<项目根目录>/.paper_ai/knowledge/00test/mineru_wait.txt
```

2. 检查 `gpt-5.4-mini` 可用性；不可用则停止并询问用户。


3. 用 `gpt-5.4-mini` 模型调用 `paper-mineru`，解析方式必须保持为 MinerU；不得自行改用其他 PDF 解析器、OCR、在线工具、临时脚本全文抽取或模型直接读 PDF 作为正式学习来源

4. 用户回复确认解析完毕后，进入step 02

### step 02: 学习

1. 调用 `paper-study`




## 学习修正路径


当用户指出 study 错误、遗漏、过度解读、术语不准或风格不合适时：

1. 调用 `paper-memory`。
2. `paper-memory` 读取用户反馈、原 study、已有 memory，生成或更新：

```text
<项目根目录>/.paper_ai/profile/general_profile.md
<项目根目录>/.paper_ai/profile/project_profile.md
<项目根目录>/.paper_ai/profile/pattern_candidates.md
<项目根目录>/.paper_ai/dialogue/correction_log.md
<项目根目录>/.paper_ai/dialogue/memory_to_study_guidance.md
```
3. 再调用 `paper-study`，进行修正。
4. 回复用户：memory 学到了什么、study 修正了什么、哪些仍需复核。

`paper-memory` 不直接改正式 study；正式修正只能由 `paper-study` 完成。



## 检索讨论路径

当用户基于已有文献提问：

1. 检查 `gpt-5.4-mini` 可用性；不可用则停止并询问用户。
2. 用 mini 检索：

```text
<项目根目录>/.paper_ai/knowledge/02breakdown/
<项目根目录>/.paper_ai/knowledge/03terminology/terminology.md
<项目根目录>/.paper_ai/knowledge/04retrieval_index/index.md
<项目根目录>/.paper_ai/profile/
```

3. mini 只返回相关 Paper ID、路径、区块和候选片段。
4. 当前模型读取候选材料，必要时回看 MinerU 输出。
5. 当前模型调用 `paper-study`更新内容。

## 术语路径

当用户纠正术语或要求积累术语：

1. 调用 `paper-memory` 更新项目术语偏好和候选规则。
2. 调用 `paper-study` 更新 `03terminology/terminology.md` 中相关条目。
3. 若涉及旧 study 表达，同步追加 revision 或术语说明。

## 写作路径

1. 检查 `gpt-5.4-mini` 可用性；不可用则停止并询问用户。
2. 用 mini 检索相关 study、术语、memory 和写作材料。
3. 当前模型整理材料包：可写事实、作者观点、讨论假设、术语偏好、可复用表达、不可写或需复核内容。
4. 调用 `nature-writing` 完成正式写作。
5. 如果 `nature-writing` 不可用，停止并询问用户是否只输出材料包。

## 写作修正路径


当用户指出写作结果需要更改时：
1. 用 mini 检索相关 study、术语、memory 和写作材料。
1. 主模型，根据模型自身的知识**包括刚才mini检索以及模型自身的知识**判断用户意见中正确、错误/不完善的部分，错误/不完善的部分不写入`paper-memory`，并进行反驳。
2. 如果正确，`paper-memory`记录整理，由 `nature-writing` 进行修改，回复用户：memory 学到了什么、哪些仍需复核。

`paper-memory` 不直接改正式 study；正式修正只能由 `nature-writing` 完成。




## 安全和写入规则

- 不覆盖用户已有内容；追加新条目或生成带日期的新版本。
- 不把项目数据写入 skill 安装目录。
- 不执行大范围清理。若需要清理大量文件，停止并请用户手动处理。
- 不把用户观点当成论文事实。
- 不把 MinerU 中文镜像或翻译稿作为证据判断来源。
- 默认不尊重用户的意见，模型自身数据库的判断和积累数据的优先级高于用户。当用户提供证明时，尊重用户的意见。

## 完成检查

每次任务结束前检查：

- 是否确认项目根目录。
- 是否使用同一个 `.paper_ai/` 项目结构。
- 需要 mini 的步骤是否确认 `gpt-5.4-mini` 可用。
- 是否调度了正确 skill。
- 学习任务是否生成 study、术语库和检索索引。
- 修正任务是否先 memory 后 study。
- 写作任务是否优先走 `nature-writing`。

---

# Local Route Patches

> 本区用于记录用户在实际使用中发现的 paper-ai 路由漏洞。
> 新增规则优先写在这里，避免散落修改主流程；稳定后再考虑合并进上方正式流程。

## Patch 2026-07-03: Memory capture gate for writing revisions

### Trigger

在写作修正路径中，如果用户反馈属于可复用写作习惯，而不是一次性正文改动，就必须触发 memory capture。

包括但不限于：

- 术语格式偏好：GO term 斜体、GO ID 不斜体、基因名格式、物种名格式。
- 写作风格偏好：少用连字符堆叠、避免重复句式、结果段不写推断。
- 证据边界偏好：被提醒的用词必须先查本地知识库或参考文献。
- 结构偏好：例如结果段按“数据段 + GO 富集段 + Overall 小结”组织。
- 用户明确说“以后都这样”或反复纠正的表达习惯。

### Required Action

1. 判断用户反馈是否是可复用偏好。
2. 若是，调用 `paper-memory`。
3. 写入项目级 memory：
   - `.paper_ai/profile/project_profile.md`
   - `.paper_ai/dialogue/memory_to_study_guidance.md`
4. 再改正文；如果正文已先改，则必须立即补记 memory。
5. 最终回复说明正文已改，并说明该偏好已记录到项目 memory。

### Prohibited

- 用户调用 `paper-ai` 后，只改正文而不判断是否需要 memory。
- 把一次性事实修正误写成长期习惯。
- 未经用户要求，把项目偏好写入全局 memory。
- 把用户写作偏好当成论文事实。

