# Shutong Dev Workflow（书童开发工作流）

> [English](README_EN.md) | 中文

> 非程序员的AI辅助开发防偏航完整剧本

![MIT License](https://img.shields.io/badge/License-MIT-green.svg)

**核心主张：不确定 → 停 → 问 → 记 → 编 → 答**

---

## 这是什么？

一套专为**不会编程但想用AI改造项目**的用户设计的开发工作流。通过「策略AI + 执行AI」双窗口协作，把模糊需求拆解为可执行的阶段化操作，每一步都有明确的提示词、审查清单和状态标记。

你不需要懂代码，只需要会**复制粘贴**和**点确认**。

---

## 要解决的问题

AI 编程最隐蔽的坑不是报错，而是**需求漂移** — 代码能跑，但方向错了。

具体表现为：
- 改着改着偏离了最初的需求，AI 自己不会回头检查
- 在错误的假设上堆代码，越改越离谱
- 一次改多个文件导致调用链断裂，表面上没报错，底层逻辑已经坏了
- 用户不懂代码，无法判断 AI 走没走偏

**单 AI 无法自检这个问题** — 它不会质疑自己的输出。本工作流通过「策略AI 审方向 + 执行AI 写代码」的双 AI 分工，在每个阶段设置检查点，从根本上防止需求漂移。

---

## 快速开始（3步）

```text
1. 打开两个AI窗口
   - 窗口A（策略层）：Kimi / ChatGPT / Claude — 审查方向、生成指令
   - 窗口B（执行层）：Mimo Code — 查代码、改代码、运行验证

2. 从 prompts/ 目录按阶段复制提示词
   - 每个阶段有【执行AI提示词】和【策略AI提示词】
   - 把 {{占位符}} 替换成你的实际内容

3. 按剧本一步一步执行，每步等确认
   - 用户没说"确认"，执行AI绝不进入下一阶段
   - 每步有状态标记（如 [PROBE_DONE]），卡住时精准恢复
```

---

## 目录结构

```text
shutong-dev-workflow/
├── LICENSE                          # MIT 许可证
├── README.md                        # 本文件（中文）
├── README_EN.md                     # English version
├── CONTRIBUTING.md                  # 贡献指南（英文优先）
├── CHANGELOG.md                     # 版本记录
├── CHANGELOG_EN.md                  # English changelog
├── prompts/                         # 各阶段提示词（按编号顺序使用）
│   ├── 00-setup.md                  # 开局初始化
│   ├── 01-probe.md                  # PROBE：摸清现状
│   ├── 02-design.md                 # DESIGN：冻结架构
│   ├── 03-contract.md               # CONTRACT：接口契约
│   ├── 04-code.md                   # CODE：单文件推进
│   ├── 05-verify.md                 # VERIFY：运行验证
│   ├── 06-done.md                   # DONE：清理完成
│   ├── 07-exception-handling.md     # 异常处理速查表
│   ├── 08-git-safety.md            # Git 安全保障
│   └── en/                          # English prompts
│       ├── 00-setup_en.md
│       ├── 01-probe_en.md
│       ├── 02-design_en.md
│       ├── 03-contract_en.md
│       ├── 04-code_en.md
│       ├── 05-verify_en.md
│       ├── 06-done_en.md
│       ├── 07-exception-handling_en.md
│       └── 08-git-safety_en.md
├── docs/                            # 设计文档
│   ├── comparison.md                # 与现有方案对比
│   ├── why-two-ai.md               # 为什么需要双AI
│   └── en/                          # English docs
│       ├── comparison_en.md
│       └── why-two-ai_en.md
└── examples/
    ├── example-session.md           # 完整对话实录示例
    └── en/
        └── example-session_en.md    # English example session
```

---

## 阶段流程

```text
┌─────────┐     ┌─────────┐     ┌──────────┐     ┌──────┐     ┌────────┐     ┌──────┐
│  PROBE  │────▶│ DESIGN  │────▶│ CONTRACT │────▶│ CODE │────▶│ VERIFY │────▶│ DONE │
│ 摸清现状 │     │ 冻结架构 │     │ 接口契约  │     │ 单文件│     │ 运行验证 │     │ 清理  │
└─────────┘     └─────────┘     └──────────┘     └──────┘     └────────┘     └──────┘
     │                │                                                  │
     │          ┌─────┴─────┐                                            │
     │          ▼           ▼                                            │
     │     状态A(全新)  状态B(部分)                                       │
     │     从零改造      诊断修复──────────────────────────────────────────┘
     │                                                                      ▲
     └──────────────────────────────────────────────────────────────────────┘
                              VERIFY失败时回退到CODE
```

每个阶段：
- 执行AI产出内容 + 状态标记（如 `[PROBE_DONE]`）
- 用户复制给策略AI审查
- 策略AI生成下一阶段指令
- 用户复制给执行AI执行

---

## 与现有方案对比

| 特性 | 单AI工具（Cursor/Copilot） | 本工作流 |
|------|---------------------------|---------|
| AI架构 | 单AI自问自答 | 双AI纠偏（策略+执行） |
| 防偏航 | 无，AI可能方向偏离 | 策略AI审查每步输出 |
| 进度管理 | 无 | 进度坐标化（[STEP X/N]） |
| 非程序员友好 | 需要一定编程基础 | 纯复制粘贴+确认 |
| 异常恢复 | 重启对话 | 从指定步骤继续 |
| 版本控制 | 无 | Git保障（可回退） |
| 接口契约 | 无 | CONTRACT阶段冻结 |

详见 [docs/comparison.md](docs/comparison.md)

---

## 适用人群

- **不会编程**但想用AI改造项目的用户
- 有想法但不知道怎么跟AI沟通的用户
- 之前用AI改代码改崩过、想有安全保障的用户
- 需要可重复、可回退的开发流程的团队

---

## 核心原则

1. **PROBE是状态传感器** — 不是"查一下"，而是决定"从零建"还是"排故障"
2. **进度标记是防卡核心** — 让"卡住→停止→继续"有精准坐标
3. **策略AI不碰代码** — 审的是"有没有6个答案""有没有[PROBE]日志"，不是代码逻辑
4. **用户只做开关** — 确认、回退、下一步、从第X步继续，不需要懂代码
5. **方向偏离比报错更危险** — 系统正常运行但底层逻辑错误，只能靠PROBE+VERIFY层层验证
6. **不确定就停** — 任何阶段发现异常，立即停止，等策略AI判断

---

## 版本

- **v1.0** — 7阶段完整工作流（PROBE → DESIGN → CONTRACT → CODE → VERIFY → DONE）
- **v1.1** — Git保障、异常话术强化、PROBE范围限定、执行纪律强化

详见 [CHANGELOG.md](CHANGELOG.md) | [CHANGELOG_EN.md](CHANGELOG_EN.md)

---

## 参与贡献

本项目目前是一个**基于提示词的操作手册** — 用户通过复制粘贴来执行。能用，但全靠手动操作。

**欢迎有能力的开发者，把这个工作流变成可编程的工具。**

### 我们想做的工具

| 工具 | 描述 |
|------|------|
| **CLI 工具** | `shutong init`、`shutong probe`、`shutong verify` — 在终端里跑阶段 |
| **VS Code 插件** | 侧边栏阶段按钮、自动粘贴提示词、进度追踪 |
| **Web 仪表盘** | 可视化阶段流水线、进度概览、日志查看 |
| **API / SDK** | 把工作流作为中间层嵌入任何 AI 编码工具 |
| **GitHub Action** | PR 时自动跑 PROBE，push 时自动跑 VERIFY |

### 如何贡献

1. Fork 本仓库
2. 选一个上面的方向（或提出你自己的想法）
3. 开 issue 讨论你的方案
4. 提交 PR

`prompts/` 目录中的提示词就是规格说明 — 你的工具应该把它们程序化封装，让用户不需要手动复制粘贴。

详见 [CONTRIBUTING.md](CONTRIBUTING.md)

---

## 许可证

[MIT License](LICENSE) — 署名：书童协议贡献者
