# 完整对话实录示例

> 虚构完整改造的对话实录，展示双AI协作的实际过程。
>
> 包含两个案例：
> - **案例1**：改造现有项目 — 把关键词提取从"正则分词"改为"AI模型提取"
> - **案例2**：从零新建项目 — 搭建一个待办事项管理工具

---

## 案例1：改造现有项目

> **场景**：用户有一个聊天机器人项目，想把关键词提取从"正则分词"改为"AI模型提取"。

### 开局

#### 用户在窗口B（执行AI）发送初始化指令

```text
【角色】你是开发流程执行器。
【绝对铁律】
1. 用户没有说"确认"，你绝不进入下一阶段
2. 你绝不一次性修改多个文件
3. 每个阶段完成后，输出 [STATE: 阶段名] 标记
4. 如果用户说"回退"，回到上一状态重新执行
5. 编码阶段每改完一个文件，输出 [FILE_DONE: 文件名]
6. 长链条执行时，每步输出 [STEP X/N: 步骤名]，失败输出 [STEP X/N FAILED: 原因]
7. 完成后停止，等待用户发"下一步"
8. 任何阶段发现异常，立即停止并报告，不要自行修复

【Git保障铁律】
1. 每次修改代码前，必须先用 git status 检查工作区
2. 每完成一个文件修改，必须 git add + git commit（提交信息写明改了什么）
3. 如果修改失败，必须 git checkout 回退到上一次提交
4. 禁止 git push --force、git reset --hard 等破坏性操作
5. 禁止一次性 git add . （必须指定具体文件）

现在等待用户提出需求和阶段指令。
```

#### 执行AI回复

```text
已收到指令，等待用户提出需求和阶段指令。
```

---

### PROBE 阶段

#### 用户在窗口B（执行AI）发送 PROBE 指令

```text
【阶段：PROBE】只查询，不改代码，不生成代码，不执行系统命令。

需求：把关键词提取从正则分词改为AI模型提取

请对当前代码库进行全面探测，回答以下问题...
（省略，完整提示词见 prompts/01-probe.md）
```

#### 执行AI回复

```text
## PROBE 探测报告

### 1. 【现状定位】
- 后端：
  - 文件：`chat/keyword_extractor.py`
  - 方法：`extract_keywords(text: str) -> List[str]`
  - 代码片段：
    ```python
    def extract_keywords(text: str) -> List[str]:
        # 正则分词
        words = re.findall(r'\w+', text)
        return words[:5]
    ```

### 2. 【调用链】
用户输入 → `chat/routes.py:send_message()` → `chat/keyword_extractor.py:extract_keywords()` → 返回关键词列表

### 3. 【外部能力】
- 文件：`utils/ollama_client.py`
- 方法：`call_model(prompt: str) -> str`
- 调用方式：HTTP POST 到 `http://localhost:11434/api/generate`

### 4. 【数据结构】
- 无独立数据结构，关键词直接返回 `List[str]`

### 5. 【判断逻辑】
- 逐条处理，每个用户消息单独提取关键词
- 在 `chat/routes.py:send_message()` 中调用

### 6. 【扩展点】
- 新模块应放在 `chat/` 目录
- 命名规范：`xxx_extractor.py`

[PROBE_DONE]
```

#### 用户复制执行AI的返回，发送给窗口A（策略AI）

```text
【PROBE审查任务】

请审查以下Mimo返回的PROBE结果，并判断代码状态。

## Mimo的PROBE返回：
（上面的完整内容）

## 你的任务：
（省略，完整提示词见 prompts/01-probe.md）
```

#### 策略AI回复

```text
## PROBE 审查结果

### 第一步：完整性审查
- [x] 6个问题全部回答
- [x] 有具体的文件路径（chat/keyword_extractor.py）
- [x] 有方法/函数名和行号范围
- [x] 关键代码完整贴出
- [x] 末尾有 [PROBE_DONE] 标记
- [ ] 如果涉及前端，PROBE有前端部分（不涉及，跳过）

审查通过。

### 第二步：状态判断

**状态A：全新**

判断依据：
- 当前关键词提取使用正则分词（re.findall）
- 没有 AI 模型提取的相关代码
- 没有 analysis_node.py 或类似的新模块

### 第三步：下一阶段指令

【DESIGN-从零改造】

