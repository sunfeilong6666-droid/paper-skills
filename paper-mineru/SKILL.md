---
name: paper-mineru
description: PDF 文献解析准备与 MinerU 调度 skill。用于复制和重命名 PDF、配置 MinerU、生成解析命令、处理本地模型代理绕过、记录 GPU/模型/虚拟环境设置，并把解析输出放入 项目根目录下的 .paper_ai/knowledge/01mineru/。触发词包括：paper-mineru、MinerU、解析 PDF、运行 MinerU、学习这个 PDF 文件夹、PDF 重命名、生成 MinerU 命令。
---

# Paper MinerU

Paper MinerU 只负责 PDF 解析前后的工程流程。它不总结论文、不建立知识库、不判断用户阅读习惯。



## 输入信息
配置信息路径：
<项目根目录>/.paper_ai/config/source_paths.md
<项目根目录>/.paper_ai/config/mineru_config.md
解析前必须暂停询问用户是否开启了代理或 VPN

配置信息文件不存在或配置信息不完整时，完整信息包括如下：

- PDF 文件夹路径。
- MinerU 虚拟环境名称。
- 当前 shell：PowerShell、Windows CMD、Linux/macOS shell。
- 模型来源：本地模型、API、远程服务、不确定。
- 是否使用代理或 VPN。
- GPU 型号和显存；如果用户不知道，可以先按高质量默认配置生成命令。
- 是否解析图片、表格、图注；默认是。

把回答写入：

```text
<项目根目录>/.paper_ai/config/source_paths.md
<项目根目录>/.paper_ai/config/mineru_config.md
```

已有配置时直接复用。只有用户明确要求改变路径、环境、模型、代理或 GPU 参数时才更新。

## 项目根目录规则

先解析项目根目录，再进行复制、配置和命令生成：

- 如果用户提供的是论文所在文件夹的绝对路径，则以该文件夹的上一级目录作为项目根目录。
- 如果用户提供的是单个 PDF 的绝对路径，则以该 PDF 所在目录的上一级目录作为项目根目录。
- 如果用户没有提供绝对路径，则以当前工作区或用户明确指定的项目目录作为项目根目录。

所有 `.paper_ai/...` 路径都必须展开为 `<项目根目录>/.paper_ai/...` 的绝对路径。将项目根目录、原始 PDF 来源路径、规范化 PDF 输入目录、MinerU 输出目录写入 `<项目根目录>/.paper_ai/config/source_paths.md`，后续步骤只复用这些绝对路径。

示例：用户提供 `F:\Research\coral_papers\论文`，则项目根目录是 `F:\Research\coral_papers`，PDF 输入目录是 `F:\Research\coral_papers\.paper_ai\knowledge\00papers`，MinerU 输出目录是 `F:\Research\coral_papers\.paper_ai\knowledge\01mineru`。

## PDF 管理
若子agent存在，则由子agent完成：
不得修改原始 PDF。

从用户指定文件夹收集 PDF，复制到：

```text
<项目根目录>/.paper_ai/knowledge/00papers/
```

复制后按递增序号重命名：

```text
P0001.pdf
P0002.pdf
P0003.pdf
```

维护映射文件：

```text
<项目根目录>/.paper_ai/knowledge/00papers/paper_file_map.md
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
<项目根目录>/.paper_ai/knowledge/00papers/
```

输出目录：

```text
项目根目录下的 .paper_ai/knowledge/01mineru/
```

