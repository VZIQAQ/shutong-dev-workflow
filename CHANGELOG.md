# Changelog

本文件记录书童开发工作流（Shutong Dev Workflow）的版本变更。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

---

## [1.0] - 2026-07-24

### 新增

- 7阶段完整工作流：PROBE → DESIGN → CONTRACT → CODE → VERIFY → DONE
- 双AI架构：策略AI（审查方向）+ 执行AI（写代码）
- 状态判断机制：A（全新）/ B（部分）/ C（已完成）
- 进度坐标化：`[PROBE_DONE]`、`[FILE_DONE]`、`[STEP X/N]`、`[ALL_DONE]`
- 异常处理速查表：违规话术、卡住话术
- 完整的提示词模板（prompts/ 目录）
- 对比分析文档（docs/comparison.md）
- 双AI设计原理文档（docs/why-two-ai.md）
- 完整对话实录示例（examples/example-session.md）

---

## [1.1] - 2026-07-24

### 新增

- Git 安全保障（prompts/08-git-safety.md）
- 异常话术强化（prompts/07-exception-handling.md）
- PROBE 范围限定（代码库过大时优先探测 3-5 个文件）
- 执行纪律强化（绝对铁律初始化指令）