（完整指令见 prompts/02-design.md 分支A）
```

---

### DESIGN 阶段

（流程同上，完整提示词见 prompts/02-design.md）

---

### CONTRACT 阶段

（省略，流程同上，完整提示词见 prompts/03-contract.md）

---

### CODE 阶段（3个文件）

#### 文件1：chat/keyword_extractor.py

##### 用户发送 CODE 指令

```text
【阶段：CODE-文件1】只改这一个文件，不要动其他文件。

【绝对铁律】
1. 用户没有说"确认"，绝不进入下一个文件
2. 绝不一次性修改多个文件
3. 每改完一个文件，必须 git add + git commit
4. 如果修改失败，必须 git checkout 回退
5. 禁止 git push --force、git reset --hard
6. 禁止一次性 git add .

修改 文件：chat/keyword_extractor.py

改造目标：把 extract_keywords() 从正则分词改为调用 AI 模型

当前项目上下文（PROBE发现）：
- 模型调用封装在 utils/ollama_client.py 的 call_model()
- 旧方法 extract_keywords() 在 chat/keyword_extractor.py 保留备用

要求：
- 只输出变更部分的代码，用注释标明插入位置
- 关键路径必须加 [PROBE] 日志
- 所有异常必须捕获
- 代码风格与项目现有代码保持一致
- 不要重启服务，不要运行测试，不要读取日志
- 只输出这个文件的代码

输出后加 [FILE_DONE: keyword_extractor.py]
等待我发"下一步"再继续。
```

##### 执行AI回复

```python
# chat/keyword_extractor.py

import re
import logging
from typing import List
from utils.ollama_client import call_model

logger = logging.getLogger(__name__)

def extract_keywords(text: str) -> List[str]:
    """从文本中提取关键词（AI模型版本）"""
    try:
        logger.info(f"[PROBE] 输入文本: {text[:100]}...")
        
        prompt = f"请从以下文本中提取5个关键词，用逗号分隔：\n{text}"
        result = call_model(prompt)
        
        logger.info(f"[PROBE] AI返回: {result}")
        
        keywords = [kw.strip() for kw in result.split(",")]
        return keywords[:5]
        
    except Exception as e:
        logger.error(f"[PROBE] AI调用失败，降级到正则分词: {e}")
        # 降级：使用旧的正则分词
        words = re.findall(r'\w+', text)
        return words[:5]
```

```bash
$ git add chat/keyword_extractor.py
$ git commit -m "CODE-文件1：修改关键词提取，从正则改为AI模型"
```

[FILE_DONE: keyword_extractor.py]

##### 违规示例：执行AI一次改了多个文件

```text
⚠️ 执行AI违规：一次输出了 keyword_extractor.py 和 routes.py 两个文件的代码
```

##### 用户用话术纠正

```text
停。一次只改一个文件。先只输出第一个文件的代码。
```

##### 执行AI修正

```text
已修正，只输出 keyword_extractor.py 的代码。
```

（后续文件2、文件3流程相同，省略）

---

### VERIFY 阶段

（省略，完整提示词见 prompts/05-verify.md）

---

### DONE 阶段

（省略，完整提示词见 prompts/06-done.md）

---

### 改造完成

```text
✅ 改造完成

改造内容：关键词提取从正则分词改为AI模型提取
涉及文件：chat/keyword_extractor.py
验证结果：AI模型正常返回语义关键词，没有触发降级

下一步：如有新需求，从 PROBE 阶段重新开始。
```

---

## 案例2：从零新建项目

> **场景**：用户不会编程，想从零搭建一个"待办事项管理工具"（命令行版本）。
>
> **需求**：一个命令行待办事项工具，可以添加、查看、完成、删除任务，数据保存在本地文件中。

### 开局

#### 用户选择模式B

```text
请选择模式：
B. 从零新建项目（无代码库）→ 从 01-probe.md 的 INIT 分支开始
```

#### 用户在窗口B（执行AI）发送初始化指令 + 补充指令

```text
【角色】你是开发流程执行器。
【绝对铁律】（同案例1，省略）

【补充指令：从零新建模式】
你接下来要从零搭建一个项目。补充以下规则：
1. INIT阶段只输出设计，不生成代码
2. 每个新建的文件必须能独立运行（至少不报ImportError）
3. 骨架搭建顺序：配置文件 → 工具类/公共模块 → 入口文件 → 业务模块
4. 每搭完一层，必须验证骨架不塌（运行/导入检查）
5. 技术选型必须等待用户确认后才进入下一阶段
6. 验收条件由用户在INIT阶段定义，VERIFY时对照检查
```

---

### INIT 阶段

#### 用户在窗口B（执行AI）发送 INIT 指令

```text
【阶段：INIT】从零新建项目，先搭建骨架，不改代码。

