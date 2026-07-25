# 04 - CODE: Single-File Changes

> **Stage goal**: Modify one file at a time. After finishing one, wait for confirmation before proceeding to the next.
>
> If multiple files need changes, repeat this stage (File 1 → File 2 → ... → File N).

---

## Branch A: Modify Existing Project → CODE

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

### Wait for Execution AI Response

You will receive code changes (`[FILE_DONE: filename]`).

Copy the complete response.

### Strategy AI Prompt (send to Window A)

Replace `{{Execution AI's code changes}}` with what you copied:

```text
【CODE Review Task】

Please review the following code changes returned by the Execution AI.

## Execution AI's Code Changes:
{{Execution AI's code changes}}

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

### Loop Execution

- If Strategy AI says "there's another file" → Copy [CODE-File N] instruction to Execution AI, repeat this stage
- If Strategy AI says "last file" → Copy [VERIFY] instruction, proceed to [05-verify_en.md](05-verify_en.md)

---

## Branch B: Build from Scratch → CODE

### Build Order

From-scratch projects must build in the following order, verifying once per layer:

```text
Layer 1: Config files (depended on by all modules)
Layer 2: Utility/shared modules (depended on by business modules)
Layer 3: Entry files (depends on config and utilities)
Layer 4: Business modules (depends on all above layers)
```

### Layer 1: Config Files

```text
【Stage: CODE-Skeleton-Config Layer】Create config files only. Do not touch other files.

【Absolute Rules】
1. Never advance to the next layer until the user says "confirm"
2. Never create files from multiple layers at once
3. After creating each file, must run git add + git commit
4. Never use git push --force, git reset --hard
5. Never use git add . (must specify exact files)

Confirmed config checklist:
{{paste the config file list confirmed in CONTRACT stage}}

Please create the following config files:
- {{config file 1 path}}: {{purpose}}
- {{config file 2 path}}: {{purpose}}

Requirements:
- Output complete code for each file
- Config files must have reasonable defaults
- Must have comments explaining each config item
- After creation, verify each file can be read/imported normally

Verification method: {{what command to run to verify config file is valid}}

Add [FILE_DONE: {{filename}}] after output.
Wait for me to say "next step" before continuing.
```

### Config Layer Verification

```text
【Stage: CODE-Skeleton-Config Layer Verification】Verify config layer works correctly.

Please execute:
1. Import/read all config files
2. Verify all required config items have values
3. Verify no conflicts between config files

If verification passes, output [LAYER_DONE: Config Layer]
If verification fails, output [LAYER_FAILED: Config Layer] + failure reason
```

### Layer 2: Utility/Shared Modules

```text
【Stage: CODE-Skeleton-Utility Layer】Create utility classes and shared modules only. Do not touch other files.

【Absolute Rules】(same as above)

Confirmed utility module checklist:
{{paste the module interface list confirmed in CONTRACT stage}}

Please create the following files:
- {{file path}}: {{functionality description}}
- {{file path}}: {{functionality description}}

Requirements:
- Output complete code for each file
- Each utility module must be importable independently (no business module dependencies)
- Must have type annotations
- After creation, verify each module can be imported normally

Verification method: {{what command to run to verify module is importable}}

Add [FILE_DONE: {{filename}}] after output.
Wait for me to say "next step" before continuing.
```

### Utility Layer Verification

```text
【Stage: CODE-Skeleton-Utility Layer Verification】Verify utility layer works correctly.

Please execute:
1. Import all utility modules
2. Verify each module's public interfaces (functions/classes) can be called
3. Verify utility modules don't depend on business modules

If verification passes, output [LAYER_DONE: Utility Layer]
If verification fails, output [LAYER_FAILED: Utility Layer] + failure reason
```

### Layer 3: Entry Files

```text
【Stage: CODE-Skeleton-Entry Layer】Create entry files only. Do not touch other files.

【Absolute Rules】(same as above)

Confirmed entry file:
{{paste the entry point design confirmed in INIT stage}}

Please create entry file: {{file path}}

Requirements:
- Output complete code
- Entry file must be runnable independently (no errors on startup)
- Must import config and utility modules
- Startup method must match what was confirmed in INIT stage

Verification method: {{what command to run to verify entry file can start}}

Add [FILE_DONE: {{filename}}] after output.
Wait for me to say "next step" before continuing.
```

### Entry Layer Verification

```text
【Stage: CODE-Skeleton-Entry Layer Verification】Verify entry file can start correctly.

Please execute:
1. Run the entry file
2. Verify startup logs have no errors
3. Verify config and utility modules are correctly loaded

If verification passes, output [LAYER_DONE: Entry Layer]
If verification fails, output [LAYER_FAILED: Entry Layer] + failure reason
```

### Layer 4: Business Modules

```text
【Stage: CODE-Skeleton-Business Layer】Create business module files only. Do not touch other files.

【Absolute Rules】
1. Never advance to the next file until the user says "confirm"
2. Never create multiple files at once
3. After creating each file, must run git add + git commit
4. Never use git push --force, git reset --hard
5. Never use git add . (must specify exact files)

Confirmed business module checklist:
{{paste the API interfaces and data models confirmed in CONTRACT stage}}

Please create business module files:
- {{file path}}: {{functionality description}}

Requirements:
- Output complete code for each file
- Must import config and utility modules
- Must implement interfaces defined in CONTRACT stage
- All exceptions must be handled per CONTRACT stage strategy
- Must have type annotations

Add [FILE_DONE: {{filename}}] after output.
Wait for me to say "next step" before continuing.
```

### Business Layer Verification

```text
【Stage: CODE-Skeleton-Business Layer Verification】Verify business modules work correctly.

Please execute:
1. Import all business modules
2. Run smoke test (call core interface, verify return)
3. Verify exception handling works as expected

If verification passes, output [LAYER_DONE: Business Layer]
If verification fails, output [LAYER_FAILED: Business Layer] + failure reason
```

### Strategy AI Prompt (after each layer verification, send to Window A)

```text
【CODE-Skeleton Review Task】

Please review the following skeleton building results returned by the Execution AI.

## Execution AI's Building Results:
{{Execution AI's building results}}

## Your Task:

### Step 1: Compliance Review
- [ ] All files in this layer have been created (has [FILE_DONE] markers)
- [ ] This layer's verification passed (has [LAYER_DONE] marker)
- [ ] File content matches interfaces defined in CONTRACT stage
- [ ] Has exception handling
- [ ] Has type annotations

### Step 2: Determine Next Step
- If this layer passed and there's another layer: Output next layer's CODE instruction
- If this layer passed and it's the last layer: Output [VERIFY] instruction (see 05-verify_en.md)
- If this layer failed: Output fix instruction
```
