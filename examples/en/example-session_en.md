# Full Example Session

> A fictional complete transformation dialogue showing dual-AI collaboration in action.
>
> **Scenario**: User has a chatbot project and wants to change keyword extraction from "regex tokenization" to "AI model extraction".

---

## Initialization

### User sends initialization to Window B (Execution AI)

```text
【Role】You are the development workflow executor.
【Absolute Rules】
1. Never advance to the next stage until the user says "confirm"
2. Never modify multiple files at once
3. Output [STATE: stage_name] marker after completing each stage
4. If the user says "rollback", return to the previous state and re-execute
5. After modifying each file during coding, output [FILE_DONE: filename]
6. During long-chain execution, output [STEP X/N: step_name] for each step, and [STEP X/N FAILED: reason] on failure
7. Stop after completion, wait for the user to say "next step"
8. If any anomaly is found at any stage, stop immediately and report — do not fix on your own

【Git Safety Rules】
1. Before every code modification, run git status to check the working directory
2. After modifying each file, run git add + git commit (commit message must describe what changed)
3. If modification fails, run git checkout to revert to the last commit
4. Never use git push --force, git reset --hard, or other destructive operations
5. Never use git add . (always specify exact files)

Now waiting for the user to provide requirements and stage instructions.
```

### Execution AI Response

```text
Instructions received. Waiting for user requirements and stage instructions.
```

---

## PROBE Stage

### User sends PROBE instruction to Window B (Execution AI)

```text
【Stage: PROBE】Query only. Do not modify code, do not generate code, do not execute system commands.

Requirement: Change keyword extraction from regex tokenization to AI model extraction

Please perform a comprehensive probe of the current codebase and answer the following questions...
(abbreviated, full prompt in prompts/en/01-probe_en.md)
```

### Execution AI Response

```text
## PROBE Probe Report

### 1. 【Current State】
- Backend:
  - File: `chat/keyword_extractor.py`
  - Method: `extract_keywords(text: str) -> List[str]`
  - Code snippet:
    ```python
    def extract_keywords(text: str) -> List[str]:
        # Regex tokenization
        words = re.findall(r'\w+', text)
        return words[:5]
    ```

### 2. 【Call Chain】
User input → `chat/routes.py:send_message()` → `chat/keyword_extractor.py:extract_keywords()` → Return keyword list

### 3. 【External Capabilities】
- File: `utils/ollama_client.py`
- Method: `call_model(prompt: str) -> str`
- Call method: HTTP POST to `http://localhost:11434/api/generate`

### 4. 【Data Structures】
- No independent data structure, keywords returned directly as `List[str]`

### 5. 【Judgment Logic】
- Item-by-item processing, each user message extracts keywords separately
- Called in `chat/routes.py:send_message()`

### 6. 【Extension Points】
- New modules should go in `chat/` directory
- Naming convention: `xxx_extractor.py`

[PROBE_DONE]
```

### User copies Execution AI's response, sends to Window A (Strategy AI)

```text
【PROBE Review Task】

Please review the following PROBE results returned by Mimo and judge the code state.

## Mimo's PROBE Response:
(full content above)

## Your Task:
(abbreviated, full prompt in prompts/en/01-probe_en.md)
```

### Strategy AI Response

```text
## PROBE Review Results

### Step 1: Completeness Review
- [x] All 6 questions answered
- [x] Has specific file paths (chat/keyword_extractor.py)
- [x] Has method/function names and line number ranges
- [x] Key code fully pasted
- [x] Has [PROBE_DONE] marker at the end
- [ ] If frontend involved, PROBE includes frontend (not applicable, skip)

Review passed.

### Step 2: State Judgment

**State A: Brand New**

Basis:
- Current keyword extraction uses regex tokenization (re.findall)
- No AI model extraction code exists
- No analysis_node.py or similar new module

### Step 3: Next-Stage Instruction

【DESIGN-Build from Scratch】

(full instruction in prompts/en/02-design_en.md Branch A)
```

---

## DESIGN Stage

### User copies Strategy AI's instruction, sends to Window B (Execution AI)

```text
【Stage: DESIGN】Answer design questions only. Do not write code, do not execute commands.

