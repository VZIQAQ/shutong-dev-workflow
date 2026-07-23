# Comparison with Alternatives

> Comparison analysis between this workflow and mainstream AI-assisted development solutions.

---

## Compared Projects

| Project | Description |
|---------|-------------|
| **aicodeguide** | AI-assisted coding guide with coding standards and best practices |
| **claude-task-master** | Claude's task management framework, breaking large tasks into subtasks |
| **claude-code-spec-workflow** | Claude's code spec workflow, from specification to code |
| **claude-flow** | Claude's multi-agent collaboration framework |
| **This Workflow (Shutong)** | Non-programmer's dual-AI anti-drift workflow |

---

## Comparison Dimensions

| Dimension | aicodeguide | claude-task-master | claude-code-spec-workflow | claude-flow | This Workflow (Shutong) |
|-----------|-------------|-------------------|--------------------------|-------------|------------------------|
| **Target Users** | Programmers | Programmers | Programmers | Programmers | **Non-programmers** |
| **AI Architecture** | Single AI | Single AI | Single AI | Multi AI (same type) | **Dual AI (Strategy + Execution)** |
| **Stage Division** | None | Task decomposition | Spec → Code | Task assignment | **6-stage pipeline** |
| **Non-programmer Friendly** | Low | Low | Low | Low | **High (pure copy-paste)** |
| **Anti-drift Mechanism** | None | None | Spec constraint | Multi-AI cross-review | **Strategy AI reviews every step** |
| **Progress Management** | None | Task state | None | Task state | **Progress coordinates** |
| **Error Recovery** | None | Retry | None | Reassign | **Resume from specific step** |
| **Version Control** | None | None | None | None | **Git safety** |
| **Interface Contract** | None | None | Spec document | None | **CONTRACT stage freezes it** |

---

## Unique Value of This Workflow

### 1. Dual-AI Cross-Check

- **Strategy AI** (Kimi/ChatGPT/Claude): Reviews direction, never touches code
- **Execution AI** (Mimo): Writes code, never judges direction
- **User**: Information hub, only copy-paste and confirm

Single AI tools cannot self-check errors. Dual AI achieves cross-check through division of labor.

### 2. Progress Coordinates

Each stage has clear status markers:
- `[PROBE_DONE]` — Probe complete
- `[DESIGN_DONE]` — Design complete
- `[CONTRACT_DONE]` — Contract confirmed
- `[FILE_DONE: filename]` — File modification complete
- `[STEP X/N: step_name]` — Verification step progress
- `[ALL_DONE]` — All complete

When stuck, can resume from any marker without restarting.

### 3. User as Pure Switch

User doesn't need to know code, only needs to:
- Copy-paste prompts
- Confirm or rollback
- Say "next step" or "continue from step X"

---

## Use Case Comparison

| Scenario | Recommended Solution |
|----------|---------------------|
| Programmer daily coding | aicodeguide / Cursor / Copilot |
| Programmer managing complex tasks | claude-task-master |
| Programmer from spec to code | claude-code-spec-workflow |
| Multi-AI collaboration | claude-flow |
| **Non-programmer transforming projects** | **This Workflow (Shutong)** |
