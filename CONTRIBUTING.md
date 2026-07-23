# Contributing to Shutong Dev Workflow

> [中文版](#中文版) | English

Thank you for your interest in making this workflow programmable!

---

## The Problem

Right now, users must manually copy-paste prompts between two AI windows. This works, but it's tedious and error-prone.

## The Goal

Turn the prompts in `prompts/` into a programmable pipeline. The prompts are the spec — your tool wraps them so users interact with buttons/commands instead of copy-paste.

---

## What We're Looking For

### 1. CLI Tool

```bash
shutong init          # Initialize project config
shutong probe         # Run PROBE stage
shutong design        # Run DESIGN stage
shutong contract      # Run CONTRACT stage
shutong code          # Run CODE stage (one file at a time)
shutong verify        # Run VERIFY stage
shutong done          # Run DONE stage
shutong status        # Show current stage and progress
```

- Store state in `.shutong/state.json`
- Each command sends the corresponding prompt to the configured AI API
- Supports OpenAI, Anthropic, Ollama backends

### 2. VS Code Extension

- Side panel showing the 6-stage pipeline
- Click a stage → auto-paste prompt into the AI chat panel
- Status bar showing current stage
- Progress markers rendered as checkboxes

### 3. Web Dashboard

- Visual pipeline with stage nodes
- Real-time progress tracking
- Log viewer for [PROBE] markers
- Team collaboration (multiple users, shared state)

### 4. API / SDK

```python
from shutong import Pipeline

pipe = Pipeline(strategy_ai="openai/gpt-4", execution_ai="ollama/codellama")
pipe.probe(requirement="Add keyword extraction using AI")
pipe.design()
pipe.contract()
pipe.code(files=["keyword_extractor.py"])
pipe.verify()
pipe.done()
```

### 5. GitHub Action

```yaml
- uses: shutong/probe-action@v1
  with:
    requirement: "Extract keywords using AI model"
    strategy-ai: "openai/gpt-4"
    execution-ai: "ollama/codellama"
```

---

## How to Contribute

1. **Fork** this repository
2. **Pick** an idea above (or propose your own in an issue)
3. **Open an issue** to discuss your approach before coding
4. **Submit a PR** with your implementation
5. Make sure prompts in `prompts/` are used as the source of truth

---

## Design Principles

- **Prompts are the spec** — don't hardcode prompt text, read from `prompts/` directory
- **Bilingual** — support both `prompts/*.md` and `prompts/en/*_en.md`
- **State persistence** — save progress so users can resume after closing
- **Non-programmer friendly** — the tool should be easier than copy-paste, not harder
- **Backward compatible** — manual copy-paste should still work alongside the tool

---

## Questions?

Open an issue with the label `question`.

---

## 中文版

感谢你有兴趣把这个工作流变成可编程工具！

### 问题

目前用户必须在两个AI窗口之间手动复制粘贴提示词。能用，但繁琐且容易出错。

### 目标

把 `prompts/` 中的提示词变成可编程流水线。提示词就是规格说明，你的工具封装它们，让用户通过按钮/命令交互，而不是复制粘贴。

### 如何贡献

1. Fork 本仓库
2. 选一个方向（或在 issue 中提出你的想法）
3. 开 issue 讨论你的方案
4. 提交 PR
5. 确保 `prompts/` 中的提示词作为唯一来源

### 设计原则

- **提示词是规格说明** — 不要硬编码提示词文本，从 `prompts/` 目录读取
- **双语支持** — 同时支持 `prompts/*.md` 和 `prompts/en/*_en.md`
- **状态持久化** — 保存进度，关闭后可恢复
- **对非程序员友好** — 工具应该比复制粘贴更简单，而不是更复杂
- **向后兼容** — 手动复制粘贴应与工具并行工作
