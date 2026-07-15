---
name: paper-ai
description: |
  文献系统总入口。用于学习论文、解析 PDF、讨论 study 的不足、纠正文献理解、积累术语、记录 memory、根据文献写作论文。用户只调用 paper-ai；paper-ai 负责确认项目根目录、维护 .paper_ai 运行结构、检查 gpt-5.4-mini 可用性，并调度 paper-mineru / paper-study / paper-memory / nature-writing。
---

# Paper AI

Paper AI 是用户唯一直接交流入口。

它只负责：

1. 确认项目根目录；
2. 维护 `.paper_ai/` 运行结构；
3. 判断用户意图；
4. 调度下游 skill；
5. 在高频对话后调用 paper-memory capture。

公共目录、模型分工和安全边界见：

```text
references/runtime-contract.md
```

## 启动检查

1. 必需 skill：

| Skill | 职责 |
|---|---|
| `paper-mineru` | PDF 复制、重命名、MinerU 解析调度 |
| `paper-study` | 从 MinerU 输出建立结构化文献资产 |
| `paper-memory` | 记录项目事实、阅读偏好、写作偏好和候选规律 |

缺少任何一个必需 skill，停止并告知用户。

2. 写作任务需要 `nature-writing`。只有进入写作路径时才检查。

3. 用户未指定项目根目录时，默认当前工作区。

4. 创建缺失 `.paper_ai/` 目录和空白配置文件；不得覆盖已有内容。

5. 检查 `gpt-5.4-mini` 可用性；不可用则停止并询问用户。


## 入口分流

| 用户意图 | 路径 | 例子 |
|---|---|---|
| 学习/复习 | path 01 | “学习这个 PDF 文件夹”“解析这些论文”“复习这几篇” |
| 讨论/纠正 | path 02 | “这个是什么意思”“这篇读得不对”“这个术语以后这样翻译” |
| 写/改论文 | path 03 | “根据这些文献写讨论”“写引言框架”“这里改得更像论文” |

除明确学习、写作外，模糊问题默认进入 path 02。

## path 01：学习路径

用户要求学习、参考、检索、借鉴某篇 PDF 或某个文献文件夹时：

1. 确认项目根目录和 `.paper_ai/` 运行结构。
2. 检查 `gpt-5.4-mini` 可用性。
3. 判断文件状态，有且只有三个状态：
   - 未复制/未有 MinerU 输出：进入 step 011。
   - 已有 MinerU 输出：将需要学习的，mineru解析后的md文件的绝对路径逐行写入 `study_wait.txt`，禁止 step 011，进入 step 012。
   - 已有 MinerU 输出，已有study输出：默认为复习，将需要学习的，mineru解析后的md文件的绝对路径逐行写入 `restudy_wait.txt`，禁止 step 011，进入 step 013。

### step 011：未解析

1. 将需要解析的 PDF 绝对路径逐行写入：

```text
<项目根目录>/.paper_ai/knowledge/00test/mineru_wait.txt
```

2. 调用 `paper-mineru`。

正式学习来源必须是 MinerU 输出。不得自行改用其他 PDF 解析器、OCR、在线工具、临时脚本全文抽取或模型直接读 PDF。

3. 用户确认解析完毕后，将mineru输出的md文件的绝对路径逐行写入：

```text
<项目根目录>/.paper_ai/knowledge/00test/study_wait.txt
```

4. 进入 step 012。

### step 012：学习文献

1. 逐行读取：

```text
<项目根目录>/.paper_ai/knowledge/00test/study_wait.txt
```

2. 每篇文章必须使用独立子 agent 调用 `paper-study`。

3. 主 agent 发送给子 agent 的命令格式：

```text
/paper-study 学习这篇文章，路径为 <绝对路径>
```

4. 子 agent 完成后，只向主 agent 返回：
   - 文章已学习结束；
   - 原文件路径；
   - study 输出路径；
   - 术语库是否更新；
   - 检索索引是否更新；
   - 失败原因，如有。

5. 主 agent 记录结果后，结束该子 agent 上下文，再启动下一篇。

6. 不得把多篇文章合并给同一个子 agent 精读。

7. 不得因队列很长而改成略读、批量总结或降低模板覆盖范围。

8. 如果上下文、时间或模型状态不足以继续保持单篇精读质量，停止批处理，保留未完成列表，不得伪装完成。

9. 学习结束后，删除 `study_wait.txt`中学习完的文献路径，只能删除这一个明确文件；不得批量清理目录。

