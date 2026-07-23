# 04 - CODE: Single-File Changes

> **Stage goal**: Modify one file at a time. After finishing one, wait for confirmation before proceeding to the next.
>
> If multiple files need changes, repeat this stage (File 1 → File 2 → ... → File N).

---

## Execution AI Prompt (send to Window B)

### File 1

```text
【Stage: CODE-File 1】Modify only this file. Do not touch other files.

【Absolute Rules】
1. Never advance to the next file until the user says "confirm"
2. Never modify multiple files at once
3. After modifying each file, must run git add + git commit (commit message must describe what changed)
4. If modification fails, must run git checkout to revert to the last commit
5. Never use git push --force, git reset --hard, or other destructive operations
6. Never use git add . (must specify exact files)

{{Create/Modify}} file: {{file path}}

Modification goal: {{specific feature description}}

Current project context (PROBE findings):
- Model call wrapper in {{file}}'s {{method}}
- Related data structure: {{data object}} fields are {{field list}}
- Old method {{method name}} in {{file}} preserved as fallback

Requirements:
- Output only the changed code, with comments marking insertion position
- If creating a new file, output complete code
- Key paths must have [PROBE] logs, format: logger.info(f"[PROBE] specific info")
- All exceptions must be caught, no unhandled exceptions allowed
- Code style must match existing project code
- Do not restart services, do not run tests, do not read logs
- Output only this file's code

Add [FILE_DONE: {{filename}}] after output.
Wait for me to say "next step" before continuing.
```

### File N (and subsequent files)

```text
【Stage: CODE-File N】Modify only this file. Do not touch other files.

【Absolute Rules】(same as above, omitted)

{{Create/Modify}} file: {{file path}}

Modification goal: {{specific feature description}}

Current project context (PROBE findings):
- Model call wrapper in {{file}}'s {{method}}
- Related data structure: {{data object}} fields are {{field list}}
- Old method {{method name}} in {{file}} preserved as fallback

Requirements:
- Output only the changed code, with comments marking insertion position
- If creating a new file, output complete code
- Key paths must have [PROBE] logs, format: logger.info(f"[PROBE] specific info")
- All exceptions must be caught, no unhandled exceptions allowed
- Code style must match existing project code
- Do not restart services, do not run tests, do not read logs
- Output only this file's code

Add [FILE_DONE: {{filename}}] after output.
Wait for me to say "next step" before continuing.
```

---

## Wait for Execution AI Response

You will receive code changes (`[FILE_DONE: filename]`).

Copy the complete response.

---

## Strategy AI Prompt (send to Window A)

Replace `{{Mimo's code changes}}` with what you copied:

```text
【CODE Review Task】

Please review the following code changes returned by Mimo.

## Mimo's Code Changes:
{{Mimo's code changes}}

## Your Task:

### Step 1: Compliance Review
- [ ] Only modified 1 file (output has only 1 [FILE_DONE])
- [ ] Has [PROBE] logs (code has logger.info(f"[PROBE] ...") )
- [ ] Has exception catching (has try/except or similar mechanism)
- [ ] References PROBE methods (e.g., _call_api)
- [ ] Field names match CONTRACT (no hallucinated field names)

### Step 2: Determine Next Step

If there's another file to modify, output [CODE-File N] instruction.
If this is the last file, output [VERIFY] instruction (see 05-verify_en.md).
```

---

## Loop Execution

- If Strategy AI says "there's another file" → Copy [CODE-File N] instruction to Execution AI, repeat this stage
- If Strategy AI says "last file" → Copy [VERIFY] instruction, proceed to [05-verify_en.md](05-verify_en.md)
