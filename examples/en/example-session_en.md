# Full Example Session

> A fictional complete transformation dialogue showing dual-AI collaboration in action.
>
> Contains two cases:
> - **Case 1**: Modify existing project — Change keyword extraction from "regex tokenization" to "AI model extraction"
> - **Case 2**: Build from scratch — Build a todo list management tool

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

---

## Case 2: Build from Scratch

> **Scenario**: User can't code and wants to build a "todo list management tool" (CLI version) from scratch.
>
> **Requirement**: A command-line todo tool that can add, view, complete, and delete tasks, with data stored in a local JSON file.

### Initialization

#### User selects Mode B

```text
Choose your mode:
B. Build from scratch (no codebase) → Start from 01-probe_en.md INIT branch
```

#### User sends initialization + supplement to Window B (Execution AI)

```text
【Role】You are the development workflow executor.
【Absolute Rules】(same as Case 1, abbreviated)

【Supplement: Build from Scratch Mode】
You will be building a project from scratch. Additional rules:
1. INIT stage only outputs design, does not generate code
2. Every new file must be runnable independently (at least no ImportError)
3. Skeleton build order: config files → utility/shared modules → entry files → business modules
4. After building each layer, verify the skeleton doesn't collapse (run/import check)
5. Tech stack selection must wait for user confirmation before proceeding
6. Acceptance criteria defined by user in INIT stage, checked against in VERIFY
```

---

### INIT Stage

#### User sends INIT instruction to Window B (Execution AI)

```text
【Stage: INIT】Build project from scratch, design skeleton first, do not modify code.

Requirement: Build a command-line todo list management tool
- Can add tasks
- Can view all tasks
- Can mark tasks as completed
- Can delete tasks
- Data stored in local JSON file

Please answer:
1. 【Tech Stack】Recommend tech stack based on requirements
2. 【Directory Structure】Design project directory tree
3. 【Dependency Planning】Core dependencies?
4. 【Entry Point Design】Main entry file? Startup method?
5. 【Config Design】What config files are needed?
6. 【Extension Planning】Future modules that may be extended
7. 【Acceptance Criteria】How to prove the project is "done"?

Output [INIT_DONE]
```

#### Execution AI Response

```text
## INIT Project Design Plan

### 1. 【Tech Stack】

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Language | Python 3.10+ | Easy to learn, rich stdlib, good for CLI tools |
| CLI Framework | argparse (stdlib) | No extra dependencies, sufficient functionality |
| Data Storage | JSON file | As required, lightweight, no dependencies |
| Package Manager | pip + requirements.txt | Standard Python approach |

### 2. 【Directory Structure】

```
todo-app/
├── main.py              # Main entry (CLI interface)
├── config.py            # Config file (data file paths etc.)
├── storage.py           # Data storage module (JSON read/write)
├── task.py              # Task data model
├── commands.py          # Command handling (add/view/complete/delete)
├── requirements.txt     # Dependency list
└── data/                # Data directory
    └── tasks.json       # Task data file (auto-generated at runtime)
```

### 3. 【Dependency Planning】

| Dependency | Version | Required | Purpose |
|------------|---------|----------|---------|
| Python | 3.10+ | Yes | Runtime |
| (no third-party deps) | - | - | All using stdlib |

### 4. 【Entry Point Design】

- Main entry: `main.py`
- Startup: `python main.py <command> [args]`
- Examples:
  - `python main.py add "Buy milk"` — Add task
  - `python main.py list` — View all tasks
  - `python main.py done 1` — Mark task 1 as completed
  - `python main.py delete 1` — Delete task 1

### 5. 【Config Design】

Config file: `config.py`

| Config Item | Default | Description |
|-------------|---------|-------------|
| DATA_DIR | ./data | Data directory path |
| TASKS_FILE | ./data/tasks.json | Task data file path |

### 6. 【Extension Planning】

- Future extensions: priority field, due dates, category tags
- Reserve: `task.py` Task class can add new fields

### 7. 【Acceptance Criteria】

- **Functionality**: Can add tasks, view list, mark complete, delete tasks
- **Persistence**: Data survives restart (JSON file)
- **Error handling**: Deleting non-existent ID shows error message
- **Verification**: Run all commands and check output

[INIT_DONE]
```

#### Strategy AI Response

