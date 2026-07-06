---
name: paper-mineru
description: PDF 文献解析准备与 MinerU 调度 skill。用于复制和重命名 PDF、配置 MinerU、生成解析命令、处理本地模型代理绕过、记录 GPU/模型/虚拟环境设置，并把解析输出放入 项目根目录下的 .paper_ai/01mineru/01mineru/。触发词包括：paper-mineru、MinerU、解析 PDF、运行 MinerU、学习这个 PDF 文件夹、PDF 重命名、生成 MinerU 命令。
---

# Paper MinerU

Paper MinerU 只负责 PDF 解析前后的工程流程。它不总结论文、不建立知识库、不判断用户阅读习惯。



## 输入信息
配置信息路径：
.paper_ai/00config/source_paths.md
.paper_ai/00config/mineru_config.md
解析前必须暂停询问用户信息
信息包括如下：

- MinerU 虚拟环境名称（conda）。
- 是否授权开启终端：只能yes或者no
- 模型来源：本地模型、API、远程服务、不确定。
- 是否使用代理或 VPN：只能yes或者no
- GPU 型号和显存；如果用户不知道，可以先按高质量默认配置生成命令。

把回答写入：

```text
.paper_ai/00config/source_paths.md
.paper_ai/00config/mineru_config.md
```

已有配置时直接复用。只有用户明确要求改变路径、环境、模型、代理或 GPU 参数时才更新。


## PDF 管理

不得修改原始 PDF。

从`.paper_ai/01mineru/01mineru/mineru_wait.txt`中读取所有 PDF的路径，将对应文件复制到：

```text
.paper_ai/01mineru/00papers/
```

复制后按递增序号重命名：

```text
P0001.pdf
P0002.pdf
P0003.pdf
```

临时文件夹目录

```text
.paper_ai/01mineru/00test/
```
若不存在则构建。

维护映射文件：

```text
.paper_ai/01mineru/00test/paper_file_map.md
```

表格格式：

```markdown
| Paper ID | Normalized file | Original file name | Original path | Added date | Status | Notes |
|---|---|---|---|---|---|---|
```



## MinerU 命令

输入目录：

```text
.paper_ai/01mineru/00papers/
```
若不存在则构建。

输出目录：

```text
.paper_ai/01mineru/01mineru/
```

默认生成高质量解析命令。

如果用户使用代理或 VPN，并且模型来源是本地模型、localhost API 或本地服务，在命令前加入：

```text
NO_PROXY=localhost,127.0.0.1,::1
no_proxy=localhost,127.0.0.1,::1
```
读取存放项目根目录的`.paper_ai/01mineru/00test/work.txt`文件


在桌面打开终端，**必须是长时间显示的窗口**。
禁止使用powershell
将最终命令写入 `.paper_ai/01mineru/00test/mineru_run.txt`
**逐行**读取`.paper_ai/01mineru/00test/mineru_run.txt`，**逐行**向终端输入对应的代码，


Windows CMD 示例：

```bat
set "NO_PROXY=localhost,127.0.0.1,::1"
set "no_proxy=localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p "<项目根目录>\paper_ai\01mineru\00papers" -o "<项目根目录>\.paper_ai\01mineru\01mineru" -m auto -b vlm-engine --image-analysis true
```

Linux/macOS 示例：

```bash
export NO_PROXY="localhost,127.0.0.1,::1"
export no_proxy="localhost,127.0.0.1,::1"
conda activate <ENV_NAME>
mineru -p "<项目根目录>\paper_ai\01mineru\00papers" -o "<项目根目录>\.paper_ai\01mineru\01mineru" -m auto -b vlm-engine --image-analysis truee
```

列举需要删除的文件夹绝对路径，随后暂停，向用户请求授权删除文件夹的权限以及确认解析情况。**此授权位于最高级别**

**检查**
查看映射文件的文件名全部更改成功，没有遗漏后，删除 `.paper_ai/01mineru/00papers/`和`.paper_ai/01mineru/00test/`



## 不要做

- 不要总结论文。
- 不要判断论文贡献。
- 不要更新 .paper_ai/01mineru/02breakdown/ 或 03terminology/。
- 不要覆盖原始 PDF。
- 不要把很长的原始文件名直接交给 MinerU。




