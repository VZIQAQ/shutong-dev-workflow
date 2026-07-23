# 完整对话实录示例

> 虚构一次完整改造的对话实录，展示双AI协作的实际过程。
>
> **场景**：用户有一个聊天机器人项目，想把关键词提取从"正则分词"改为"AI模型提取"。

---

## 开局

### 用户在窗口B（执行AI）发送初始化指令

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

### 执行AI回复

```text
已收到指令，等待用户提出需求和阶段指令。
```

---

## PROBE 阶段

### 用户在窗口B（执行AI）发送 PROBE 指令

```text
【阶段：PROBE】只查询，不改代码，不生成代码，不执行系统命令。

需求：把关键词提取从正则分词改为AI模型提取

请对当前代码库进行全面探测，回答以下问题...
（省略，完整提示词见 prompts/01-probe.md）
```

### 执行AI回复

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

### 用户复制执行AI的返回，发送给窗口A（策略AI）

```text
【PROBE审查任务】

请审查以下Mimo返回的PROBE结果，并判断代码状态。

## Mimo的PROBE返回：
（上面的完整内容）

## 你的任务：
（省略，完整提示词见 prompts/01-probe.md）
```

### 策略AI回复

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

## DESIGN 阶段

### 用户复制策略AI的指令，发送给窗口B（执行AI）

```text
【阶段：DESIGN】只回答设计问题，不写代码，不执行命令。

基于你刚才查到的代码，本次改造目标：把关键词提取从正则分词改为AI模型提取

当前代码状态（PROBE发现）：
- 关键词提取在 chat/keyword_extractor.py 的 extract_keywords()，使用正则分词
- 模型调用能力在 utils/ollama_client.py 的 call_model()
- 判断逻辑当前是逐条处理，在 chat/routes.py

请回答：
1. 【数据流】改造后的完整数据流是什么？
2. 【改造点】本次改造涉及哪几个文件？
3. 【调用关系】新增/修改的模块由谁调用？
4. 【回退策略】如果新逻辑失败，系统如何回退？
5. 【验证方案】如何证明改造生效？
6. 【风险点】改造可能破坏哪些现有功能？

回答清楚后，我回复"确认"你再写代码。
输出末尾加 [DESIGN_DONE]
```

### 执行AI回复

```text
## DESIGN 设计方案

### 1. 【数据流】
用户输入 → chat/routes.py:send_message() → chat/keyword_extractor.py:extract_keywords() → utils/ollama_client.py:call_model() → AI返回关键词 → 返回关键词列表

新增环节：call_model() 调用
修改环节：extract_keywords() 从正则改为调用AI
删除环节：re.findall() 正则分词

### 2. 【改造点】
1. chat/keyword_extractor.py（修改 extract_keywords 方法）
2. utils/ollama_client.py（已有，无需修改）
3. chat/routes.py（无需修改，调用方式不变）

### 3. 【调用关系】
chat/routes.py:send_message() → chat/keyword_extractor.py:extract_keywords() → utils/ollama_client.py:call_model()

### 4. 【回退策略】
如果 AI 调用失败，降级调用旧的正则分词方法：
```python
try:
    keywords = call_model(prompt)
except Exception:
    keywords = re.findall(r'\w+', text)  # 降级
```

### 5. 【验证方案】
在 extract_keywords() 中添加日志：
```python
logger.info(f"[PROBE] 输入文本: {text}")
logger.info(f"[PROBE] AI返回关键词: {keywords}")
```
发送测试消息，检查日志中是否有 AI 返回的关键词。

### 6. 【风险点】
- 如果 Ollama 服务未启动，会触发降级
- 如果 AI 返回格式不正确，需要解析处理

[DESIGN_DONE]
```

### 用户复制执行AI的返回，发送给窗口A（策略AI）

（省略，完整提示词见 prompts/02-design.md）

### 策略AI回复