默认生成高质量解析命令。MinerU 命令中的 `-p` 和 `-o` 必须使用上面解析出的绝对路径，不要使用相对 `.paper_ai/...`。

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
mineru -p "<项目根目录>\.paper_ai\knowledge\00papers" -o "<项目根目录>\.paper_ai\knowledge\01mineru" -m auto -b vlm-engine --image-analysis true
```

Windows CMD 示例：

```bat
set "NO_PROXY=localhost,127.0.0.1,::1"
set "no_proxy=localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p "<项目根目录>\.paper_ai\knowledge\00papers" -o "<项目根目录>\.paper_ai\knowledge\01mineru" -m auto -b vlm-engine --image-analysis true
```

Linux/macOS 示例：

```bash
export NO_PROXY="localhost,127.0.0.1,::1"
export no_proxy="localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p "<项目根目录>/.paper_ai/knowledge/00papers" -o "<项目根目录>/.paper_ai/knowledge/01mineru" -m auto -b vlm-engine --image-analysis true
```

将最终命令写入 `<项目根目录>/.paper_ai/config/mineru_config.md`。Windows CMD 配置下，可以在项目根目录生成 `run_mineru.bat`，但生成后仍需等待用户确认运行。

## 后台运行 MinerU

运行 MinerU 时，优先使用当前环境可用的后台执行与日志监控工具，而非只生成 bat 脚本。后台运行的目标：
- 用户无需手动操作脚本文件。
- Agent 可以持续监控进度和日志。
- 用户可以随时询问进度。
- 完成后可以根据进程状态、日志和输出文件综合判断结果。

执行方式示例：

```bash
cd "<项目根目录>" && "<MINERU_FULL_PATH>" -p ".paper_ai/knowledge/00papers" -o ".paper_ai/knowledge/01mineru" -m auto -b vlm-engine --image-analysis true 2>&1
```

在 Windows/Codex 环境中，可以使用 PowerShell 后台进程和日志文件，例如 `Start-Process` 启动解析，并把 stdout/stderr 写入 `<项目根目录>/.paper_ai/config/mineru_run.log`。任务启动后：
1. 记录进程 ID、日志路径和输出目录。
2. 告知用户任务已启动及预计时间。
3. 用户询问进度时，检查进程状态、日志尾部和输出目录。

进度查询方式：
- 用户问“进度如何”“跑完了吗”时，读取日志和输出目录展示已处理文件数和当前状态。
- 完成判断必须综合进程状态、日志、`paper_file_map.md`、规范化 PDF 数量和 Markdown 产物。
## 已知问题：localhost 健康检查超时

如果 MinerU 日志显示本地 `mineru-api` 已启动，例如 `Started local mineru-api at http://127.0.0.1:<port>`，但随后报错 `Timed out waiting for local mineru-api to become healthy`，优先判断为代理/VPN 接管了本机端口或 localhost 流量。

解决顺序：

1. 不要删除输出目录或批量清理文件。
2. 检查 `项目根目录下的 .paper_ai/knowledge/01mineru/` 是否已有 Markdown；没有产物时才重试。
3. 在同一个 shell 进程中显式设置 localhost 代理绕过后重跑：

```powershell
$env:NO_PROXY="localhost,127.0.0.1,::1"
$env:no_proxy="localhost,127.0.0.1,::1"
conda run -n <ENV_NAME> mineru -p "<项目根目录>\.paper_ai\knowledge\00papers" -o "<项目根目录>\.paper_ai\knowledge\01mineru" -m auto -b pipeline -f true -t true
```

4. 如果 `vlm-engine` 因本地 API 健康检查失败，可先用 `pipeline` 后端产出可学习 Markdown；不要为了追求高精度而阻塞 `paper-study`。
5. 把失败日志、代理判断、最终成功命令写入 `<项目根目录>/.paper_ai/config/mineru_config.md`。

## 完成条件

当用户告知 MinerU 已完成时：

1. 检查 `项目根目录下的 .paper_ai/knowledge/01mineru/` 是否存在输出。
2. 优先确认每篇论文是否有 Markdown 输出。
3. 将状态写回 `paper_file_map.md`。
4. 提醒后续交给 `paper-study` 建知识库和术语库。

## 不要做

- 不要总结论文。
- 不要判断论文贡献。
- 不要更新 .paper_ai/knowledge/02breakdown/ 或 03terminology/。
- 不要覆盖原始 PDF。
- 不要把很长的原始文件名直接交给 MinerU。



