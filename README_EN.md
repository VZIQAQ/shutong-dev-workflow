# Shutong Dev Workflow

> English | [中文](shutong-dev-workflow.html)

> A complete anti-drift playbook for non-programmers using AI-assisted development v2.0

![MIT License](https://img.shields.io/badge/License-MIT-green.svg)

**Core principle: Unsure -> Stop -> Ask -> Record -> Code -> Answer**

---

## What is this?

A development workflow designed for **users who can't code but want to build projects with AI**. Through dual-AI collaboration (Strategy AI + Execution AI), it breaks vague requirements into executable staged operations, with clear prompts, review checklists, and status markers.

You don't need to know code - just **copy-paste** and **click confirm**.

---

## Quick Start

**Open `shutong-dev-workflow_en.html`, choose a mode, copy prompts stage by stage.**

```text
1. Open two AI windows
   - Window A (Strategy): Kimi / ChatGPT / Claude - review direction, generate instructions
   - Window B (Execution): Mimo Code / Cursor / Copilot - read code, modify code, verify

2. Choose your scenario (3 modes)
   - New Project: No code, build from scratch
   - Add Feature: Have code, adding new functionality
   - Fix Issue: Have code, something is broken

3. Copy prompts stage by stage, wait for confirmation at each step
   - Each prompt card has instructions (who to send to, when, expected result)
   - Strategy AI tells you where to go after review passes
```

---

## Three Modes

### New Project

For building from scratch with no existing code.

```text
Init -> Design -> Contract -> Code -> Verify -> Deploy -> Done
```

### Add Feature

For adding new features to an existing codebase.

```text
Probe -> Design -> Contract -> Code -> Verify -> Done
```

### Fix Issue

For fixing broken functionality in an existing codebase.

```text
Probe -> Diagnose -> Contract -> Code -> Verify -> Done
```

---

## Features

| Feature | Description |
|---------|-------------|
| **3 Modes** | New Project / Add Feature / Fix Issue |
| **Dual-AI** | Strategy AI reviews direction + Execution AI writes code |
| **Instructions** | Each prompt card shows: who, when, expected result, next step |
| **Copy Button** | One-click copy for prompts |
| **Notepad** | Requirements and Notes tabs, localStorage persistence |
| **Dark Theme** | Eye-friendly dark interface with mode-colored accents |
| **Checklists** | Manual review checkpoints embedded in each stage |

---

## File Structure

```
shutong-dev-workflow/
├── shutong-dev-workflow.html      # Chinese interactive page (main)
├── shutong-dev-workflow_en.html   # English version
├── README.md                      # Chinese README
├── README_EN.md                   # This file
├── prompts/                       # Prompt reference docs (Markdown)
│   ├── 00-setup.md ~ 09-deploy.md
│   └── en/                        # English prompts
├── docs/                          # Design documents
├── examples/                      # Example sessions
├── CHANGELOG.md                   # Changelog
└── LICENSE                        # MIT License
```

---

## Core Principles

1. **Strategy AI never touches code** - Only reviews direction and completeness
2. **One file at a time** - Prevents call chain breakage
3. **Status markers at every step** - Precise recovery when stuck
4. **When unsure, stop** - Any anomaly: stop immediately, wait for Strategy AI

---

## Versions

- **v2.0** - 3 modes, interactive HTML, notepad, instruction cards
- **v1.2** - New project mode (INIT/DEPLOY stages)
- **v1.1** - Git safety, exception handling hardening
- **v1.0** - Initial release (PROBE->DESIGN->CONTRACT->CODE->VERIFY->DONE)

---

## License

[MIT License](LICENSE) - Attribution: Shutong Protocol Contributors
