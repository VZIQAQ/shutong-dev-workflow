# Shutong Dev Workflow

> **A complete anti-drift playbook for non-programmers using AI-assisted development v2.0**

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**Core principle: Unsure -> Stop -> Ask -> Record -> Code -> Answer**

---

## What is this?

A development workflow designed for **users who can't code but want to build projects with AI**.

Through dual-AI collaboration (Strategy AI + Execution AI), it breaks vague requirements into executable staged operations. Every step has clear prompts, review checklists, and status markers.

**You don't need to know code - just copy-paste and click confirm.**

---

## Quick Start

### 1. Open the page

Double-click `shutong-dev-workflow.html` to open in browser (no server needed, no internet required).

### 2. Choose your scenario

```
🆕 New Project    -> No code, build from scratch
➕ Add Feature    -> Have code, adding new functionality
🔧 Fix Issue      -> Have code, something is broken
```

### 3. Open two AI windows

| Window | Role | Recommended Tools |
|--------|------|-------------------|
| Window A (Strategy) | Review direction, give instructions | Kimi / ChatGPT / Claude |
| Window B (Execution) | Write code, run verification | Mimo Code / Cursor / Copilot |

### 4. Follow the wizard

- The page shows **one step at a time**
- Each prompt card shows: **who to send to, when, expected result, next step**
- Click **Copy** to paste to AI
- After Strategy AI approves, click **Next** to continue

---

## Three Modes

### 🆕 New Project

For building from scratch with no existing code.

```
Init -> Design -> Contract -> Code -> Verify -> Deploy -> Done
```

- **Init**: Tech stack, directory structure, acceptance criteria
- **Code in 4 layers**: Config -> Utility -> Entry -> Business, verify each layer

### ➕ Add Feature

For adding new features to an existing codebase.

```
Probe -> Design -> Contract -> Code -> Verify -> Done
```

- **Insert branches anytime**: Halfway through and want to add something? Click "Insert New Requirement", complete it, then merge back to main flow

### 🔧 Fix Issue

For fixing broken functionality in an existing codebase.

```
Probe -> Design -> Contract -> Code -> Verify -> Done
```

- **Diagnosis-oriented**: PROBE focuses on locating what's broken, not understanding the entire codebase

---

## Features

| Feature | Description |
|---------|-------------|
| **Wizard-style** | One step at a time, prev/next navigation, no need to figure out which prompt to copy |
| **3 independent modes** | New Project / Add Feature / Fix Issue, theme colors auto-switch (green/blue/orange) |
| **Dual-AI review** | Strategy AI reviews direction + Execution AI writes code, must pass review to proceed |
| **Stage freezing** | CONTRACT stage freezes interfaces and data structures, code changes cannot exceed contract scope |
| **Branch insertion** | Insert "new requirement" branch at any stage, supports nesting, merge back when done |
| **Instruction cards** | Each prompt annotated: who, when, expected result, next step |
| **One-click copy** | Copy prompts to clipboard with one click, auto-embedding feature (e.g. document pre-review) |
| **Document pre-review** | Have an MVP document? Send it to Strategy AI for 10-dimension quality review before entering the workflow |
| **Checklists** | Manual review checkpoints at each stage bottom, reminders not blockers |
| **State persistence** | localStorage auto-saves progress, survives page refresh |
| **Export/Import** | Export JSON progress file for backup and restore |
| **Right-side notepad** | Requirements/Notes dual tabs, CRUD, text export |
| **Troubleshooting phrases** | Built-in stuck phrases, violation phrases, Git safety quick reference |
| **Keyboard shortcuts** | -> / Enter for next, <- for previous |
| **Responsive design** | Desktop, tablet, mobile adaptive |
| **Dark theme** | Eye-friendly dark interface |

---

## Usage Examples

### Scenario: Build a todo app from scratch

```
1. Open shutong-dev-workflow.html
2. Click 🆕 New Project
3. Step 1 "Init":
   -> Copy Execution AI prompt -> paste to Mimo -> get tech stack proposal
   -> Copy Strategy AI prompt -> paste to Kimi -> review passes
   -> Check the checklist -> click "Next"
4. Step 2 "Design": Design data flow and skeleton order
5. Step 3 "Contract": Confirm interface definitions and coding discipline
6. Step 4 "Code": Build in 4 layers (config -> utility -> entry -> business)
7. Step 5 "Verify": Check against acceptance criteria from Init
8. Step 6 "Deploy": Environment check, dependencies, deploy
9. Step 7 "Done": Complete docs, git archive
10. 🎉 Project done! Export report
```

