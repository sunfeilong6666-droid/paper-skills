# Memory Distillation

本文件只在用户明确要求构建 skill、更新 skill、审查 candidate 或蒸馏科研习惯时读取。

## 目标

从多次阅读反馈、写作反馈、项目讨论和纠错中，提炼可复用、可测试、可迁移的科研行为规律，并将验证通过的规律压缩写入：

```text
<项目根目录>/.paper_ai/02memory/profile/skill.md
```

它蒸馏的是用户如何读文献、判断方法、解释结果、控制结论强度、使用术语、反对 AI 胡编和从文献迁移到自己的课题。

## 读取文件

```text
<项目根目录>/.paper_ai/02memory/project/project.md
<项目根目录>/.paper_ai/02memory/profile/read.md
<项目根目录>/.paper_ai/02memory/profile/write.md
<项目根目录>/.paper_ai/02memory/profile/candidate.md
<项目根目录>/.paper_ai/02memory/profile/skill.md
```

## 核心链条

```text
一次性反馈
  ↓
read.md / write.md / project.md
  ↓
candidate.md
  ↓
跨任务复现 + 生成力 + 用户特异性验证
  ↓
测试题验证
  ↓
skill.md
```

## candidate 规则

candidate 是待验证规律池，不是普通日志。

只有可能成为长期规律的内容才进入 candidate。单篇文献细节、单次实验结果、一次性表达修改、项目事实不得直接进入 candidate。

### ID 前缀

| 类型 | ID |
|---|---|
| 阅读规律 | `C-READ-001` |
| 写作规律 | `C-WRITE-001` |
| 证据规则 | `C-EVID-001` |
| 术语规则 | `C-TERM-001` |
| 反模式 | `C-ANTI-001` |
| 跨领域迁移 | `C-TRANSFER-001` |
| 综合科研心智模型 | `C-SKILL-001` |

### 状态

```text
seed / accumulating / ready_for_test / promoted / rejected / retained / conflicted / merged
```

### candidate 模板

```markdown
## C-[TYPE]-[编号] [候选规律名称]

### 状态
seed / accumulating / ready_for_test / promoted / rejected / retained / conflicted / merged

### 类型
阅读规律 / 写作规律 / 证据规则 / 术语规则 / 反模式 / 跨领域迁移 / 综合科研心智模型

### 候选规律
用一句话说明用户可能存在的稳定科研行为规律。

### 触发场景
- ...

### 具体执行方式
- ...

### 证据来源
- [YYYY-MM-DD] read.md：...
- [YYYY-MM-DD] write.md：...
- [YYYY-MM-DD] project.md：...
- [YYYY-MM-DD] 用户纠正：...

### 验证
- 跨任务复现：通过 / 部分通过 / 未通过
- 生成力：通过 / 部分通过 / 未通过
- 用户特异性：通过 / 部分通过 / 未通过

### 当前置信度
低 / 中 / 高

### 测试题
1. ...
2. ...
3. ...

### 测试结果
待测试 / 通过 / 未通过 / 部分通过

### 处理决定
保留 / 继续积累 / 准备测试 / 晋升 / 拒绝 / 合并

### 最后更新时间
YYYY-MM-DD
```

## 晋升条件

candidate 晋升到 `skill.md` 前，必须满足：

1. 多次出现；
2. 跨任务复现；
3. 有生成力；
4. 有用户特异性；
5. 能写成明确执行动作；
6. 通过测试；
7. 有明确边界；
8. 不与旧规则冲突，或冲突已处理。

证据最低要求：

```text
3 条支持证据
或 2 条强证据 + 用户明确要求长期执行
或 1 条强证据 + 测试表现出明显价值
```

## skill.md 写入格式

```markdown
## [规则名称]

### 模型类型
阅读模型 / 写作模型 / 证据模型 / 术语模型 / 反模式 / 跨领域迁移模型

### 核心规律
一句话说明该模型。

### 触发场景
- ...

### 执行动作
1. ...
2. ...
3. ...

### 默认输出要求
- ...

### 禁止行为
- ...

### 边界条件
- ...

### 来源 candidate
- C-XXX-XXX

### 最近更新
YYYY-MM-DD
```

## 优先蒸馏模型

优先考虑：

- 问题链阅读模型；
- 方法本质模型；
- 结果-判断分离模型；
- 证据链约束模型；
- 保守/激进双结论模型；
- 课题映射模型；
- 术语强度控制模型；
- 反胡编模型。

## 输出规范

```markdown
已完成本轮 memory 蒸馏：

### 新增/更新 candidate
- ...

### 晋升到 skill.md
- ...

### 暂不晋升
- ...

### 原因
- ...

### 需要继续观察
- ...
```

## 禁止行为

- 不读取 PDF 原文。
- 不直接写论文正文。
- 不替代 paper-study。
- 不替代 nature-writing。
- 不凭空推断用户偏好。
- 不把项目事实写成心智模型。
- 不把一次性反馈写进 `skill.md`。
- 不删除旧 candidate。
- 不无痕覆盖 `skill.md`。

