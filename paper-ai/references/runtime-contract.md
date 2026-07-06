# Paper AI Runtime Contract

本文件是新版 paper skills 的公共运行契约。各 skill 只在需要确认目录、模型或安全边界时读取本文件。

## 项目根目录


用户未指定时，默认当前工作区为 `<项目根目录>`。

所有 paper skills 必须使用同一个 `<项目根目录>`。

将项目根目录写入：

```text
<项目根目录>/.paper_ai/workspace_root.txt
```

不得把项目数据写入 skill 安装目录。

## 运行目录

```text
<项目根目录>/.paper_ai/
  00config/
    source_paths.md
    mineru_config.md
    model_routing.md
  01mineru/
    00papers/
    00test/
      mineru_wait.txt
      study_wait.txt
      paper_file_map.md
      mineru_run.txt
    01mineru/
  02memory/
    project/
      project.md
    profile/
      read.md
      write.md
      candidate.md
      skill.md
  03study/
    breakdown/
      journal_articles/
      reviews/
      theses/
      other/
    terminology/
      terminology.md
    retrieval_index/
      index.md
  04dialogue/
    correction_log.md
    memory_to_study_guidance.md
```

## 模型分工

- `gpt-5.4-mini`：文件检索、候选片段定位、批量筛选、相关 study 判断。
- 当前模型：正式阅读判断、证据边界判断、观点讨论、写作取材、最终回复。
- 如果必须使用 `gpt-5.4-mini` 但不可用，停止并询问用户；不要自动改用其他模型。

## 写入规则

- 可以创建缺失目录和缺失文件。
- 不覆盖用户已有内容。
- 更新旧文件时追加新条目、状态变化、冲突说明或废弃原因。
- 修正旧 study 时追加 `Revision` 区块，不无痕改写旧判断。
- 不把用户观点当成论文事实。
- 不把 MinerU 中文镜像或翻译稿作为证据判断来源。
- 不把项目事实写入用户稳定心智模型。

## 删除规则

禁止批量删除文件或目录。

不得使用任何递归删除、批量删除或按通配符删除的命令形式。

需要删除文件时，只能一次删除一个明确路径的文件。

如果需要清理多个文件或目录，停止操作，列出路径，请用户手动处理。


