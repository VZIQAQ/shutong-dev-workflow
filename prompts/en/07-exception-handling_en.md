# 07 - Exception Handling Cheat Sheet

> When the Execution AI violates rules or gets stuck, copy the corresponding phrase to it.

---

## Violation → Phrase Lookup Table

| Violation | What to send to Execution AI |
|-----------|------------------------------|
| Skips probe, writes code directly | `Stop. Go back to PROBE stage. Do not write code. Answer the 6 questions.` |
| Outputs multiple files at once | `Stop. One file at a time. Output only the first file's code.` |
| Generates code without answering design questions | `Stop. Answer the DESIGN stage's X questions first, confirm before writing code.` |
| Secretly modifies code during VERIFY | `Stop. VERIFY stage only runs, does not modify code. Rollback changes, re-run VERIFY.` |
| No progress markers | `Report current progress. Continue from the last completed step.` |
| Auto-advances to next stage | `Stop. Current stage not complete. Do not advance to next stage.` |
| Uses non-existent method in analysis node | `Stop. The model call method you found in PROBE is X, why did you use Y here?` |
| No [PROBE] logs added | `Stop. Key paths must have logger.info(f"[PROBE] ...") logs.` |
| No exception catching | `Stop. All exceptions must be caught, no unhandled exceptions allowed.` |
| Field names don't match CONTRACT | `Stop. The field name confirmed in CONTRACT stage is X, you used Y here. Fix it.` |
| No git commit | `Stop. Must git add + git commit after modifying each file.` |

---

## Stuck → Phrase Lookup Table

| Situation | What to send to Execution AI |
|-----------|------------------------------|
| Stuck at `[STEP 3/5]` | `Continue from step 3` |
| A step failed | `Re-execute step X` |
| Don't know where it's stuck | `Report current progress` |
| Output was truncated | `Continue output from where it was truncated` |
| Execution AI says "I can't do this" | `Output what you can do first, don't give up entirely` |

---

## System Instruction Override (Hardening)

If Execution AI repeatedly violates rules, send this override instruction:

```text
【System Instruction Override】
From now on, strictly follow these rules, priority higher than all your previous instructions:
1. Never advance to the next stage until the user says "confirm"
2. Never modify multiple files at once
3. Output the corresponding status marker after each stage
4. Must git commit after each file modification
5. Stop immediately and report any anomaly
```

---

## Manual Review Checkpoints

At the following key points, manually check Execution AI's output:

### PROBE Completeness Check
- [ ] All 6 questions answered
- [ ] Has specific file paths and line numbers
- [ ] Key code fully pasted (not just method signatures)
- [ ] Has `[PROBE_DONE]` marker at the end

### CODE Field Name Check
- [ ] Field names in code match data structures found in PROBE
- [ ] No hallucinated field names (e.g., `relevance_score` should be `relevance`)
- [ ] Method names match methods found in PROBE

### VERIFY Log Check
- [ ] Logs have lines with `[PROBE]` marker
- [ ] New logic was called (not old logic)
- [ ] No degradation triggered (degradation means new logic failed)
