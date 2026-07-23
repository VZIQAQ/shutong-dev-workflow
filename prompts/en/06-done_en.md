# 06 - DONE: Cleanup

> **Stage goal**: Remove probes, confirm the system is clean.

---

## Execution AI Prompt (send to Window B)

```text
【Stage: DONE】Cleanup probes, complete modification.

Verification passed:
- {{brief verification result}}

Please execute:
1. Remove all temporary log code marked with [PROBE]
2. Keep normal business logs
3. After cleanup, do a smoke test: send one message, confirm normal reply, no ERROR in console

Output modified file list and test results.
Add [ALL_DONE] at the end of output.
```

---

## Wait for Execution AI Response

You will receive cleanup results + `[ALL_DONE]`.

**Modification complete.**

---

## Cleanup Checklist

Confirm the following:

- [ ] All `[PROBE]` temporary logs removed
- [ ] Normal business logs retained
- [ ] Smoke test passed (send message → normal reply → no ERROR)
- [ ] Code has been git committed

---

## Next

Modification complete. For new requirements, restart from [01-probe_en.md](01-probe_en.md).