需求：搭建一个命令行待办事项管理工具
- 可以添加任务
- 可以查看所有任务
- 可以标记任务为已完成
- 可以删除任务
- 数据保存在本地JSON文件中

请回答：
1. 【技术选型】根据需求，推荐技术栈
2. 【目录结构】设计项目目录树
3. 【依赖规划】核心依赖有哪些？
4. 【入口设计】主入口文件是什么？启动方式？
5. 【配置设计】需要哪些配置文件？
6. 【扩展预留】未来可能扩展的模块

要求：
- 只输出设计，不生成代码
- 如果有不确定的技术选型，列出选项和利弊，等待用户确认
- 输出 [INIT_DONE]
```

#### 执行AI回复

```text
## INIT 项目设计方案

### 1. 【技术选型】

| 组件 | 选择 | 理由 |
|------|------|------|
| 语言 | Python 3.10+ | 简单易学，标准库丰富，适合命令行工具 |
| 命令行框架 | argparse（标准库） | 无需额外依赖，功能足够 |
| 数据存储 | JSON文件 | 需求明确要求，轻量无依赖 |
| 包管理 | pip + requirements.txt | Python标准方式 |

备选方案：
- 命令行框架也可以用 click（更现代），但需要额外安装
- 数据存储也可以用 SQLite，但JSON更简单直观

### 2. 【目录结构】

```
todo-app/
├── main.py              # 主入口（命令行界面）
├── config.py            # 配置文件（数据文件路径等）
├── storage.py           # 数据存储模块（JSON读写）
├── task.py              # 任务数据模型
├── commands.py          # 命令处理模块（添加/查看/完成/删除）
├── requirements.txt     # 依赖列表
└── data/                # 数据目录
    └── tasks.json       # 任务数据文件（运行时自动生成）
```

### 3. 【依赖规划】

| 依赖 | 版本 | 必须 | 用途 |
|------|------|------|------|
| Python | 3.10+ | 是 | 运行环境 |
| （无第三方依赖） | - | - | 全部使用标准库 |

### 4. 【入口设计】

- 主入口文件：`main.py`
- 启动方式：`python main.py <命令> [参数]`
- 示例：
  - `python main.py add "买牛奶"` — 添加任务
  - `python main.py list` — 查看所有任务
  - `python main.py done 1` — 标记任务1为已完成
  - `python main.py delete 1` — 删除任务1

### 5. 【配置设计】

配置文件：`config.py`

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| DATA_DIR | ./data | 数据目录路径 |
| TASKS_FILE | ./data/tasks.json | 任务数据文件路径 |

### 6. 【扩展预留】

- 未来可扩展：优先级字段、截止日期、分类标签
- 预留位置：`task.py` 的 Task 类中可新增字段

[INIT_DONE]
```

#### 用户复制执行AI的返回，发送给窗口A（策略AI）

#### 策略AI回复

```text
## INIT 审查结果

### 第一步：完整性审查
- [x] 6个问题全部回答
- [x] 技术选型有具体框架名和版本号（Python 3.10+，argparse）
- [x] 目录结构有完整的树状图
- [x] 依赖列表有包名和版本（无第三方依赖）
- [x] 入口文件和启动方式明确（python main.py <命令>）
- [x] 配置文件清单完整
- [x] 末尾有 [INIT_DONE] 标记

审查通过。

### 第二步：技术选型确认
- Python + argparse + JSON：适合需求，无兼容性问题
- 无第三方依赖：部署简单，推荐保持

### 第三步：下一阶段指令

【DESIGN-架构设计】

（完整指令见 prompts/02-design.md 分支C）
```

---

### DESIGN 阶段（从零模式）

#### 用户发送 DESIGN 指令

```text
【阶段：DESIGN-架构设计】只回答设计问题，不写代码，不执行命令。

基于 INIT 阶段确认的方案，本次目标：搭建命令行待办事项管理工具

已确认的技术栈：
- Python 3.10+
- argparse（标准库）
- JSON文件存储

已确认的目录结构：
todo-app/
├── main.py
├── config.py
├── storage.py
├── task.py
├── commands.py
├── requirements.txt
└── data/
    └── tasks.json