### step 012：复习文献

1. 逐行读取：

```text
<项目根目录>/.paper_ai/knowledge/00test/restudy_wait.txt
```

2. 每篇文章必须使用独立子 agent 调用 `paper-study`。

3. 主 agent 发送给子 agent 的命令格式：

```text
/paper-study 学习这篇文章，路径为 <绝对路径>,输出的内容不得覆盖原有内容，在原有的文件底部添加此次学习的内容。
```

4. 子 agent 完成后，只向主 agent 返回：
   - 文章已学习结束；
   - 原文件路径；
   - study 输出路径；
   - 术语库是否更新；
   - 检索索引是否更新；
   - 失败原因，如有。

5. 主 agent 记录结果后，结束该子 agent 上下文，再启动下一篇。

6. 不得把多篇文章合并给同一个子 agent 精读。

7. 不得因队列很长而改成略读、批量总结或降低模板覆盖范围。

8. 如果上下文、时间或模型状态不足以继续保持单篇精读质量，停止批处理，保留未完成列表，不得伪装完成。

9. 学习结束后，删除 `restudy_wait.txt`中学习完的文献路径，只能删除这一个明确文件；不得批量清理目录。

## path 02：讨论和纠正路径

### step 021：讨论

触发例子：

```text
这个 xxx 是什么意思
这篇文章的结果部分我没看懂
这个机制有哪些证据
之前那几篇怎么看
```

流程：

1. 用 `gpt-5.4-mini` 检索：

```text
<项目根目录>/.paper_ai/03study/breakdown/
<项目根目录>/.paper_ai/03study/terminology/terminology.md
<项目根目录>/.paper_ai/03study/retrieval_index/index.md
<项目根目录>/.paper_ai/02memory/
```

2. 当前模型读取候选材料；必要时回看：

```text
<项目根目录>/.paper_ai/knowledge/01mineru/
```

3. 回复用户观点，区分：
   - 原文事实；
   - 作者判断；
   - AI 推断；
   - 用户偏好；
   - 缺失信息。

4. 如果用户表达了可复用偏好、关注点或术语习惯，调用 `paper-memory` 执行 capture。

### step 022：纠正

触发例子：

```text
你刚才这篇读得不对
图 3 应该更具体一点
这个术语以后这样翻译
这个结果不能这么解释
```

流程：

1. 默认不直接肯定用户。
2. 先检索 `.paper_ai/03study/`、`.paper_ai/02memory/`，必要时回看 MinerU 输出。
3. 判断用户观点是否被本地材料支持：
   - 支持：肯定用户，并调用相关 skill 追加修正。
   - 不支持：说明证据不足或冲突；必要时再搜索外部资料。
4. 调用 `paper-memory` capture 记录用户反馈。

网络信息证据强度：

```text
论文 > 官方资料 > 新闻 > 百科和其他网页
```

## path 03：写作路径

用户要求写论文、改论文、搭框架时：

1. 确认项目根目录和 `.paper_ai/` 运行结构。
2. 检查 `nature-writing` 是否可用。
3. 分层读取写作材料，避免 `write.md` 和全量 study 随长期积累挤占正文上下文：

默认完整读取：

```text
<项目根目录>/.paper_ai/02memory/profile/skill.md
```

按当前写作任务、章节、主题、引用需求和关键词检索相关条目，不默认全文读取：

```text
<项目根目录>/.paper_ai/03study/
<项目根目录>/.paper_ai/02memory/project/project.md
<项目根目录>/.paper_ai/02memory/profile/write.md
```

检索 `write.md` 时，优先按任务类型定位相关偏好，例如 Results、Discussion、Introduction、Methods、figure legend、Word 编辑、统计表达、术语强度、引用组织和结论强度。

检索 `.paper_ai/03study/` 时，只带入当前章节或论点需要的 study 片段、证据标签、术语和引用线索；不得把全量 study 目录一次性塞入上下文。

4. 调用 `nature-writing` 完成写作。
5. 如果用户对写作提出可复用反馈，调用 `paper-memory` capture。

## Memory Capture Gate

除用户明确要求不记忆之外，每次调用paper-ai时，都要调用 `paper-memory` capture

包括但不限于：

- “以后都这样”；
- 反复纠正同类问题；
- 术语翻译或格式偏好；
- 结果和判断的分离要求；
- 结论强度要求；
- 写作结构偏好；
- 对 AI 反模式的纠正。

不得把一次性事实修正误写成长期习惯。

