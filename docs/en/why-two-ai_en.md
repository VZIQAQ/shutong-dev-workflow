# Why Two AIs?

> The fatal flaws of a single AI self-questioning, and how dual-AI division of labor solves them.

---

## Problems with Single AI

### 1. Hallucinations Cannot Self-Check

A single AI generating code may:
- Hallucinate non-existent field names (e.g., `relevance_score` when it's actually `relevance`)
- Hallucinate non-existent method names (e.g., `call_model()` when it's actually `_call_api()`)
- Hallucinate non-existent file paths

**Problem**: AI reviewing its own generated code will think "this is correct" because it doesn't question its own output.

**Dual-AI Solution**: Strategy AI reviews Execution AI's output, comparing against actual field names and method names found in PROBE, immediately pointing out inconsistencies.

### 2. Direction Drift Cannot Self-Correct

A single AI in multi-turn conversations may:
- Forget the original requirement and go in the wrong direction
- Build more code on wrong assumptions, getting further off track
- The code "runs fine" but the underlying logic is wrong

**Problem**: AI has no "look back" mechanism and won't proactively check "am I going off track?"

**Dual-AI Solution**: Strategy AI reviews Execution AI's output at each stage, judging "does this match the original requirement?" and immediately correcting when drift is detected.

### 3. Cannot Self-Control Multi-File Changes

A single AI executing complex tasks may:
- Modify multiple files at once, breaking the call chain
- Change file A but forget to synchronize changes to file B
- Output code snippets that contradict each other

**Problem**: AI has no "one thing at a time" discipline and tends to "solve everything at once."

**Dual-AI Solution**: Workflow enforces one file at a time. Strategy AI reviews "was only 1 file modified?" and immediately stops violations.

---

## Dual-AI Division of Labor

### Strategy AI (Window A)

**Responsibility**: Review direction, never touches code

- Reviews whether Execution AI's output is complete
- Judges current code state (brand new / partial / completed)
- Generates next-stage execution instructions
- **Never generates actual code**

**Suitable AIs**: Kimi, ChatGPT, Claude (good at analysis and judgment)

### Execution AI (Window B)

**Responsibility**: Write code, never judges direction

- Follows instructions to read code, modify code, run verification
- Outputs status markers (e.g., `[PROBE_DONE]`)
- **Never decides next steps on its own**

**Suitable AIs**: Mimo Code, Cursor, Copilot (good at code operations)

---

## User as Information Hub

The user's role in the dual-AI architecture is the **information hub**:

```text
┌─────────────┐          ┌─────────────┐
│  Strategy AI │◄────────►│    User      │◄────────►│ Execution AI │
│  (Window A)  │  Review   │  (Info Hub)  │ Inst/Result│ (Window B)   │
│  Kimi etc.   │◄────────►│    You       │◄────────►│    Mimo      │
└─────────────┘          └─────────────┘          └─────────────┘
```

User's operations:
1. Copy Execution AI's output to Strategy AI for review
2. Copy Strategy AI's instructions to Execution AI for execution
3. Confirm or rollback

**User doesn't need to know code** — just needs to copy-paste and click confirm.

---

## Analogy

| Role | Responsibility | Maps To |
|------|---------------|---------|
| **Director** | Reviews direction, judges "is this scene correct?" | Strategy AI |
| **Actor** | Performs according to script, doesn't change the script | Execution AI |
| **Script Supervisor** | Records progress, passes information, confirms "that's a take" | User |

- Director doesn't personally act (Strategy AI doesn't write code)
- Actor doesn't decide what scene to perform next (Execution AI doesn't judge direction)
- Script Supervisor doesn't need to know how to act (User doesn't need to know code)

---

## When You Don't Need Dual AI

- Task is very simple (change one line, add one field) → Use single AI directly
- User is a programmer who can review code → Single AI + self-review
- Task has a clear spec document → Single AI + spec constraint

## When You Need Dual AI

- User can't code but wants to use AI to transform projects
- Task is complex, involving multiple files and modules
- Previously broken by AI, wants safety guarantees
- Needs repeatable, rollback-able development workflow