请回答：
1. 【数据流】项目的核心数据流是什么？
2. 【骨架搭建顺序】按依赖关系排列需要创建的文件
3. 【模块依赖图】各模块之间的调用关系
4. 【清理策略】搭建失败时如何清理？
5. 【验证方案】每搭完一层如何验证？
6. 【风险点】搭建过程中可能遇到什么问题？

输出末尾加 [DESIGN_DONE]
```

#### 执行AI回复

```text
## DESIGN-架构设计

### 1. 【数据流】
用户输入命令 → main.py（解析命令）→ commands.py（处理逻辑）→ storage.py（读写数据）→ data/tasks.json

### 2. 【骨架搭建顺序】
1. config.py（被所有模块依赖）
2. task.py（数据模型，被storage和commands依赖）
3. storage.py（依赖config和task）
4. commands.py（依赖storage和task）
5. main.py（依赖commands和config）
6. requirements.txt
7. data/目录

### 3. 【模块依赖图】
main.py → commands.py → storage.py → config.py
                     → task.py

### 4. 【清理策略】
- 搭建失败时，删除已创建的文件，回到空目录状态
- git commit 记录每一步，可精确回退

### 5. 【验证方案】
- 配置层：导入config.py，验证DATA_DIR和TASKS_FILE有值
- 工具层：导入task.py和storage.py，验证能创建Task对象和读写JSON
- 业务层：运行 main.py add "测试"，验证任务被保存

### 6. 【风险点】
- JSON文件不存在时的首次读取（需要处理空文件）
- 任务ID生成（需要自增）

[DESIGN_DONE]
```

（策略AI审查通过，省略）

---

### CONTRACT 阶段（从零模式）

#### 用户发送 CONTRACT 指令

```text
【阶段：CONTRACT-从零】设计接口和数据模型，不写代码，不执行命令。

基于 DESIGN 阶段确认的架构设计：
main.py → commands.py → storage.py → config.py
                     → task.py

【技术选型（已确认）】
- Python 3.10+
- argparse（标准库）
- JSON文件存储

请回答：
1. 【数据模型设计】核心数据实体有哪些？
2. 【API接口设计】需要哪些接口？
3. 【模块接口设计】各模块之间的函数调用接口
4. 【异常处理策略】每个模块的异常如何处理？
5. 【技术选型确认】复述已确认的技术栈
6. 【编码纪律确认】编码阶段是否一次只创建一个文件？

输出末尾加 [CONTRACT_DONE]
```

#### 执行AI回复

```text
## CONTRACT-从零 接口设计

### 1. 【数据模型设计】

```python
@dataclass
class Task:
    id: int           # 任务ID（自增）
    title: str        # 任务标题
    done: bool        # 是否完成（默认False）
    created_at: str   # 创建时间（ISO格式）
```

### 2. 【API接口设计】

本项目是命令行工具，接口为命令：

| 命令 | 参数 | 返回 | 说明 |
|------|------|------|------|
| add | title: str | Task | 添加任务 |
| list | 无 | List[Task] | 查看所有任务 |
| done | id: int | Task | 标记完成 |
| delete | id: int | bool | 删除任务 |

### 3. 【模块接口设计】

```python
# config.py
DATA_DIR: str
TASKS_FILE: str

# task.py
class Task:
    def __init__(self, id: int, title: str, done: bool, created_at: str): ...
    def to_dict(self) -> dict: ...
    @classmethod
    def from_dict(cls, data: dict) -> Task: ...

# storage.py
def load_tasks() -> List[Task]: ...
def save_tasks(tasks: List[Task]) -> None: ...
def get_next_id(tasks: List[Task]) -> int: ...

# commands.py
def cmd_add(title: str) -> Task: ...
def cmd_list() -> List[Task]: ...
def cmd_done(task_id: int) -> Task: ...
def cmd_delete(task_id: int) -> bool: ...

# main.py
def main() -> None: ...  # 解析命令并调用commands
```

### 4. 【异常处理策略】

| 模块 | 异常类型 | 处理方式 |
|------|----------|----------|
| storage | 文件不存在 | 自动创建空文件 |
| storage | JSON格式错误 | 报错并提示用户检查文件 |
| commands | 任务ID不存在 | 报错并提示有效ID列表 |
| main | 未知命令 | 显示帮助信息 |

### 5. 【技术选型确认】
- Python 3.10+
- argparse（标准库）
- JSON文件存储
- 无第三方依赖

### 6. 【编码纪律确认】
确认：一次只创建一个文件。

