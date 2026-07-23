# 08 - Git 安全保障

> **核心观点**：Git 回退是唯一可靠的后悔药。没有 Git，改崩了就是改崩了。

---

## 为什么必须 Git？

| 场景 | 没有 Git | 有 Git |
|------|---------|--------|
| 改崩了想回退 | 手动撤销（可能漏改） | `git checkout` 一键回退 |
| 不确定改了什么 | 翻聊天记录 | `git diff` 精确对比 |
| 想保留多个版本 | 复制文件夹 | `git branch` 轻量分支 |
| 多人协作 | 互相覆盖 | `git merge` 自动合并 |
| AI改错了文件 | 不知道改了哪些 | `git status` 一目了然 |

---

## 基础命令速查

### 查看状态

```bash
# 查看哪些文件被修改了
git status

# 查看具体改了什么
git diff

# 查看某个文件的修改
git diff 文件路径
```

### 提交修改

```bash
# 添加指定文件（不要用 git add .）
git add 文件路径

# 提交，写明改了什么
git commit -m "PROBE阶段：修改了关键词提取模块"
```

### 回退修改

```bash
# 回退单个文件到上次提交的状态
git checkout -- 文件路径

# 回退所有修改（危险！确认后再执行）
git checkout -- .
```

### 查看历史

```bash
# 查看提交历史
git log --oneline

# 查看某次提交改了什么
git show 提交哈希
```

### 临时保存

```bash
# 临时保存当前修改（不想提交但想切换分支）
git stash

# 恢复临时保存
git stash pop
```

---

## CODE 阶段 Git Checklist

每次进入 CODE 阶段前，执行AI 必须：

```text
【Git Checklist - 每次修改前】
1. git status — 确认工作区干净
2. 修改文件
3. git diff — 确认修改内容正确
4. git add 文件路径 — 添加修改的文件（指定具体路径，不要用 .）
5. git commit -m "CODE-文件N：简述改了什么"
6. git status — 确认提交成功
```

---

## 紧急情况处理

### 情况1：改崩了，想回到某个阶段之前

```bash
# 查看提交历史，找到目标提交
git log --oneline

# 回退到目标提交（保留修改在工作区）
git reset --soft 提交哈希

# 回退到目标提交（丢弃所有修改，危险！）
git reset --hard 提交哈希
```

### 情况2：AI 改错了文件，想撤销

```bash
# 查看哪些文件被修改了
git status

# 撤销某个文件的修改
git checkout -- 文件路径
```

### 情况3：想保留当前修改，但创建一个新分支

```bash
# 创建新分支并切换
git checkout -b 新分支名

# 在新分支上继续修改
# 如果新分支改崩了，切回主分支
git checkout main
```

### 情况4：不小心执行了破坏性操作

```bash
# 查看操作历史
git reflog

# 找到目标操作的哈希，回退
git reset --hard 目标哈希
```

---

## 绝对禁止的操作

```bash
# ❌ 禁止：强制推送到远程仓库
git push --force

# ❌ 禁止：强制重置（会丢失所有未提交的修改）
git reset --hard

# ❌ 禁止：一次性添加所有文件（可能包含敏感文件）
git add .

# ❌ 禁止：删除分支（除非你确定不再需要）
git branch -D 分支名
```

---

## 执行AI的 Git 铁律

执行AI的 Git 铁律已在初始化时设定，详见 [00-setup.md](00-setup.md) 中的【Git保障铁律】部分。本文档的命令速查和紧急情况处理是对铁律的补充说明。