### Scenario: Halfway through, want to add user login

```
While in "Code" step, click "Insert New Requirement"
-> Enter branch name "Add User Login"
-> Enter Add Feature mode, go through probe->design->contract->code->verify->done
-> After completion, "Merge Plan" appears, choose to return to main flow's "Code" stage
```

### Scenario: Have an MVP document, pre-review before starting

```
1. Click 🆕 New Project -> "📄 Have MVP Document"
2. Paste your MVP document
3. Click "Copy Review Prompt" (auto-embeds your document content)
4. Paste to Strategy AI (Kimi), get 10-dimension score
5. Based on score: A. Enter Design directly / B. Fill gaps / C. Start over
6. Enter the corresponding stage to begin workflow
```

---

## Core Principles

1. **Strategy AI never touches code** - Only reviews direction and completeness
2. **One file at a time** - Prevents call chain breakage
3. **Status markers at every step** - Precise recovery when stuck (e.g. [INIT_DONE], [CODE_DONE])
4. **When unsure, stop** - Any anomaly: stop immediately, wait for Strategy AI
5. **Contract freezing** - Interfaces cannot be changed after CONTRACT stage, changes require rolling back
6. **Layered building** - New projects build config -> utility -> entry -> business, dependencies first

---

## File Structure

```text
shutong-dev-workflow/
├── shutong-dev-workflow.html      # Chinese interactive page (main file, double-click to use)
├── shutong-dev-workflow_en.html   # English version
├── README.md                      # Chinese README
├── README_EN.md                   # This file
├── prompts/                       # Prompt reference docs (Markdown backup)
│   ├── 00-setup.md ~ 09-deploy.md
│   └── en/                        # English prompts
├── docs/                          # Design documents
├── examples/                      # Example sessions
├── CHANGELOG.md                   # Changelog
└── LICENSE                        # MIT License
```

> **Usage**: Just open `shutong-dev-workflow.html` in your browser. No dependencies to install.

---

## Version History

### v2.0 (Current)

- ✨ Wizard-style interaction: one step at a time, prev/next navigation
- ✨ 3 independent modes: New Project / Add Feature / Fix Issue
- ✨ Branch insertion: insert new requirement branch at any stage, nested merge support
- ✨ Layered coding: New Project mode code stage split into 4 layers (config -> utility -> entry -> business)
- ✨ Instruction cards: each prompt annotated with who, when, expected result, next step
- ✨ State persistence: localStorage auto-save, survives refresh
- ✨ Export/Import: JSON progress file backup and restore
- ✨ Right-side notepad: Requirements/Notes dual tabs, localStorage persistence
- ✨ Keyboard shortcuts: arrow keys and Enter for quick navigation
- ✨ Responsive design: adapts to desktop, tablet, mobile
- ✨ Document pre-review: review MVP document quality with 10-dimension scoring before entering workflow

### v1.2

- New project mode (INIT/DEPLOY stages)
- Layered building mechanism (config -> utility -> entry -> business)

### v1.1

- Git safety (forbidden destructive operations)
- Exception phrase hardening

### v1.0

- Initial release (PROBE -> DESIGN -> CONTRACT -> CODE -> VERIFY -> DONE)

---

## Why not just use Cursor / Claude Code?

| | Cursor Agent | Shutong Workflow |
|---|---|---|
| **Flow control** | Continuous execution, no stage freezing | Must pass Strategy AI review to proceed |
| **Requirement drift** | Easy to change requirements while coding | CONTRACT stage freezes interfaces, code cannot exceed scope |
| **Dual-AI review** | Single AI self-checking | Strategy AI independently reviews Execution AI output |
| **Non-programmer friendly** | Need to understand code to judge correctness | Just copy-paste and click confirm |
| **State recovery** | Close window = lose context | localStorage persistence, resume anytime |
| **Branch management** | None | Insert new requirement branches |

**Shutong doesn't replace Cursor - it wraps Cursor with an anti-drift scaffold.**

---

## License

[MIT License](LICENSE) - Attribution: Shutong Protocol Contributors
