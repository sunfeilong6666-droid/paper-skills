---
name: paper-mineru
description: PDF 文献解析准备与 MinerU 调度 skill。用于复制和重命名 PDF、配置 MinerU、生成解析命令、处理本地模型代理绕过、记录 GPU/模型/虚拟环境设置，并把解析输出放入 .paper_ai/knowledge/01mineru/。触发词包括：paper-mineru、MinerU、解析 PDF、运行 MinerU、学习这个 PDF 文件夹、PDF 重命名、生成 MinerU 命令。
---

# Paper MinerU

Paper MinerU 只负责 PDF 解析前后的工程流程。它不总结论文、不建立知识库、不判断用户阅读习惯。

## 输入信息

首次使用或配置缺失时，询问用户：

- PDF 文件夹路径。
- MinerU 虚拟环境名称。
- 当前 shell：PowerShell、Windows CMD、Linux/macOS shell。
- 模型来源：本地模型、API、远程服务、不确定。
- 是否使用代理或 VPN。
- GPU 型号和显存；如果用户不知道，可以先按高质量默认配置生成命令。
- 是否解析图片、表格、图注；默认是。

把回答写入：

```text
.paper_ai/config/source_paths.md
.paper_ai/config/mineru_config.md
```

已有配置时直接复用。只有用户明确要求改变路径、环境、模型、代理或 GPU 参数时才更新。

## PDF 管理

不得修改原始 PDF。

从用户指定文件夹收集 PDF，复制到：

```text
.paper_ai/knowledge/00papers/
```

复制后按递增序号重命名：

```text
P0001.pdf
P0002.pdf
P0003.pdf
```

维护映射文件：

```text
.paper_ai/knowledge/00papers/paper_file_map.md
```

表格格式：

```markdown
| Paper ID | Normalized file | Original file name | Original path | Added date | Status | Notes |
|---|---|---|---|---|---|---|
```

避免重复复制。判断重复时优先检查原始路径、原始文件名、文件大小；必要时使用 hash。

## MinerU 命令

输入目录：

```text
.paper_ai/knowledge/00papers/
```

输出目录：

```text
.paper_ai/knowledge/01mineru/
```

默认生成高质量解析命令。不要运行 MinerU，除非用户明确说“运行 MinerU”“开始解析”“run MinerU”。

如果用户使用代理或 VPN，并且模型来源是本地模型、localhost API 或本地服务，在命令前加入：

```text
NO_PROXY=localhost,127.0.0.1,::1
no_proxy=localhost,127.0.0.1,::1
```

PowerShell 示例：

```powershell
$env:NO_PROXY="localhost,127.0.0.1,::1"
$env:no_proxy="localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p ".paper_ai\knowledge\00papers" -o ".paper_ai\knowledge\01mineru" -m auto -b vlm-engine --image-analysis true
```

Windows CMD 示例：

```bat
set "NO_PROXY=localhost,127.0.0.1,::1"
set "no_proxy=localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p ".paper_ai\knowledge\00papers" -o ".paper_ai\knowledge\01mineru" -m auto -b vlm-engine --image-analysis true
```

Linux/macOS 示例：

```bash
export NO_PROXY="localhost,127.0.0.1,::1"
export no_proxy="localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p ".paper_ai/knowledge/00papers" -o ".paper_ai/knowledge/01mineru" -m auto -b vlm-engine --image-analysis true
```

将最终命令写入 `.paper_ai/config/mineru_config.md`。Windows CMD 配置下，可以在项目根目录生成 `run_mineru.bat`，但生成后仍需等待用户确认运行。

## 完成条件

当用户告知 MinerU 已完成时：

1. 检查 `.paper_ai/knowledge/01mineru/` 是否存在输出。
2. 优先确认每篇论文是否有 Markdown 输出。
3. 将状态写回 `paper_file_map.md`。
4. 提醒后续交给 `papers-study` 建知识库和术语库。

## 不要做

- 不要总结论文。
- 不要判断论文贡献。
- 不要更新 `.paper_ai/knowledge/02breakdown/` 或 `03terminology/`。
- 不要在用户未明确同意时运行 MinerU。
- 不要覆盖原始 PDF。
- 不要把很长的原始文件名直接交给 MinerU。
