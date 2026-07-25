# 09 - DEPLOY: Deploy to Production

> **Stage goal**: Deploy the verified project to production and ensure it runs correctly.
>
> For from-scratch projects only. Deployment of existing project modifications is handled by the user.

---

## Execution AI Prompt (send to Window B)

```text
【Stage: DEPLOY】Deploy to production, with progress markers.

【Execution Rules】
- After completing each step, output [STEP X/6: step_name]
- If a step fails, output [STEP X/6 FAILED: reason] and stop
- User may stop midway and specify "continue from step X"
- DEPLOY stage only executes deployment, does not modify business code

Deployment target:
- Project: {{project name}}
- Target platform: {{server/cloud/local}}
- Deployment method: {{manual/automatic}}

Step checklist:

1. 【Environment Check】
   - Confirm target environment is ready (server/container/cloud)
   - Confirm environment variables are configured (list all required env vars)
   - Confirm dependency versions match development environment

2. 【Dependency Install】
   - Install project dependencies in target environment
   - Verify dependencies installed successfully (no errors, correct versions)

3. 【Build & Package】(if build step exists)
   - Execute build command
   - Verify build artifacts are complete

4. 【Deploy Execution】
   - Execute deployment per the deployment method
   - Record deployment logs

5. 【Smoke Test】
   - Access production URL/endpoints
   - Verify core functionality works (at least 3 core endpoints/pages)
   - Record response times

6. 【Monitoring Check】
   - Confirm log output is normal (no ERROR)
   - Confirm monitoring metrics are normal (if applicable)
   - Confirm alert rules are configured (if applicable)

Output format per step:
[STEP X/6: step_name] ✓ Pass / ✗ Fail (reason)

【Rollback Strategy】
- On deployment failure: {{rollback method, e.g. "restore previous version backup"}}
- On data migration failure: {{data recovery method}}
- On critical production bug: {{emergency rollback procedure}}

Add [DEPLOY_DONE] at the end of output.
Begin execution.
```

---

## Wait for Execution AI Response

You will receive deployment results with `[STEP X/6]` markers.

### If Deployment Fails

- Stop Execution AI
- Send the failed step and reason to Strategy AI for analysis
- Strategy AI will determine whether to fix code or adjust deployment plan

---

## Strategy AI Prompt (send to Window A)

Replace `{{Execution AI's DEPLOY response}}` with what you copied:

```text
【DEPLOY Analysis Task】

Please analyze the following deployment results returned by the Execution AI.

## Execution AI's DEPLOY Response:
{{Execution AI's DEPLOY response}}

## Your Task:

### Step 1: Deployment Result Analysis
1. 【Environment check passed?】Yes/No. Evidence:
2. 【Dependencies installed successfully?】Yes/No. Evidence:
3. 【Build & package succeeded?】Yes/No/Skipped. Evidence:
4. 【Deployment executed successfully?】Yes/No. Evidence:
5. 【Smoke test passed?】Yes/No. Evidence:
6. 【Monitoring check normal?】Yes/No. Evidence:

### Step 2: Next Step Decision
- If all steps passed: Output [DONE] instruction (see 06-done_en.md)
- If deployment failed: Output rollback instruction, let user confirm whether to rollback
- If smoke test failed: Output [CODE-Fix] instruction (fall back to 04-code_en.md)
- If monitoring abnormal: Output troubleshooting instruction
```

---

## Deployment Checklist

Confirm the following:

- [ ] Target environment ready
- [ ] Environment variables configured
- [ ] Dependencies installed successfully
- [ ] Build & package succeeded (if applicable)
- [ ] Deployment executed successfully
- [ ] Smoke test passed (core functionality works)
- [ ] No ERROR in logs
- [ ] Monitoring normal
- [ ] Rollback plan confirmed

---

## Next

Deployment complete. Proceed to [06-done_en.md](06-done_en.md) for final cleanup.
