# 01 - PROBE / INIT: Understand Current State / Initialize from Scratch

> **PROBE stage goal**: Have the Execution AI investigate "how is this currently implemented" in the codebase, to prevent building on wrong assumptions.
> **INIT stage goal**: Design the project skeleton from scratch, confirm tech stack, directory structure, and dependency planning.

---

## Mode Selection

Please choose the corresponding branch based on your project status:

- **Mode A: Modify existing project** (codebase exists) → Use [Branch A: PROBE](#branch-a-modify-existing-project--probe)
- **Mode B: Build from scratch** (no codebase) → Use [Branch B: INIT](#branch-b-build-from-scratch--init)

---

## Branch A: Modify Existing Project → PROBE

### Execution AI Prompt (send to Window B)

```text
【Stage: PROBE】Query only. Do not modify code, do not generate code, do not execute system commands.

Requirement: {{your requirement}}

Please perform a comprehensive probe of the current codebase and answer the following questions. Each answer must reference specific file names, method/function names, class names, and line number ranges. If the project has frontend/backend separation, list both.

1. 【Current State】Where is the code that currently implements this requirement?
   - Backend: List all related files, methods/classes, key code snippets
   - Frontend: List all related components/pages, methods, key code snippets

2. 【Call Chain】What is the complete call chain that triggers this feature?
   - From user action/request entry to final processing, use → for each step
   - Example: User input → API route → Controller → Service → Model method

3. 【External Capabilities】Where are the project's existing model calls / AI analysis / external API wrappers?
   - What files? What methods? How are they called?
   - If there's a unified model call base class/utility, list it

4. 【Data Structures】Where are the core data structures defined?
   - Entity classes / data models / interface definition file locations
   - Key field names and types

5. 【Judgment Logic】Are the related conditionals and loops batch or item-by-item? Where are they implemented?

6. 【Extension Points】If a new module/component needs to be added, where should it go based on the project structure? What's the naming convention?

Requirements:
- Paste complete code of key methods (do not omit)
- Mark file paths with code blocks
- Output [PROBE_DONE] at the end

【v1.1 Patch: Large Codebase】
If the codebase has more than 50 files, or you cannot complete the full probe in one response:
1. First list all relevant files (no more than 10)
2. Prioritize probing the 3-5 files most directly related to the requirement
3. Note in output: "Probed X files, Y files remaining"
4. Wait for user confirmation before continuing with remaining files
```

### Wait for Execution AI Response

You will receive a probe report (6 answers + `[PROBE_DONE]`).

Copy the complete response.

### Strategy AI Prompt (send to Window A)

Replace `{{Execution AI's PROBE response}}` with what you copied:

```text
【PROBE Review Task】

Please review the following PROBE results returned by the Execution AI and judge the code state.

## Execution AI's PROBE Response:
{{Execution AI's PROBE response}}

## Your Task:

### Step 1: Completeness Review
Check the following items, mark pass or fail:
- [ ] All 6 questions answered (check that 1-6 are all present)
- [ ] Has specific file paths (can see xxx.py format)
- [ ] Has method/function names and line number ranges
- [ ] Key code fully pasted (not just method signatures)
- [ ] Has [PROBE_DONE] marker at the end
- [ ] If frontend is involved, PROBE includes frontend section

If any item fails, output a 【Follow-up Instruction】 asking the Execution AI to supplement the missing items.

### Step 2: State Judgment (Most Critical)
Based on PROBE results, determine which state the code is in:

**State A: Brand New**
- No related new code exists at all
- Only old logic (e.g., regex tokenization, string matching)
- No analysis_node.py or similar new module

**State B: Partial**
- New code files exist but haven't taken effect, or new and old are mixed
- Call chain has been modified but runtime still uses old logic
- Previously broke during modification, with residual code
- User explicitly says "modified before but it didn't work"

**State C: Completed**
- Code exists and PROBE shows logic is correct
- Call chain is complete, no residual old logic

Please output explicit state judgment: A / B / C
And provide judgment basis (cite specific evidence from PROBE).

### Step 3: Output Next-Stage Instructions

Based on state judgment, output the corresponding next-stage instruction:

- If State A (brand new): Output [DESIGN-Fresh Build] instruction (see 02-design_en.md Branch A)
- If State B (partial): Output [DESIGN-Diagnosis] instruction (see 02-design_en.md Branch B)
- If State C (completed): Output [VERIFY-Verify] instruction (see 05-verify_en.md)

The instruction must be a complete prompt that can be directly copied to the Execution AI.
```

### After Strategy AI Review Passes

You will receive:
- Completeness review results
- State judgment (A/B/C)
- Next-stage instruction (can be directly copied to Execution AI)

**Check if the state judgment is reasonable**. If Strategy AI says "State B" but you think it should be "State A", tell it to correct.

Copy the next-stage instruction generated by Strategy AI, proceed to [02-design_en.md](02-design_en.md).

---

## Branch B: Build from Scratch → INIT

### Execution AI Prompt (send to Window B)

```text
【Stage: INIT】Build project from scratch, design skeleton first, do not modify code.

Requirement: {{your project requirement}}

Please answer the following questions. Each answer must provide specific suggestions and rationale. If multiple tech options exist, list pros and cons for comparison.

1. 【Tech Stack】Based on requirements, recommend tech stack
   - Frontend: framework, UI library, build tools (if applicable)
   - Backend: language, framework, ORM (if applicable)
   - Database: type, version (if applicable)
   - Deployment: target platform, containerization (if applicable)
   - Explain rationale for each choice

2. 【Directory Structure】Design project directory tree
   - Show complete directory structure as a tree diagram
   - Explain what each directory/file contains
   - Mark which are core modules and which are auxiliary

3. 【Dependency Planning】What are the core dependencies?
   - List all required dependency packages
   - Suggest version numbers
   - Mark which are required and which are optional

4. 【Entry Point Design】What is the main entry file?
   - Startup method (CLI/script/double-click)
   - Startup parameters (if any)
   - Differences between development and production environments

5. 【Config Design】What config files are needed?
   - Environment variable list (name, purpose, default value)
   - Config file format (YAML/JSON/TOML/.env)
   - Which configs must be user-provided and which have defaults

6. 【Extension Planning】Future modules that may be extended
   - What interfaces/directories to reserve
   - Positions to reserve in architecture but not implement now

7. 【Acceptance Criteria】How do you prove this project is "done"?
   - Functionality checklist: Which features must work? (e.g. can add todos, can mark complete, can delete)
   - Acceptance testing: How to verify these features? (e.g. send test request, what response to expect)
   - Performance/stability: Any concurrency, response time, or other requirements?

Requirements:
- Output design only, do not generate code
- If tech selection is uncertain, list options with pros and cons, wait for user confirmation
- Output [INIT_DONE]
```

### Wait for Execution AI Response

You will receive a project design plan (7 answers + `[INIT_DONE]`).

Copy the complete response.

### Strategy AI Prompt (send to Window A)

Replace `{{Execution AI's INIT response}}` with what you copied:

```text
【INIT Review Task】

Please review the following project initialization plan returned by the Execution AI.

## Execution AI's INIT Response:
{{Execution AI's INIT response}}

## Your Task:

### Step 1: Completeness Review
Check the following items:
- [ ] All 7 questions answered
- [ ] Tech stack has specific framework names and version numbers
- [ ] Directory structure has a complete tree diagram
- [ ] Dependency list has package names and versions
- [ ] Entry file and startup method are clear
- [ ] Config file checklist is complete
- [ ] Acceptance criteria are specific and testable (has functionality checklist and verification method)
- [ ] Has [INIT_DONE] marker at the end

If any item fails, output a 【Follow-up Instruction】.

### Step 2: Tech Stack Confirmation
Check if the tech selection is reasonable:
- Does it match user requirements?
- Are there obvious compatibility issues?
- Is the dependency count reasonable (not over-engineered)?

If there are issues, point them out and suggest corrections.

### Step 3: Output Next-Stage Instructions

If review passes, output [DESIGN-Architecture Design] instruction (see 02-design_en.md Branch C).

The instruction must be a complete prompt that can be directly copied to the Execution AI.
```

### After Strategy AI Review Passes

You will receive:
- Completeness review results
- Tech stack confirmation
- Next-stage instruction (can be directly copied to Execution AI)

**Check if the tech selection meets your expectations**. If there are disagreements, tell Strategy AI to correct.

Copy the next-stage instruction generated by Strategy AI, proceed to [02-design_en.md](02-design_en.md).