[CONTRACT_DONE]
```

（策略AI审查通过，省略）

---

### CODE 阶段（从零模式，逐层搭建）

#### 第1层：配置层

##### 用户发送 CODE 指令

```text
【阶段：CODE-骨架-配置层】只创建配置文件，不要动其他文件。

已确认的配置清单：
- config.py：DATA_DIR = "./data", TASKS_FILE = "./data/tasks.json"

请创建配置文件：config.py

要求：
- 输出完整代码
- 配置文件必须有合理的默认值
- 必须有注释说明每个配置项的用途

验证方式：python -c "from config import DATA_DIR, TASKS_FILE; print(DATA_DIR, TASKS_FILE)"

输出后加 [FILE_DONE: config.py]
```

##### 执行AI回复

```python
# config.py
# 配置文件 - 定义数据存储路径

import os

# 数据目录路径
DATA_DIR = os.path.join(os.path.dirname(os.path.abspath(__file__)), "data")

# 任务数据文件路径
TASKS_FILE = os.path.join(DATA_DIR, "tasks.json")
```

```bash
$ python -c "from config import DATA_DIR, TASKS_FILE; print(DATA_DIR, TASKS_FILE)"
D:\todo-app\data D:\todo-app\data\tasks.json
```

```bash
$ git add config.py
$ git commit -m "CODE-骨架-配置层：创建config.py，定义数据路径"
```

[FILE_DONE: config.py]

[LAYER_DONE: 配置层]

##### 用户复制给策略AI审查

（策略AI审查通过，输出下一层指令）

#### 第2层：工具层（task.py + storage.py）

（流程相同，逐个文件创建，每个文件创建后验证导入）

#### 第3层：入口层（main.py）

（流程相同，创建后验证能运行 `python main.py --help`）

#### 第4层：业务层（commands.py）

（流程相同，逐个文件创建）

---

### VERIFY 阶段（从零模式）

#### 用户发送 VERIFY 指令

```text
【阶段：VERIFY-从零】执行功能验收，带进度标记。

验收条件清单（INIT阶段定义）：
1. 能添加任务并保存到JSON文件
2. 能查看所有任务列表
3. 能标记任务为已完成
4. 能删除任务
5. 数据持久化（重启后数据不丢失）

请执行以下验收步骤：
1. 【环境检查】确认所有依赖已安装，配置文件正确
2. 【启动检查】启动项目，确认无报错
3. 【功能验收1】添加任务
4. 【功能验收2】查看任务列表
5. 【功能验收3】标记任务完成
6. 【功能验收4】删除任务
7. 【功能验收5】重启后验证数据持久化
8. 【异常验收】删除不存在的任务ID
9. 【汇总】输出验收结果汇总表
```

#### 执行AI回复

```text
[STEP 1/9: 环境检查] ✓ 配置文件正确，数据目录存在

[STEP 2/9: 启动检查] ✓ python main.py --help 正常显示帮助信息

[STEP 3/9: 功能验收1-添加任务] ✓
$ python main.py add "买牛奶"
任务已添加：#1 买牛奶

[STEP 4/9: 功能验收2-查看任务] ✓
$ python main.py list
1. [ ] 买牛奶

[STEP 5/9: 功能验收3-标记完成] ✓
$ python main.py done 1
任务已完成：#1 买牛奶

[STEP 6/9: 功能验收4-删除任务] ✓
$ python main.py delete 1
任务已删除：#1 买牛奶

[STEP 7/9: 功能验收5-数据持久化] ✓
$ python main.py add "写报告"
$ python main.py list
1. [ ] 写报告
# 重启后数据仍在

[STEP 8/9: 异常验收] ✓
$ python main.py delete 999
错误：任务ID 999 不存在

[STEP 9/9: 汇总]
| 步骤 | 结果 |
|------|------|
| 环境检查 | ✓ 通过 |
| 启动检查 | ✓ 通过 |
| 添加任务 | ✓ 通过 |
| 查看任务 | ✓ 通过 |
| 标记完成 | ✓ 通过 |
| 删除任务 | ✓ 通过 |
| 数据持久化 | ✓ 通过 |
| 异常处理 | ✓ 通过 |

全部验收通过。
```

#### 策略AI回复

```text
## VERIFY-从零分析结果

### 第一步：验收结果分析
1. 【环境检查通过？】是
2. 【启动检查通过？】是
3. 【功能验收通过率】5/5
4. 【异常验收通过？】是
5. 【哪些功能验收失败？】无