Based on the code you just probed, this modification goal: Change keyword extraction from regex tokenization to AI model extraction

Current code state (PROBE findings):
- Keyword extraction in chat/keyword_extractor.py's extract_keywords(), using regex tokenization
- Model call capability in utils/ollama_client.py's call_model()
- Judgment logic is currently item-by-item, in chat/routes.py

Please answer:
1. 【Data Flow】What is the complete data flow after modification?
2. 【Modification Points】How many files are involved?
3. 【Call Relationships】Who calls the new/modified modules?
4. 【Rollback Strategy】If the new logic fails, how does the system rollback?
5. 【Verification Plan】How to prove the modification worked?
6. 【Risk Points】What existing features might this break?

After answering clearly, I'll reply "confirm" before you write code.
Add [DESIGN_DONE] at the end of output.
```

### Execution AI Response

```text
## DESIGN Design Plan

### 1. 【Data Flow】
User input → chat/routes.py:send_message() → chat/keyword_extractor.py:extract_keywords() → utils/ollama_client.py:call_model() → AI returns keywords → Return keyword list

New stage: call_model() call
Modified stage: extract_keywords() changed from regex to AI call
Deleted stage: re.findall() regex tokenization

### 2. 【Modification Points】
1. chat/keyword_extractor.py (modify extract_keywords method)
2. utils/ollama_client.py (already exists, no modification needed)
3. chat/routes.py (no modification needed, call method unchanged)

### 3. 【Call Relationships】
chat/routes.py:send_message() → chat/keyword_extractor.py:extract_keywords() → utils/ollama_client.py:call_model()

### 4. 【Rollback Strategy】
If AI call fails, degrade to old regex tokenization:
```python
try:
    keywords = call_model(prompt)
except Exception:
    keywords = re.findall(r'\w+', text)  # Degrade
```

### 5. 【Verification Plan】
Add logs in extract_keywords():
```python
logger.info(f"[PROBE] Input text: {text}")
logger.info(f"[PROBE] AI returned keywords: {keywords}")
```
Send test message, check logs for AI-returned keywords.

### 6. 【Risk Points】
- If Ollama service is not running, degradation will trigger
- If AI returns incorrect format, parsing handling needed

[DESIGN_DONE]
```

### User copies Execution AI's response, sends to Window A (Strategy AI)

(abbreviated, full prompt in prompts/en/02-design_en.md)

### Strategy AI Response

```text
## DESIGN Review Results

### Step 1: Completeness Review
- [x] Has complete data flow diagram
- [x] Lists specific files to modify
- [x] Has clear modification order
- [x] Rollback strategy is clear
- [x] Verification plan is specific
- [x] Lists risk points
- [x] Has [DESIGN_DONE] marker at the end

Review passed.

### Step 2: Next-Stage Instruction

【CONTRACT-Contract Confirmation】

(full instruction in prompts/en/03-contract_en.md)
```

---

## CONTRACT Stage

(Abbreviated, flow same as above, full prompt in prompts/en/03-contract_en.md)

---

## CODE Stage (3 files)

### File 1: chat/keyword_extractor.py

#### User sends CODE instruction

```text
【Stage: CODE-File 1】Modify only this file. Do not touch other files.

【Absolute Rules】
1. Never advance to the next file until the user says "confirm"
2. Never modify multiple files at once
3. After modifying each file, must run git add + git commit
4. If modification fails, must run git checkout to revert
5. Never use git push --force, git reset --hard
6. Never use git add .

Modify file: chat/keyword_extractor.py

Modification goal: Change extract_keywords() from regex tokenization to AI model call

Current project context (PROBE findings):
- Model call wrapper in utils/ollama_client.py's call_model()
- Old method extract_keywords() in chat/keyword_extractor.py preserved as fallback

Requirements:
- Output only the changed code, with comments marking insertion position
- Key paths must have [PROBE] logs
- All exceptions must be caught
- Code style must match existing project code
- Do not restart services, do not run tests, do not read logs
- Output only this file's code

Add [FILE_DONE: keyword_extractor.py] after output.
Wait for me to say "next step" before continuing.
```

#### Execution AI Response

```python
# chat/keyword_extractor.py

import re
import logging
from typing import List
from utils.ollama_client import call_model

