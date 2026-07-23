# 05 - VERIFY: Run Verification

> **Stage goal**: Prove the modification worked — not just "no errors = correct".

---

## Execution AI Prompt (send to Window B)

```text
【Stage: VERIFY】Execute verification flow with progress markers.

【Execution Rules】
- After completing each step, output [STEP X/5: step_name]
- If a step fails, output [STEP X/5 FAILED: reason] and stop
- User may stop midway and specify "continue from step X"
- VERIFY stage only runs, does not modify code

Step checklist:
1. Save all modified code files
2. Restart Flask/service (background start, non-blocking)
3. Wait 3 seconds, confirm service is ready
4. Send test request to {{API path found in PROBE}}
5. Read logs, filter lines containing [PROBE], output analysis

Begin execution.
```

---

## Wait for Execution AI Response

You will receive execution results with `[STEP X/5]` markers.

### If Execution AI gets stuck at a step

- Stop Execution AI
- Send: `continue from step X`

### Final Response

Execution AI will return log content.

Copy the complete response (especially the log section).

---

## Strategy AI Prompt (send to Window A)

Replace `{{Mimo's VERIFY response}}` with what you copied:

```text
【VERIFY Analysis Task】

Please analyze the following verification logs returned by Mimo.

## Mimo's VERIFY Response:
{{Mimo's VERIFY response}}

## Your Task:

### Step 1: Log Analysis
1. 【PROBE logs exist?】Yes/No. Evidence:
2. 【Analysis node/new logic called?】Yes/No. Evidence:
3. 【What did the model return?】Raw response:
4. 【Was degradation triggered?】Yes/No. Evidence:
5. 【Keywords/output are model results or old logic?】Judgment:

### Step 2: Root Cause Judgment
Based on logs, what is the root cause?

### Step 3: Next Step Decision
- If modification worked (logs prove new logic ran normally): Output [DONE] instruction (see 06-done_en.md)
- If analysis node returned empty / degradation triggered: Output [CODE-Fix] instruction (fall back to 04-code_en.md)
- If analysis node was not called: Output [CODE-Call Chain Fix] instruction (fall back to 04-code_en.md)
- If model call failed: Prompt user to check model service (Ollama etc.)
- If logs don't exist at all: Output [CODE-Log Fix] instruction (fall back to 04-code_en.md)
```

---

## Next

- Strategy AI says "modification worked" → Copy [DONE] instruction, proceed to [06-done_en.md](06-done_en.md)
- Strategy AI says "needs fix" → Copy the corresponding [CODE-Fix] instruction, return to [04-code_en.md](04-code_en.md)