### 第二步：下一步决策
所有验收通过，输出【DONE】指令。
```

---

### DEPLOY 阶段（从零模式）

#### 用户发送 DEPLOY 指令

```text
【阶段：DEPLOY】部署上线，带进度标记。

【执行铁律】
- 每完成一步，输出 [STEP X/6: 步骤名]
- 如果失败，输出 [STEP X/6 FAILED: 原因] 并停止
- DEPLOY阶段只执行部署操作，不修改业务代码

部署目标：
- 项目：待办事项管理工具
- 目标平台：本地（命令行工具，无需远程部署）
- 部署方式：手动安装

步骤清单：

1. 【环境检查】确认Python环境就绪
2. 【依赖安装】确认无第三方依赖，标准库即可
3. 【构建打包】无需构建，跳过
4. 【部署执行】将项目文件复制到目标目录，配置PATH
5. 【冒烟测试】从新目录运行命令，验证功能正常
6. 【监控确认】确认无报错日志

输出末尾加 [DEPLOY_DONE]
开始执行。
```

#### 执行AI回复

```text
[STEP 1/6: 环境检查] ✓
$ python --version
Python 3.12.0
环境就绪。

[STEP 2/6: 依赖安装] ✓
无第三方依赖，标准库即可。跳过安装。

[STEP 3/6: 构建打包] ✓ 跳过（命令行工具无需构建）

[STEP 4/6: 部署执行] ✓
$ mkdir -p C:\Tools\todo-app
$ xcopy /E /I . C:\Tools\todo-app
已复制 7 个文件

[STEP 5/6: 冒烟测试] ✓
$ cd C:\Tools\todo-app
$ python main.py add "部署测试任务"
任务已添加：#1 部署测试任务
$ python main.py list
1. [ ] 部署测试任务
$ python main.py delete 1
任务已删除：#1 部署测试任务
功能正常。

[STEP 6/6: 监控确认] ✓
无报错日志，运行正常。

[DEPLOY_DONE]
```

#### 策略AI回复

```text
## DEPLOY 分析结果

### 第一步：部署结果分析
1. 【环境检查通过？】是。Python 3.12.0 就绪
2. 【依赖安装成功？】是。无第三方依赖
3. 【构建打包成功？】跳过
4. 【部署执行成功？】是。文件已复制到 C:\Tools\todo-app
5. 【冒烟测试通过？】是。添加/查看/删除功能均正常
6. 【监控确认正常？】是。无报错日志

### 第二步：下一步决策
所有步骤通过，输出【DONE-从零模式】指令。
```

---

### DONE 阶段（从零模式）

#### 用户发送 DONE 指令

```text
【阶段：DONE-从零模式】项目收尾，完成新建。

验证已通过：
- 功能验收全部通过（添加/查看/完成/删除/持久化/异常处理）
- 部署验证通过（冒烟测试正常）

请执行：
1. 补全项目文档：README.md（项目说明、安装步骤、启动方式）
2. 补全环境配置说明：.env.example 或配置文档
3. 生成交接清单：项目结构说明、关键文件位置、后续扩展建议
4. 最终归档：git add + git commit + git tag v0.1
5. 冒烟测试：按README步骤从零克隆安装，确认能跑通

输出修改后的文件列表和归档结果。
输出末尾加 [ALL_DONE]
```

#### 执行AI回复

```text
已补全文档：

1. README.md：
   - 项目说明：命令行待办事项管理工具
   - 安装步骤：克隆仓库 → 无依赖
   - 启动方式：python main.py <命令> [参数]
   - 使用示例：add/list/done/delete 四个命令

2. 环境配置：无额外配置，开箱即用

3. 交接清单：
   - 项目结构：5个Python文件 + data目录
   - 关键文件：commands.py（核心逻辑）、storage.py（数据持久化）
   - 扩展建议：可添加优先级字段、截止日期、分类标签

4. 归档：
   $ git add .
   $ git commit -m "v0.1: 待办事项管理工具初始版本"
   $ git tag v0.1

5. 冒烟测试：从零克隆安装验证通过

[ALL_DONE]
```

---

### 从零搭建完成

```text
✅ 项目搭建完成

项目：待办事项管理工具（命令行）
技术栈：Python 3.10+ + argparse + JSON
涉及文件：config.py, task.py, storage.py, commands.py, main.py
验收结果：全部通过（添加/查看/完成/删除/持久化/异常处理）

下一步：如有新需求，从 PROBE 阶段重新开始（此时项目已变为"改造现有项目"）。
```