```text
## INIT Review Results

### Step 1: Completeness Review
- [x] All 7 questions answered
- [x] Tech stack has specific framework and version (Python 3.10+, argparse)
- [x] Directory structure has complete tree
- [x] Dependency list has package names and versions (no third-party)
- [x] Entry file and startup method clear (python main.py <command>)
- [x] Config file checklist complete
- [x] Acceptance criteria specific and testable
- [x] Has [INIT_DONE] marker

Review passed.

### Step 2: Tech Stack Confirmation
- Python + argparse + JSON: fits requirements, no compatibility issues
- No third-party dependencies: simple deployment, recommend keeping

### Step 3: Next-Stage Instruction

【DESIGN-Architecture Design】

(full instruction in prompts/en/02-design_en.md Branch C)
```

---

### DESIGN Stage (from-scratch mode)

(Abbreviated, flow same as Case 1 DESIGN stage)

---

### CONTRACT Stage (from-scratch mode)

(Abbreviated, flow same as Case 1 CONTRACT stage)

---

### CODE Stage (from-scratch mode, layer by layer)

#### Layer 1: Config Layer

##### User sends CODE instruction

```text
【Stage: CODE-Skeleton-Config Layer】Create config files only. Do not touch other files.

Confirmed config checklist:
- config.py: DATA_DIR = "./data", TASKS_FILE = "./data/tasks.json"

Please create config file: config.py

Verification: python -c "from config import DATA_DIR, TASKS_FILE; print(DATA_DIR, TASKS_FILE)"

Add [FILE_DONE: config.py]
```

##### Execution AI Response

```python
# config.py
# Config file - define data storage paths

import os

# Data directory path
DATA_DIR = os.path.join(os.path.dirname(os.path.abspath(__file__)), "data")

# Task data file path
TASKS_FILE = os.path.join(DATA_DIR, "tasks.json")
```

```bash
$ python -c "from config import DATA_DIR, TASKS_FILE; print(DATA_DIR, TASKS_FILE)"
D:\todo-app\data D:\todo-app\data\tasks.json
```

```bash
$ git add config.py
$ git commit -m "CODE-Skeleton-Config: Create config.py, define data paths"
```

[FILE_DONE: config.py]

[LAYER_DONE: Config Layer]

(Strategy AI review passes, outputs next layer instruction)

#### Layer 2: Utility Layer (task.py + storage.py)

(Same flow, one file at a time, verify import after each)

#### Layer 3: Entry Layer (main.py)

(Same flow, verify `python main.py --help` works)

#### Layer 4: Business Layer (commands.py)

(Same flow, one file at a time)

---

### VERIFY Stage (from-scratch mode)

#### User sends VERIFY instruction

```text
【Stage: VERIFY-From Scratch】Execute acceptance testing with progress markers.

Acceptance criteria checklist (defined in INIT stage):
1. Can add tasks and save to JSON file
2. Can view all tasks
3. Can mark tasks as completed
4. Can delete tasks
5. Data persistence (data survives restart)

Please execute:
1. 【Environment Check】Confirm all dependencies installed, config correct
2. 【Startup Check】Start project, confirm no errors
3. 【Acceptance 1】Add task
4. 【Acceptance 2】View task list
5. 【Acceptance 3】Mark task complete
6. 【Acceptance 4】Delete task
7. 【Acceptance 5】Restart and verify persistence
8. 【Exception Test】Delete non-existent task ID
9. 【Summary】Output acceptance result summary
```

#### Execution AI Response

```text
[STEP 1/9: Environment Check] ✓ Config correct, data directory exists

[STEP 2/9: Startup Check] ✓ python main.py --help shows help info

[STEP 3/9: Acceptance 1-Add Task] ✓
$ python main.py add "Buy milk"
Task added: #1 Buy milk

[STEP 4/9: Acceptance 2-View Tasks] ✓
$ python main.py list
1. [ ] Buy milk

[STEP 5/9: Acceptance 3-Mark Complete] ✓
$ python main.py done 1
Task completed: #1 Buy milk

[STEP 6/9: Acceptance 4-Delete Task] ✓
$ python main.py delete 1
Task deleted: #1 Buy milk

[STEP 7/9: Acceptance 5-Persistence] ✓
$ python main.py add "Write report"
$ python main.py list
1. [ ] Write report
# Data persists after restart

[STEP 8/9: Exception Test] ✓
$ python main.py delete 999
Error: Task ID 999 not found

[STEP 9/9: Summary]
| Step | Result |
|------|--------|
| Environment Check | ✓ Pass |
| Startup Check | ✓ Pass |
| Add Task | ✓ Pass |
| View Tasks | ✓ Pass |
| Mark Complete | ✓ Pass |
| Delete Task | ✓ Pass |
| Persistence | ✓ Pass |
| Exception Handling | ✓ Pass |

All acceptance tests passed.
```

