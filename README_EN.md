# Shutong Dev Workflow

> English | [中文](README.md)

> A complete anti-drift playbook for non-programmers using AI-assisted development

![MIT License](https://img.shields.io/badge/License-MIT-green.svg)

**Core principle: Unsure → Stop → Ask → Record → Code → Answer**

---

## What is this?

A development workflow designed for **users who can't code but want to use AI to transform their projects**. Through a dual-AI collaboration (Strategy AI + Execution AI), it breaks vague requirements into executable staged operations, with clear prompts, review checklists, and status markers at every step.

You don't need to know code — you just need to know how to **copy-paste** and **click confirm**.

---

## Quick Start (3 Steps)

```text
1. Open two AI windows
   - Window A (Strategy Layer): Kimi / ChatGPT / Claude — review direction, generate instructions
   - Window B (Execution Layer): Mimo Code — read code, modify code, run verification

2. Copy prompts from prompts/ directory stage by stage
   - Each stage has an [Execution AI Prompt] and a [Strategy AI Prompt]
   - Replace {{placeholders}} with your actual content

3. Follow the playbook step by step, wait for confirmation at each step
   - Execution AI never advances until you say "confirm"
   - Each step has a status marker (e.g. [PROBE_DONE]) for precise recovery when stuck
```

---

## Directory Structure

```text
shutong-dev-workflow/
├── LICENSE                          # MIT License
├── README.md                        # Chinese version
├── README_EN.md                     # This file (English)
├── CHANGELOG.md                     # Chinese changelog
├── CHANGELOG_EN.md                  # English changelog
├── prompts/                         # Stage prompts (use in order)
│   ├── 00-setup.md                  # Initialization
│   ├── 01-probe.md                  # PROBE: understand current state
│   ├── 02-design.md                 # DESIGN: freeze architecture
│   ├── 03-contract.md               # CONTRACT: interface contract
│   ├── 04-code.md                   # CODE: single-file changes
│   ├── 05-verify.md                 # VERIFY: run verification
│   ├── 06-done.md                   # DONE: cleanup
│   ├── 07-exception-handling.md     # Exception handling cheat sheet
│   ├── 08-git-safety.md            # Git safety
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
├── docs/                            # Design documents
│   ├── comparison.md                # Comparison with alternatives
│   ├── why-two-ai.md               # Why two AIs?
│   └── en/                          # English docs
│       ├── comparison_en.md
│       └── why-two-ai_en.md
└── examples/
    ├── example-session.md           # Full example session (Chinese)
    └── en/
        └── example-session_en.md    # Full example session (English)
```

---

## Stage Flow

```text
┌─────────┐     ┌─────────┐     ┌──────────┐     ┌──────┐     ┌────────┐     ┌──────┐
│  PROBE  │────▶│ DESIGN  │────▶│ CONTRACT │────▶│ CODE │────▶│ VERIFY │────▶│ DONE │
│  Scout  │     │  Plan   │     │ Contract │     │ Edit │     │  Test  │     │ Clean│
└─────────┘     └─────────┘     └──────────┘     └──────┘     └────────┘     └──────┘
     │                │                                                  │
     │          ┌─────┴─────┐                                            │
     │          ▼           ▼                                            │
     │    State A (new)  State B (partial)                                │
     │    Build fresh    Diagnose ──────────────────────────────────────────┘
     │                                                                      ▲
     └──────────────────────────────────────────────────────────────────────┘
                          VERIFY failure falls back to CODE
```

At each stage:
- Execution AI produces output + status marker (e.g. `[PROBE_DONE]`)
- User copies it to Strategy AI for review
- Strategy AI generates next-stage instructions
- User copies instructions to Execution AI

---

## Comparison with Alternatives

| Feature | Single AI Tools (Cursor/Copilot) | This Workflow |
|---------|----------------------------------|---------------|
| AI Architecture | Single AI, self-questioning | Dual AI cross-check (Strategy + Execution) |
| Anti-drift | None, AI may drift | Strategy AI reviews every output |
| Progress Management | None | Progress coordinates ([STEP X/N]) |
| Non-programmer Friendly | Requires some coding | Pure copy-paste + confirm |
| Error Recovery | Restart conversation | Resume from specific step |
| Version Control | None | Git safety (rollback) |
| Interface Contract | None | CONTRACT stage freezes it |

See [docs/en/comparison_en.md](docs/en/comparison_en.md)

---

## Who Is This For?

- Users who **can't code** but want to use AI to transform their projects
- Users who have ideas but don't know how to communicate with AI
- Users who have been burned by AI making things worse, and want safety guarantees
- Teams that need repeatable, rollback-able development workflows

---

## Core Principles

1. **PROBE is a state sensor** — Not just "check it", but decide "build fresh" or "debug existing"
2. **Progress markers are anti-stuck core** — Give "stuck → stop → resume" precise coordinates
3. **Strategy AI never touches code** — It reviews "are there 6 answers" / "is there a [PROBE] log", not code logic
4. **User is just a switch** — Confirm, rollback, next step, resume from step X — no code knowledge needed
5. **Direction drift is more dangerous than errors** — System runs fine but logic is wrong, only PROBE+VERIFY layers can expose it
6. **When unsure, stop** — Any anomaly at any stage: stop immediately, wait for Strategy AI judgment

---

## Versions

- **v1.0** — 7-stage complete workflow (PROBE → DESIGN → CONTRACT → CODE → VERIFY → DONE)
- **v1.1** — Git safety, exception handling hardening, PROBE scope limiting, execution discipline

See [CHANGELOG_EN.md](CHANGELOG_EN.md) | [CHANGELOG.md](CHANGELOG.md)

---

## License

[MIT License](LICENSE) — Attribution: Shutong Protocol Contributors
