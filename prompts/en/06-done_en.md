# 06 - DONE: Cleanup

> **Stage goal**: Remove probes, confirm the system is clean.

---

## Branch A: Modify Existing Project → DONE

### Execution AI Prompt (send to Window B)

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

### Wait for Execution AI Response

You will receive cleanup results + `[ALL_DONE]`.

**Modification complete.**

---

### Cleanup Checklist

Confirm the following:

- [ ] All `[PROBE]` temporary logs removed
- [ ] Normal business logs retained
- [ ] Smoke test passed (send message → normal reply → no ERROR)
- [ ] Code has been git committed

---

### Next

Modification complete. For new requirements, restart from [01-probe_en.md](01-probe_en.md).

---

## Branch B: Build from Scratch → DONE

### Execution AI Prompt (send to Window B)

```text
【Stage: DONE-From Scratch】Project wrap-up, complete new build.

Verification passed:
- {{brief verification result}}

Please execute:
1. Complete project documentation: README.md (project description, installation steps, startup method)
2. Complete environment config documentation: .env.example or config documentation
3. Generate handover checklist: project structure overview, key file locations, future extension suggestions
4. Final archive: git add + git commit + git tag v0.1
5. Smoke test: Follow README steps to clone and install from scratch, confirm it works

Output modified file list and archive results.
Add [ALL_DONE] at the end of output.
```

### Wait for Execution AI Response

You will receive wrap-up results + `[ALL_DONE]`.

**New project build complete.**

---

### Wrap-up Checklist

Confirm the following:

- [ ] README.md completed (project description, installation steps, startup method)
- [ ] Environment config documentation completed
- [ ] Handover checklist generated (project structure, key files, extension suggestions)
- [ ] Code has been git committed + git tagged v0.1
- [ ] Smoke test passed (fresh install works)

---

### Next

New project build complete. For future requirements, the project is now "an existing project" — restart from [01-probe_en.md](01-probe_en.md) PROBE branch.