logger = logging.getLogger(__name__)

def extract_keywords(text: str) -> List[str]:
    """Extract keywords from text (AI model version)"""
    try:
        logger.info(f"[PROBE] Input text: {text[:100]}...")
        
        prompt = f"Extract 5 keywords from the following text, separated by commas:\n{text}"
        result = call_model(prompt)
        
        logger.info(f"[PROBE] AI returned: {result}")
        
        keywords = [kw.strip() for kw in result.split(",")]
        return keywords[:5]
        
    except Exception as e:
        logger.error(f"[PROBE] AI call failed, degrading to regex: {e}")
        # Degrade: use old regex tokenization
        words = re.findall(r'\w+', text)
        return words[:5]
```

```bash
$ git add chat/keyword_extractor.py
$ git commit -m "CODE-File 1: Change keyword extraction from regex to AI model"
```

[FILE_DONE: keyword_extractor.py]

#### Violation Example: Execution AI modifies multiple files at once

```text
⚠️ Execution AI violated: Output code for both keyword_extractor.py and routes.py
```

#### User corrects with phrase

```text
Stop. One file at a time. Output only the first file's code.
```

#### Execution AI corrects

```text
Corrected. Only outputting keyword_extractor.py code.
```

(Files 2 and 3 follow the same flow, abbreviated)

---

## VERIFY Stage

### User sends VERIFY instruction

```text
【Stage: VERIFY】Execute verification flow with progress markers.

【Execution Rules】
- After completing each step, output [STEP X/5: step_name]
- If a step fails, output [STEP X/5 FAILED: reason] and stop
- VERIFY stage only runs, does not modify code

Step checklist:
1. Save all modified code files
2. Restart Flask/service (background start, non-blocking)
3. Wait 3 seconds, confirm service is ready
4. Send test request to /api/send_message
5. Read logs, filter lines containing [PROBE], output analysis

Begin execution.
```

### Execution AI Response

```text
[STEP 1/5: Save code files] ✓ All files saved

[STEP 2/5: Restart service] ✓ Service started in background

[STEP 3/5: Wait for service ready] ✓ Service ready

[STEP 4/5: Send test request] ✓ Test request sent

[STEP 5/5: Read logs]
[PROBE] Input text: The weather is really nice today, perfect for going out...
[PROBE] AI returned: weather,going out,perfect,today,nice
```

### User copies Execution AI's response, sends to Window A (Strategy AI)

### Strategy AI Response

```text
## VERIFY Analysis Results

### Step 1: Log Analysis
1. 【PROBE logs exist?】Yes. Evidence: Logs have lines with [PROBE] marker
2. 【Analysis node/new logic called?】Yes. Evidence: Logs show call_model() was called
3. 【What did the model return?】"weather,going out,perfect,today,nice"
4. 【Was degradation triggered?】No. Evidence: No degradation logs
5. 【Keywords/output are model results or old logic?】Model results ("weather" etc. are semantic keywords, not regex single characters)

### Step 2: Root Cause Judgment
Modification worked. AI model normally returned keywords.

### Step 3: Next Step Decision
Modification worked. Output [DONE] instruction.

(full instruction in prompts/en/06-done_en.md)
```

---

## DONE Stage

### User sends DONE instruction

```text
【Stage: DONE】Cleanup probes, complete modification.

Verification passed:
- AI model normally returns semantic keywords
- No degradation triggered

Please execute:
1. Remove all temporary log code marked with [PROBE]
2. Keep normal business logs
3. After cleanup, do a smoke test

Output modified file list and test results.
Add [ALL_DONE] at the end of output.
```

### Execution AI Response

```text
Cleaned up:
- Removed 2 [PROBE] temporary log lines from chat/keyword_extractor.py
- Retained 1 business log line

Smoke test:
- Sent test message ✓
- Received normal reply ✓
- No ERROR in console ✓

[ALL_DONE]
```

---

## Modification Complete

```text
✅ Modification complete

What changed: Keyword extraction from regex tokenization to AI model extraction
Files involved: chat/keyword_extractor.py
Verification result: AI model normally returns semantic keywords, no degradation triggered

Next: For new requirements, restart from PROBE stage.
```
