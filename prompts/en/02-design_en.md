# 02 - DESIGN: Freeze Architecture

> **Stage goal**: Confirm "what it will look like after modification", "how to verify", and "how to rollback if it fails".
>
> Based on the state judgment from PROBE/INIT stage, choose the corresponding branch.

---

## Branch A: State A (Brand New) → DESIGN-Build from Scratch

> For: PROBE judged as State A (no related new code at all)

### Execution AI Prompt (send to Window B)

```text
【Stage: DESIGN】Answer design questions only. Do not write code, do not execute commands.

Based on the code you just probed, this modification goal: {{repeat your requirement}}

Current code state (PROBE findings):
- Keyword extraction in {{file}}'s {{method}}, using {{old implementation}}
- Memory domain selection in {{file}}'s {{method}}, using {{old implementation}}
- Model call capability in {{file}}'s {{method}}
- Judgment logic is currently {{batch/item-by-item}}, in {{file}}

Please answer:
1. 【Data Flow】What is the complete data flow after modification? Use → to表示. Mark new/modified/deleted stages.
2. 【Modification Points】How many files are involved? List in dependency order (depended-on files first).
3. 【Call Relationships】Who calls the new/modified modules? Who do they output to? Draw the call relationships.
4. 【Rollback Strategy】If the new logic fails, how does the system rollback? What's the rollback path?
5. 【Verification Plan】How to prove the modification worked? Suggest where to add temporary logs/probes.
6. 【Risk Points】What existing features might this modification break? How to mitigate?

After answering clearly, I'll reply "confirm" before you write code.
Add [DESIGN_DONE] at the end of output.
```

### Wait for Execution AI Response

You will receive a design plan (6 answers + `[DESIGN_DONE]`).

Copy the complete response.

### Strategy AI Prompt (send to Window A)

```text
【DESIGN Review Task】

Please review the following DESIGN results returned by Mimo.

## Mimo's DESIGN Response:
{{Mimo's DESIGN response}}

## Your Task:

### Step 1: Completeness Review
Check the following items:
- [ ] Has complete data flow diagram (includes → arrows, from user input to final output)
- [ ] Lists specific files to modify (has file paths)
- [ ] Has clear modification order (which to modify first, which next)
- [ ] Rollback strategy is clear (which old method to call on failure)
- [ ] Verification plan is specific (not "check results", but "add logger at X file Y line")
- [ ] Lists at least 1 risk point
- [ ] Has [DESIGN_DONE] marker at the end

If any item fails, output a 【Follow-up Instruction】.

### Step 2: Output Next-Stage Instructions

If review passes, output [CONTRACT-Contract Confirmation] instruction (see 03-contract_en.md).
```

---

## Branch B: State B (Partial) → DESIGN-Diagnosis

> For: PROBE judged as State B (new code exists but hasn't taken effect)

### Execution AI Prompt (send to Window B)

```text
【Stage: DESIGN-Diagnosis】Do not modify code, only design a troubleshooting plan.

PROBE findings:
- {{existing file/method}} already exists
- {{call chain location}} already calls the new method
- But when user runs it, {{specific symptom, e.g. "keywords are still regex results"}}

Please design a troubleshooting plan, answer:
1. 【Hypothesis List】{{feature}} exists but output is still {{old result}}, list all possible causes, ranked by probability.
2. 【Verification Points】To verify each hypothesis, where to add what logs? Specific to file, method, line number.
3. 【Troubleshooting Order】List troubleshooting steps from highest to lowest probability.
4. 【Fix Prediction】What's the most likely problem? Which files would the fix involve?
5. 【Verification Plan】After fixing, how to verify? What action to trigger? What logs to expect?

Do not write code.
Add [DESIGN_DONE] at the end of output.
```

### Wait for Execution AI Response

You will receive a diagnosis plan.

Copy the complete response.

### Strategy AI Prompt (send to Window A)

```text
【DESIGN-Diagnosis Review Task】

Please review the following diagnosis plan returned by Mimo.

## Mimo's Diagnosis Plan:
{{Mimo's diagnosis plan}}

## Your Task:

### Step 1: Completeness Review
- [ ] Hypothesis list ranked by probability
- [ ] Verification points specific to file, method, line number
- [ ] Troubleshooting order reasonable (highest probability first)
- [ ] Fix prediction has basis
- [ ] Has [DESIGN_DONE] marker at the end

### Step 2: Output Next-Stage Instructions

If review passes, output [CODE-Diagnosis Fix] instruction (add logs to locate root cause, see 04-code_en.md).
```

---

## Branch C: Build from Scratch → DESIGN-Architecture Design

> For: INIT stage completed, building from scratch

### Execution AI Prompt (send to Window B)

```text
【Stage: DESIGN-Architecture Design】Answer design questions only. Do not write code, do not execute commands.

Based on the confirmed INIT plan, this project goal: {{repeat your project requirement}}

Confirmed tech stack:
- {{frontend framework/backend framework/database/other}}

Confirmed directory structure:
{{paste the directory tree confirmed in INIT stage}}

Please answer:
1. 【Data Flow】What is the core data flow of the project? Use → to表示. From user input to final output.
2. 【Skeleton Build Order】List files to create in dependency order, mark which are depended-on (must be created first).
3. 【Module Dependency Graph】What are the call relationships between modules? Draw the dependency graph.
4. 【Cleanup Strategy】If skeleton building fails midway, how to clean up already-created files? Revert to what state?
5. 【Verification Plan】After building each layer (config/utility/business), how to verify the skeleton doesn't collapse?
6. 【Risk Points】What problems might be encountered during building? How to mitigate?

After answering clearly, I'll reply "confirm" before you start building.
Add [DESIGN_DONE] at the end of output.
```

### Wait for Execution AI Response

You will receive an architecture design plan (6 answers + `[DESIGN_DONE]`).

Copy the complete response.

### Strategy AI Prompt (send to Window A)

```text
【DESIGN-Architecture Design Review Task】

Please review the following architecture design plan returned by Mimo.

## Mimo's Architecture Design Response:
{{Mimo's architecture design response}}

## Your Task:

### Step 1: Completeness Review
Check the following items:
- [ ] Has complete data flow diagram (includes → arrows)
- [ ] Skeleton build order is in dependency order (depended-on first)
- [ ] Has module dependency graph
- [ ] Cleanup strategy is clear (which files to delete on failure)
- [ ] Verification plan is specific (not "check results", but what exact command to run)
- [ ] Lists at least 1 risk point
- [ ] Has [DESIGN_DONE] marker at the end

If any item fails, output a 【Follow-up Instruction】.

### Step 2: Output Next-Stage Instructions

If review passes, output [CONTRACT-Interface Contract] instruction (see 03-contract_en.md from-scratch mode).
```

---

## Next

After review passes, copy the instruction generated by Strategy AI, proceed to [03-contract_en.md](03-contract_en.md).