#### Strategy AI Response

```text
## VERIFY-From Scratch Analysis

### Step 1: Acceptance Result Analysis
1. 【Environment check passed?】Yes
2. 【Startup check passed?】Yes
3. 【Acceptance pass rate】5/5
4. 【Exception test passed?】Yes
5. 【Which acceptance tests failed?】None

### Step 2: Next Step Decision
All passed. Output [DONE] instruction.
```

---

### DEPLOY Stage (from-scratch mode)

#### User sends DEPLOY instruction

```text
【Stage: DEPLOY】Deploy to production, with progress markers.

Deployment target:
- Project: Todo List Management Tool
- Target platform: Local (CLI tool, no remote deployment needed)
- Deployment method: Manual install

Steps:
1. Environment check: Confirm Python ready
2. Dependency install: No third-party deps, stdlib only
3. Build: No build needed, skip
4. Deploy: Copy project to target directory, configure PATH
5. Smoke test: Run commands from new directory
6. Monitoring: Confirm no errors

Add [DEPLOY_DONE] at the end.
```

#### Execution AI Response

```text
[STEP 1/6: Environment Check] ✓
$ python --version
Python 3.12.0
Environment ready.

[STEP 2/6: Dependency Install] ✓
No third-party dependencies. Stdlib only. Skipping install.

[STEP 3/6: Build] ✓ Skipped (CLI tool, no build needed)

[STEP 4/6: Deploy] ✓
$ mkdir -p C:\Tools\todo-app
$ xcopy /E /I . C:\Tools\todo-app
7 files copied

[STEP 5/6: Smoke Test] ✓
$ cd C:\Tools\todo-app
$ python main.py add "Deploy test task"
Task added: #1 Deploy test task
$ python main.py list
1. [ ] Deploy test task
$ python main.py delete 1
Task deleted: #1 Deploy test task
Functions working.

[STEP 6/6: Monitoring] ✓
No error logs, running normally.

[DEPLOY_DONE]
```

#### Strategy AI Response

```text
## DEPLOY Analysis

### Step 1: Deployment Result Analysis
1. 【Environment check passed?】Yes. Python 3.12.0 ready
2. 【Dependencies installed?】Yes. No third-party deps
3. 【Build succeeded?】Skipped
4. 【Deployment succeeded?】Yes. Files copied to C:\Tools\todo-app
5. 【Smoke test passed?】Yes. Add/view/delete all working
6. 【Monitoring normal?】Yes. No error logs

### Step 2: Next Step Decision
All steps passed. Output [DONE-From Scratch] instruction.
```

---

### DONE Stage (from-scratch mode)

#### User sends DONE instruction

```text
【Stage: DONE-From Scratch】Project wrap-up, complete new build.

Verification passed:
- All acceptance tests passed
- Deployment verification passed

Please execute:
1. Complete README.md (project description, install, startup)
2. Complete environment config docs
3. Generate handover checklist
4. Final archive: git add + git commit + git tag v0.1
5. Smoke test: clone and install from scratch

Add [ALL_DONE]
```

#### Execution AI Response

```text
Documentation completed:

1. README.md:
   - Project: CLI todo list management tool
   - Install: Clone repo → no dependencies
   - Startup: python main.py <command> [args]
   - Usage: add/list/done/delete commands

2. Environment config: No extra config needed, works out of the box

3. Handover checklist:
   - Structure: 5 Python files + data directory
   - Key files: commands.py (core logic), storage.py (persistence)
   - Extensions: priority, due dates, category tags

4. Archive:
   $ git add .
   $ git commit -m "v0.1: Initial todo list management tool"
   $ git tag v0.1

5. Smoke test: Fresh clone and install verified

[ALL_DONE]
```

---

### Build Complete

```text
✅ Project build complete

Project: Todo List Management Tool (CLI)
Tech stack: Python 3.10+ + argparse + JSON
Files: config.py, task.py, storage.py, commands.py, main.py
Acceptance: All passed (add/view/complete/delete/persistence/exception handling)
Deployment: Verified working at C:\Tools\todo-app

Next: For new requirements, the project is now "existing" — restart from PROBE stage.
```
