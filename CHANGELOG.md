# Changelog

本文件记录书童开发工作流（Shutong Dev Workflow）的版本变更。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

---

## [1.2] - 2026-07-25

### 新增

- **从零新建项目模式**：INIT 阶段（技术选型、目录结构、依赖规划、入口设计、配置设计、扩展预留）
- **DEPLOY 阶段**（prompts/09-deploy.md）：环境检查、依赖安装、构建打包、部署执行、冒烟测试、监控确认
- **逐层搭建机制**：配置层 → 工具层 → 入口层 → 业务层，每层验证通过再搭下一层
- **功能验收机制**：INIT 阶段定义验收条件，VERIFY 阶段对照检查
- **从零模式对话实录**（examples/example-session.md 案例2）：完整的"待办事项工具"搭建过程
- **模式选择说明**（prompts/00-setup.md）：开局选择"改造现有"或"从零新建"
- **README 更新**：双模式流程图、适用场景说明

### 修改

- 00-setup.md：新增模式选择步骤和模式B初始化补充指令
- 01-probe.md：新增 INIT 分支（从零模式执行AI和策略AI提示词）
- 02-design.md：新增分支C（从零模式架构设计）
- 03-contract.md：新增从零模式（数据模型设计、API接口设计、异常处理策略）
- 04-code.md：新增从零模式（逐层搭建：配置层→工具层→入口层→业务层）
- 05-verify.md：新增从零模式（功能验收标准）
- README.md：更新目录结构、阶段流程图、适用人群、核心原则、版本记录
- CHANGELOG.md：新增 v1.2 版本记录

---

## [1.1] - 2026-07-24

### 新增

- Git 安全保障（prompts/08-git-safety.md）
- 异常话术强化（prompts/07-exception-handling.md）
- PROBE 范围限定（代码库过大时优先探测 3-5 个文件）
- 执行纪律强化（绝对铁律初始化指令）

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
