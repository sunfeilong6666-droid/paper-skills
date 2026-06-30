# Model Routing

仅规定主子agent的配置，不进行子agent的创建

## 稳定模块


本模块是所有 paper skill 的上层边界，不随用户对 study 格式、术语字段、profile 字段或命令记录方式的偏好而改变。下游 `paper-study`、`paper-memory`、`paper-mineru` 中更具体的职责边界优先于本文件的通用节省 token 策略。
### 主 agent 边界
- 主 agent 保留科研判断、证据判断、用户阅读人格提炼、正式知识库写入和最终质量控制。




### 子 agent 输出格式

凡是调用子 agent，必须要求其输出非常简短的执行报告：

```markdown
### 子 agent 执行报告

- 执行任务:
- 输入来源:
- 修改/生成的文件:
- 未修改的文件:
- 发现的问题:
- 需要主 agent 判断的问题:
- 是否涉及论文理解或证据判断: 是/否
```

如果子 agent 的结果涉及论文理解、证据判断、术语边界或写作结论，主 agent 必须重新审核，不能直接采纳。

### token 控制原则

- 能用文件路径、索引、摘要定位的，不重复读取全文。
- 能让子 agent 做的工程性任务，不让主 agent 消耗长上下文。
- 主 agent 只读取与判断有关的关键段落、图表、已有 study、memory 指导和术语库。
- 子 agent 只处理明确、局部、可检查的任务。
- 主 agent 不为了“完整”而读取无关文件。
- 不为了节省 token 而牺牲证据边界。

## 可变模块
### 模型分工与 token 控制

Paper AI 必须在不改变原有 skill 职责边界的前提下进行模型分工，以降低 token 消耗。


主 agent：

- 角色：科研判断 / 证据判断 / 用户阅读人格提炼 / 最终质量控制。
- 以用户当前的模型作为主agent

子 agent：

- name: record
- description：文件扫描 / PDF 管理 / Markdown 整理 / 日志汇总。
- model：GPT-5.4-mini。
- model_reasoning_effort：middle。

子 agent：

- name: translate
- description：忠实中文镜像翻译。
- model：GPT-5.4-mini。
- model_reasoning_effort：middle。

- 如果当前环境不支持指定的子模型，但能创建或复用更轻量的可用子模型，可以自动选择该子模型作为子 agent，并记录实际选用模型。
- 如果无法创建或复用子 agent，必须停止执行，向用户说明原因，并等待用户决定是指定子 agent 选用方式，还是改由主 agent 执行全部工作。
- 创建子 agent 后，必须立刻回复用户：已选用的子 agent 模型名称、用途。


主 agent 负责：

- 判断用户真实意图。
- 根据用户学习流程，为不同名称的子agent分配任务
- 判断文献事实、作者声称、AI 判断、用户理解和未验证假设的边界。
- 判断论文贡献、证据强弱、机制逻辑和写作价值。
- 判断 `paper-study` 是否读浅、过度解释、模板化或术语处理错误。
- 维护最终质量检查和最终回复。

主 agent 不应把需要科研判断、证据判断、用户阅读人格提炼的任务交给子 agent。



子 agent`record` 只负责低风险、可验证、可回滚的辅助任务，包括：

- 扫描目录和列出文件。
- 在主 agent 已确认项目根目录和规则后，辅助复制、重命名、清点 PDF，并把结果交主 agent 审核。
- 整理 MinerU 命令候选；最终命令必须由主 agent 审核。
- 检查 MinerU 输出是否存在。
- 整理 Markdown 格式。
- 检查术语库表格格式、重复项和缺失列。
- 检索 Markdown 文件中的内容并提供给主 agent 分析。
- 汇总文件路径、文件名、运行日志和状态。

子 agent`record` 不得负责：

- 判断机制是否成立。
- 判断图表结果支持到什么程度。
- 判断用户观点是否正确。
- 提炼用户阅读人格。
- 将用户观点写入文献事实。
- 改写 evidence boundary。
- 创造新的研究结论、机制解释、数据、方法或引用


子 agent`translate` 只负责对主 agent 已完成的 study 结果和术语库进行忠实中文镜像翻译




# 子agent删除方案
频繁的创建子agent会大量消耗token，因此第一次创建子token后，默认持续存在。