```text
## DESIGN 审查结果

### 第一步：完整性审查
- [x] 有完整的数据流图
- [x] 列出了具体改造文件
- [x] 有明确的修改顺序
- [x] 回退策略清晰
- [x] 验证方案具体
- [x] 列出了风险点
- [x] 末尾有 [DESIGN_DONE] 标记

审查通过。

### 第二步：下一阶段指令

【CONTRACT-契约确认】

（完整指令见 prompts/03-contract.md）
```

---

## CONTRACT 阶段

（省略，流程同上，完整提示词见 prompts/03-contract.md）

---

## CODE 阶段（3个文件）

### 文件1：chat/keyword_extractor.py

#### 用户发送 CODE 指令

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

#### 执行AI回复

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

#### 违规示例：执行AI一次改了多个文件

```text
⚠️ 执行AI违规：一次输出了 keyword_extractor.py 和 routes.py 两个文件的代码
```

#### 用户用话术纠正

```text
停。一次只改一个文件。先只输出第一个文件的代码。
```

#### 执行AI修正

```text
已修正，只输出 keyword_extractor.py 的代码。
```

（后续文件2、文件3流程相同，省略）

---

## VERIFY 阶段

### 用户发送 VERIFY 指令

```text
【阶段：VERIFY】执行验证流程，带进度标记。

【执行铁律】
- 每完成一步，输出 [STEP X/5: 步骤名]
- 如果失败，输出 [STEP X/5 FAILED: 原因] 并停止
- VERIFY阶段只运行不改代码

步骤清单：
1. 保存所有修改的代码文件
2. 重启 Flask/服务（后台启动，不阻塞）
3. 等待3秒，确认服务就绪
4. 发送测试请求到 /api/send_message
5. 读取日志，筛选包含 [PROBE] 的行，输出分析

开始执行。
```

### 执行AI回复

```text
[STEP 1/5: 保存代码文件] ✓ 所有文件已保存

[STEP 2/5: 重启服务] ✓ 服务已在后台启动

[STEP 3/5: 等待服务就绪] ✓ 服务就绪

[STEP 4/5: 发送测试请求] ✓ 测试请求已发送

[STEP 5/5: 读取日志]
[PROBE] 输入文本: 今天天气真好，适合出去玩...
[PROBE] AI返回: 天气,出去玩,适合,今天,真好
```

### 用户复制执行AI的返回，发送给窗口A（策略AI）

### 策略AI回复

```text
## VERIFY 分析结果

### 第一步：日志分析
1. 【PROBE日志存在？】是。证据：日志中有 [PROBE] 标记的行
2. 【分析节点/新逻辑被调用？】是。证据：日志显示调用了 call_model()
3. 【模型返回了什么？】"天气,出去玩,适合,今天,真好"
4. 【是否触发降级？】否。证据：没有降级日志
5. 【关键词/输出是模型结果还是旧逻辑？】模型结果（"天气"等语义关键词，不是正则分词的单字）

### 第二步：根因判断
改造已生效，AI模型正常返回关键词。

### 第三步：下一步决策
改造已生效，输出【DONE】指令。

（完整指令见 prompts/06-done.md）
```

---

## DONE 阶段

### 用户发送 DONE 指令

```text
【阶段：DONE】清理探针，完成改造。

验证已通过：
- AI模型正常返回语义关键词
- 没有触发降级

请执行：
1. 移除所有带 [PROBE] 标记的临时日志代码
2. 保留正常的业务日志
3. 改完后做一次冒烟测试

输出修改后的文件列表和测试结果。
输出末尾加 [ALL_DONE]
```

### 执行AI回复

```text
已清理：
- 移除了 chat/keyword_extractor.py 中的 [PROBE] 临时日志（2行）
- 保留了业务日志（1行）

冒烟测试：
- 发送测试消息 ✓
- 收到正常回复 ✓
- 控制台无ERROR ✓

[ALL_DONE]
```

---

## 改造完成

```text
✅ 改造完成

改造内容：关键词提取从正则分词改为AI模型提取
涉及文件：chat/keyword_extractor.py
验证结果：AI模型正常返回语义关键词，没有触发降级

下一步：如有新需求，从 PROBE 阶段重新开始。
```
