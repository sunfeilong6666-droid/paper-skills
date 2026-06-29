# Paper AI - 可塑型科研论文学习与写作SKILLS


## 这是什么
让ai以用户的风格习惯阅读本地文献、积累知识、最后用于文章写作，核心思路：

1. **读** — AI 在本地解析你的 PDF 论文，逐篇建立知识库和术语库。
2. **辩** — 你指出 AI 读得不对的地方，AI 学习你的判断方式，修正下一次的阅读方向。
3. **写** — 基于积累的知识库、术语库和你的表达偏好，辅助学术写作。


## Skill 介绍

| Skill | 职责 |
|---|---|
| `paper-ai` | 总调度，与用户沟通的窗口，判断意图并分配任务 |
| `paper-mineru` | 将本地 PDF 论文转为 Markdown 格式，供 AI 阅读 |
| `paper-study` | 单篇论文的积累学习，建立文献知识库和专业术语库 |
| `paper-memory` | 记录用户的阅读习惯和判断偏好，指导 `paper-study` 修正学习方向 |
| `nature-writing` | 基于知识库和用户偏好进行学术写作 |

## 工作流程

```
学习论文 → 建立知识库和术语库 → 用户指出不足 → memory 学习用户判断
→ memory 指导 study 修正 → 术语库同步更新 → 论文写作时调用
```

## 用户配置

1. **本地部署 MinerU** — PDF 解析工具，详见 [MinerU](https://github.com/opendatalab/mineru)
2. **nature-skills** — 学术写作技能，详见 [nature-skills](https://github.com/Yuan1z0825/nature-skills)

## 快速使用
1. 创建一个文件夹A（名称自定义）
2. 创建一个子文件夹B（名称自定义）
3. 将所有pdf放入B中
4. 调用paper-ai，如：/paper-ai 学习文献，路径为XXX/B(绝对或相对路径)
5. 根据提示输入MinerU的信息（默认用MinerU解析，若未部署，则让ai跳过MinerU解析，直接读取pdf）
6. .paper_ai/knowledge/02breakdown记录了ai的阅读结果、.paper_ai/knowledge/03terminology记录了专业术语
7. 调用paper-ai修正，如：/paper-ai xxx应该xxx（也可以附上自己的文献学习笔记）


## 几句话

时至今日，已有蒸馏人物特征的 nuwa-skill、迭代进化的 darwin-skill、nature 写作风格的 nature-skills。但是由 AI 完成符合个人特点的科研任务仍存在困难：

- **蒸馏**：个人科研角度的数据量少，蒸馏难度大
- **阅读**：只能阅读 OA 文章以及非 OA 的摘要等
- **写作**：只有 nature 的写作风格，却没有自身研究领域的知识积累

因此本小白脑子一热，想让ai在读写文献时，具有用户思维习惯 ，paper-skills 应运而生。

受限于本人的水平能力与时间，skills 仍有许多不足：
- **模型差异**：不同的模型会产生不同的阅读结果
- **图表**：图与表的弱解读
- **写作**：不同的用户paper-memory不同，最后的写作结果有很大的差异（或许可以借用大佬的memory）

本想完善之后再开源，但转念一想，paper-skill 本身就是在与用户交流之中不断进步的。一千个人的眼中有一千个哈姆雷特，我也同样希望这个一般般的paper-skills可以成为属于用户自己的了不起的paper-skills。
由衷感谢nuwa-skill的作者，借鉴了nuwa-skill的逻辑，paper-memory也能实现“热插拔”，可以供不同的用户互相交流，只需要交换.paper_ai/profile即可。
最后祝愿初入科研的同学们，希望这个skill可以与你们共同学习、共同进步。
为此，本人于 2026 年 6 月 29 日开源 paper-skills，希望能为广大才俊们提供本人微不足道的思路。
