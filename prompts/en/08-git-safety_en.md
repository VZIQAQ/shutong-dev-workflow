# 08 - Git Safety

> **Core idea**: Git rollback is the only reliable safety net. Without Git, if you break it, it's broken.

---

## Why Git?

| Scenario | Without Git | With Git |
|----------|-------------|----------|
| Broke something, want to rollback | Manual undo (may miss things) | `git checkout` one-click rollback |
| Not sure what changed | Scroll through chat history | `git diff` precise comparison |
| Want to keep multiple versions | Copy folders | `git branch` lightweight branches |
| Multi-person collaboration | Overwrite each other | `git merge` auto-merge |
| AI modified wrong file | Don't know what changed | `git status` clear view |

---

## Basic Command Reference

### Check Status

```bash
# See which files were modified
git status

# See what exactly changed
git diff

# See changes in a specific file
git diff filepath
```

### Commit Changes

```bash
# Add specific file (don't use git add .)
git add filepath

# Commit with a message describing what changed
git commit -m "PROBE stage: modified keyword extraction module"
```

### Rollback Changes

```bash
# Rollback a single file to last commit state
git checkout -- filepath

# Rollback all changes (dangerous! confirm first)
git checkout -- .
```

### View History

```bash
# View commit history
git log --oneline

# See what a specific commit changed
git show commit-hash
```

### Temporary Save

```bash
# Temporarily save current changes (want to switch branches without committing)
git stash

# Restore temporary save
git stash pop
```

---

## CODE Stage Git Checklist

Before each CODE stage, Execution AI must:

```text
【Git Checklist - Before Each Modification】
1. git status — confirm working directory is clean
2. Modify files
3. git diff — confirm changes are correct
4. git add filepath — add modified file (specify exact path, don't use .)
5. git commit -m "CODE-File N: brief description of changes"
6. git status — confirm commit succeeded
```

---

## Emergency Handling

### Case 1: Broke something, want to rollback to before a stage

```bash
# View commit history, find target commit
git log --oneline

# Rollback to target commit (keep changes in working directory)
git reset --soft commit-hash

# Rollback to target commit (discard all changes, dangerous!)
git reset --hard commit-hash
```

### Case 2: AI modified wrong file, want to undo

```bash
# See which files were modified
git status

# Undo changes to a specific file
git checkout -- filepath
```

### Case 3: Want to keep current changes but create a new branch

```bash
# Create and switch to new branch
git checkout -b new-branch-name

# Continue modifying on new branch
# If new branch gets messed up, switch back to main
git checkout main
```

### Case 4: Accidentally performed a destructive operation

```bash
# View operation history
git reflog

# Find the target operation's hash, rollback
git reset --hard target-hash
```

---

## Absolutely Forbidden Operations

```bash
# ❌ Forbidden: Force push to remote
git push --force

# ❌ Forbidden: Hard reset (loses all uncommitted changes)
git reset --hard

# ❌ Forbidden: Add all files at once (may include sensitive files)
git add .

# ❌ Forbidden: Delete branch (unless you're sure you don't need it)
git branch -D branch-name
```

---

## Execution AI's Git Rules

Execution AI's Git rules are set during initialization, see [00-setup_en.md](00-setup_en.md) under 【Git Safety Rules】. This document's command reference and emergency handling are supplementary to those rules.
