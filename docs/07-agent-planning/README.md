# 第7章：让 Agent 学会规划

> 作者：青松与桑叶
> 本系列教程定位：保姆级、通俗易懂、每一步都可运行、中文原创

---

## 7.1 为什么需要规划

> **先明确"规划"这个词的所指**：本章讲的是 **任务分解与步骤生成**——把一个复杂目标拆成若干可执行的步骤，并决定顺序。
>
> 如果你有 RL 背景，请注意 RL 里的 planning 是另一回事：它指 **在已知环境模型时于模型内部搜索最优动作序列**（值迭代、策略迭代、MCTS 前瞻搜索等），依赖显式的 `P(s'∣s,a)` 与 `R(s,a)`，目标是累积回报最优、有收敛性结论。本章的 Plan-and-Solve 没有环境模型、不保证最优，本质是 LLM 生成的启发式计划。两者的关系只在于都要"决定接下来做什么"，方法论上没有等价性。
>
> 更完整的术语对照见[第1章 1.2 与 1.4 节](../01-what-is-agent/README.md)，组件层面的说明见[第3章 3.4 节](../03-agent-components/README.md)。

在前面的章节中，我们构建的 Agent 使用的是 **ReAct 模式** —— 边思考边行动。对于简单任务，这种方式很好用。但面对复杂任务，ReAct 模式有时会出问题。

### 简单任务 vs 复杂任务

```
简单任务（ReAct 足够）：
  "北京天气怎么样？"
  → 查天气 → 回答
  （一步搞定）

复杂任务（需要规划）：
  "帮我做一份北京3日游攻略，预算3000元，要包含景点、美食和住宿"
  → 需要先想清楚：
    1. 了解北京有哪些景点
    2. 查询景点门票价格
    3. 搜索住宿信息
    4. 查找美食推荐
    5. 规划每日行程
    6. 计算总费用
    7. 整理成攻略文档
  （不规划的话，Agent 可能东查一下西查一下，效率很低）
```

### 不规划的 Agent 会出什么问题？

1. **遗漏步骤**：复杂任务有多个子任务，不规划容易漏掉
2. **顺序混乱**：应该先查信息再写报告，不规划可能反过来
3. **重复劳动**：同一个信息查了好几次
4. **无法自我纠错**：做错了也不知道，继续错下去

本章我们学习两种高级规划策略：
- **Plan-and-Solve**：先制定计划，再按计划执行
- **Reflection**：做完之后自我检查，发现问题就纠正

---

## 7.2 Plan-and-Solve 模式

### 核心思想

Plan-and-Solve 的思路非常直观，就像我们做项目一样：

```
普通人做项目：
  想到什么做什么 → 做到一半发现方向错了 → 推倒重来

有经验的人做项目：
  先写计划 → 按计划执行 → 遇到问题调整计划 → 最终完成

Plan-and-Solve Agent：
  第1步：LLM 先制定一个执行计划
  第2步：按计划逐步执行
  第3步：每完成一步，检查是否需要调整计划
```

### 流程图

```
┌──────────────────────────────────────────────────┐
│              Plan-and-Solve 流程                  │
│                                                    │
│  ┌─────────┐                                      │
│  │ 用户任务  │                                      │
│  └────┬────┘                                      │
│       ▼                                           │
│  ┌─────────────┐                                  │
│  │ 制定计划     │ ← LLM 生成步骤列表               │
│  │ Step 1,2,3..│                                  │
│  └────┬────────┘                                  │
│       ▼                                           │
│  ┌─────────────┐     ┌──────────┐                 │
│  │ 执行当前步骤  │────→│ 调用工具  │                 │
│  └────┬────────┘     └──────────┘                 │
│       ▼                                           │
│  ┌─────────────┐                                  │
│  │ 还有下一步？  │                                  │
│  └──┬───────┬──┘                                  │
│    │是     │否                                    │
│    ▼       ▼                                      │
│  下一步   ┌──────────┐                            │
│          │ 汇总回答   │                            │
│          └──────────┘                            │
│                                                    │
└──────────────────────────────────────────────────┘
```

### 完整代码示例

```python
# plan_and_solve_agent.py
"""
Plan-and-Solve Agent
先制定计划，再按计划执行
作者：青松与桑叶
"""
import os
import json
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("API_KEY"),
    base_url=os.getenv("BASE_URL"),
)

# ==================== 工具定义 ====================
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取指定城市的天气信息",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "城市名称"},
                },
                "required": ["city"],
            },
        },
    },
    {
        "type": "function",
        "function": {
            "name": "search",
            "description": "搜索互联网信息，适用于查找实时数据、最新资讯",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string", "description": "搜索关键词"},
                },
                "required": ["query"],
            },
        },
    },
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "计算数学表达式",
            "parameters": {
                "type": "object",
                "properties": {
                    "expression": {"type": "string", "description": "数学表达式"},
                },
                "required": ["expression"],
            },
        },
    },
]


def get_weather(city: str) -> str:
    data = {
        "北京": {"temperature": 35, "weather": "晴天", "humidity": 40},
        "上海": {"temperature": 28, "weather": "多云", "humidity": 65},
        "广州": {"temperature": 32, "weather": "阵雨", "humidity": 80},
    }
    result = data.get(city, {"temperature": 25, "weather": "未知", "humidity": 50})
    return json.dumps(result, ensure_ascii=False)


def search(query: str) -> str:
    # 模拟搜索结果
    mock_results = {
        "北京景点": "北京热门景点：故宫（门票60元）、长城（门票40元）、颐和园（门票30元）、天坛（门票15元）",
        "北京美食": "北京特色美食：烤鸭（人均150元）、炸酱面（人均30元）、豆汁焦圈（人均20元）",
        "北京住宿": "北京住宿推荐：经济型酒店200-300元/晚，快捷酒店150-200元/晚",
    }
    for key, value in mock_results.items():
        if key in query:
            return value
    return f"关于 '{query}' 的搜索结果：暂无详细信息。"


def calculate(expression: str) -> str:
    try:
        allowed = set("0123456789+-*/().% ")
        if not all(c in allowed for c in expression):
            return "错误：不允许的字符"
        return str(eval(expression))
    except Exception as e:
        return f"错误：{e}"


TOOL_MAP = {
    "get_weather": get_weather,
    "search": search,
    "calculate": calculate,
}

# ==================== 计划制定 ====================

def make_plan(user_task: str) -> list[str]:
    """
    让 LLM 为用户任务制定执行计划

    返回：步骤列表，例如 ["步骤1：...", "步骤2：...", ...]
    """
    response = client.chat.completions.create(
        model=os.getenv("MODEL"),
        messages=[
            {
                "role": "system",
                "content": (
                    "你是一个任务规划专家。用户会给你一个任务，"
                    "请把它分解成多个具体的执行步骤。\n"
                    "要求：\n"
                    "1. 每个步骤应该具体、可执行\n"
                    "2. 步骤之间有逻辑顺序\n"
                    "3. 标注哪些步骤需要使用工具（搜索、计算等）\n"
                    "4. 用 JSON 数组格式返回，每个元素是一个步骤描述\n"
                    "5. 不要超过 6 个步骤\n\n"
                    "只返回 JSON 数组，不要其他文字。"
                ),
            },
            {"role": "user", "content": user_task},
        ],
        temperature=0.3,
    )

    content = response.choices[0].message.content.strip()

    # 尝试解析 JSON
    try:
        # 去掉可能的 markdown 代码块标记
        if content.startswith("```"):
            content = content.split("\n", 1)[1]
            content = content.rsplit("```", 1)[0]
        plan = json.loads(content)
        if isinstance(plan, list):
            return plan
    except json.JSONDecodeError:
        pass

    # 如果 JSON 解析失败，按行分割
    lines = [l.strip().lstrip("0123456789.-) ") for l in content.split("\n") if l.strip()]
    return lines if lines else [content]


# ==================== 步骤执行 ====================

def execute_step(step: str) -> str:
    """
    执行单个步骤（使用 ReAct 模式）
    """
    messages = [
        {
            "role": "system",
            "content": (
                "你是一个任务执行助手。请完成以下步骤。\n"
                "如果需要信息，请使用搜索或计算工具。\n"
                "完成后给出简洁的结果摘要。"
            ),
        },
        {"role": "user", "content": f"请完成这个步骤：{step}"},
    ]

    for _ in range(3):  # 每个步骤最多3轮工具调用
        response = client.chat.completions.create(
            model=os.getenv("MODEL"),
            messages=messages,
            tools=tools,
            tool_choice="auto",
        )
        msg = response.choices[0].message

        if not msg.tool_calls:
            return msg.content

        messages.append(msg)
        for tc in msg.tool_calls:
            name = tc.function.name
            args = json.loads(tc.function.arguments)
            print(f"    [工具] {name}({json.dumps(args, ensure_ascii=False)})")

            result = TOOL_MAP[name](**args)
            print(f"    [结果] {result}")

            messages.append({"role": "tool", "tool_call_id": tc.id, "content": result})

    return "步骤执行超时。"


# ==================== 主流程 ====================

def run_plan_and_solve(user_task: str):
    """
    Plan-and-Solve 主流程
    """
    print(f"{'='*60}")
    print(f"用户任务：{user_task}")
    print(f"{'='*60}\n")

    # 第1步：制定计划
    print("【阶段1：制定计划】")
    plan = make_plan(user_task)
    print(f"执行计划（共 {len(plan)} 步）：")
    for i, step in enumerate(plan, 1):
        print(f"  步骤{i}：{step}")
    print()

    # 第2步：逐步执行
    print("【阶段2：执行计划】")
    results = []
    for i, step in enumerate(plan, 1):
        print(f"\n--- 执行步骤 {i}/{len(plan)}：{step} ---")
        result = execute_step(step)
        print(f"  结果：{result}")
        results.append(f"步骤{i}（{step}）：{result}")

    # 第3步：汇总
    print(f"\n【阶段3：汇总结果】")
    all_info = "\n".join(results)
    summary_response = client.chat.completions.create(
        model=os.getenv("MODEL"),
        messages=[
            {
                "role": "system",
                "content": (
                    "你是一个任务汇总助手。用户有一个任务，Agent 已经按计划执行完毕。"
                    "请根据各步骤的执行结果，生成一份完整的最终回答。"
                    "回答应该条理清晰、信息完整。"
                ),
            },
            {"role": "user", "content": f"原始任务：{user_task}\n\n执行结果：\n{all_info}"},
        ],
    )
    final_answer = summary_response.choices[0].message.content
    print(f"\n最终回答：\n{final_answer}")
    return final_answer


# ==================== 交互入口 ====================
if __name__ == "__main__":
    print("Plan-and-Solve Agent 已启动！输入 'quit' 退出\n")

    while True:
        user_input = input("你：").strip()
        if user_input.lower() in ("quit", "exit", "q"):
            print("再见！")
            break
        if not user_input:
            continue
        run_plan_and_solve(user_input)
        print("\n" + "="*60 + "\n")
```

### 运行演示

```bash
python plan_and_solve_agent.py
```

```
你：帮我规划一个北京3日游，预算3000元

============================================================
用户任务：帮我规划一个北京3日游，预算3000元
============================================================

【阶段1：制定计划】
执行计划（共 4 步）：
  步骤1：搜索北京热门景点及门票价格
  步骤2：搜索北京特色美食及人均消费
  步骤3：搜索北京住宿推荐及价格
  步骤4：根据以上信息规划3日行程并计算总费用

【阶段2：执行计划】

--- 执行步骤 1/4：搜索北京热门景点及门票价格 ---
    [工具] search({"query": "北京景点"})
    [结果] 北京热门景点：故宫（门票60元）、长城（门票40元）、颐和园（门票30元）、天坛（门票15元）
  结果：北京热门景点包括：故宫（60元）、长城（40元）、颐和园（30元）、天坛（15元）。

--- 执行步骤 2/4：搜索北京特色美食及人均消费 ---
    [工具] search({"query": "北京美食"})
    [结果] 北京特色美食：烤鸭（人均150元）、炸酱面（人均30元）、豆汁焦圈（人均20元）
  结果：北京特色美食：烤鸭人均150元、炸酱面人均30元、豆汁焦圈人均20元。

--- 执行步骤 3/4：搜索北京住宿推荐及价格 ---
    [工具] search({"query": "北京住宿"})
    [结果] 北京住宿推荐：经济型酒店200-300元/晚，快捷酒店150-200元/晚
  结果：北京住宿：经济型酒店200-300元/晚，快捷酒店150-200元/晚。

--- 执行步骤 4/4：根据以上信息规划3日行程并计算总费用 ---
    [工具] calculate({"expression": "200*2 + 60 + 40 + 30 + 15 + 150 + 30 + 20"})
    [结果] 545
  结果：住宿2晚400元 + 景点门票145元 + 美食200元 = 545元基础费用。

【阶段3：汇总结果】

最终回答：
## 北京3日游攻略（预算3000元）

### 住宿（2晚）
推荐快捷酒店，约 150-200元/晚，2晚共计 300-400元。

### 行程安排
**Day 1**：故宫（60元）→ 品尝烤鸭（人均150元）
**Day 2**：长城一日游（门票40元 + 交通约100元）
**Day 3**：颐和园（30元）+ 天坛（15元）→ 品尝炸酱面（人均30元）

### 费用估算
- 住宿：400元
- 门票：145元
- 餐饮：约300元（3天）
- 交通：约200元
- **合计：约1045元**

预算3000元非常充裕，还可以安排更多美食体验和购物！
```

---

## 7.3 反思机制（Reflection）

### 什么是反思？

反思（Reflection）就是让 Agent **做完之后自己检查一遍**，看看有没有问题。

```
没有反思的 Agent：
  用户：帮我算一下 (100 + 200) * 3
  Agent：结果是 900
  （如果算错了呢？用户可能不知道）

有反思的 Agent：
  用户：帮我算一下 (100 + 200) * 3
  Agent：[第1次] 结果是 900
  Agent：[反思] 让我验证一下：(100+200)=300, 300*3=900。计算正确。
  Agent：最终回答：结果是 900。
```

反思机制的价值：
1. **自我纠错**：发现错误并修正
2. **提升质量**：检查回答是否完整、准确
3. **增加可靠性**：减少"一本正经胡说八道"的情况

### 实现思路

```
正常执行 → 得到结果 → 让 LLM 反思 → 如果有问题就修正 → 输出最终结果

反思时问 LLM 的问题：
1. 你的回答是否准确？
2. 是否有遗漏的信息？
3. 是否有逻辑错误？
4. 如果有错误，请修正并给出新的回答。
```

### 完整代码示例

```python
# reflection_agent.py
"""
带反思机制的 Agent
执行完任务后自我检查和纠错
作者：青松与桑叶
"""
import os
import json
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key=os.getenv("API_KEY"),
    base_url=os.getenv("BASE_URL"),
)

# ==================== 工具定义 ====================
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取指定城市的天气信息",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "城市名称"},
                },
                "required": ["city"],
            },
        },
    },
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "计算数学表达式",
            "parameters": {
                "type": "object",
                "properties": {
                    "expression": {"type": "string", "description": "数学表达式"},
                },
                "required": ["expression"],
            },
        },
    },
    {
        "type": "function",
        "function": {
            "name": "search",
            "description": "搜索互联网信息",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string", "description": "搜索关键词"},
                },
                "required": ["query"],
            },
        },
    },
]


def get_weather(city: str) -> str:
    data = {
        "北京": {"temperature": 35, "weather": "晴天", "humidity": 40},
        "上海": {"temperature": 28, "weather": "多云", "humidity": 65},
        "广州": {"temperature": 32, "weather": "阵雨", "humidity": 80},
    }
    result = data.get(city, {"temperature": 25, "weather": "未知", "humidity": 50})
    return json.dumps(result, ensure_ascii=False)


def calculate(expression: str) -> str:
    try:
        allowed = set("0123456789+-*/().% ")
        if not all(c in allowed for c in expression):
            return json.dumps({"error": "不允许的字符"})
        return json.dumps({"result": eval(expression)})
    except Exception as e:
        return json.dumps({"error": str(e)})


def search(query: str) -> str:
    mock = {
        "Python": "Python 是一种高级编程语言，广泛用于 Web 开发、数据分析和 AI。",
        "JavaScript": "JavaScript 是一种脚本语言，主要用于 Web 前端开发。",
    }
    for key, value in mock.items():
        if key.lower() in query.lower():
            return value
    return f"关于 '{query}' 的搜索结果：暂无详细信息。"


TOOL_MAP = {
    "get_weather": get_weather,
    "calculate": calculate,
    "search": search,
}


# ==================== ReAct 执行 ====================

def react_execute(user_message: str, max_rounds: int = 5) -> str:
    """使用 ReAct 模式执行任务"""
    messages = [
        {
            "role": "system",
            "content": (
                "你是一个智能助手，可以查询天气、进行计算和搜索信息。\n"
                "请根据用户的问题，选择合适的工具来完成任务。"
            ),
        },
        {"role": "user", "content": user_message},
    ]

    for _ in range(max_rounds):
        response = client.chat.completions.create(
            model=os.getenv("MODEL"),
            messages=messages,
            tools=tools,
            tool_choice="auto",
        )
        msg = response.choices[0].message

        if not msg.tool_calls:
            return msg.content

        messages.append(msg)
        for tc in msg.tool_calls:
            name = tc.function.name
            args = json.loads(tc.function.arguments)
            print(f"  [工具] {name}({json.dumps(args, ensure_ascii=False)})")
            result = TOOL_MAP[name](**args)
            print(f"  [结果] {result}")
            messages.append({"role": "tool", "tool_call_id": tc.id, "content": result})

    return "执行超时。"


# ==================== 反思机制 ====================

def reflect(user_message: str, initial_answer: str) -> dict:
    """
    让 LLM 反思自己的回答

    返回：
        {
            "is_correct": True/False,
            "issues": "发现的问题",
            "improved_answer": "改进后的回答"
        }
    """
    response = client.chat.completions.create(
        model=os.getenv("MODEL"),
        messages=[
            {
                "role": "system",
                "content": (
                    "你是一个质量检查专家。请检查以下 Agent 的回答是否正确、完整。\n"
                    "检查要点：\n"
                    "1. 回答是否准确（数据、计算结果是否正确）\n"
                    "2. 回答是否完整（有没有遗漏用户的问题）\n"
                    "3. 逻辑是否合理\n"
                    "4. 是否存在事实错误\n\n"
                    "请用 JSON 格式返回：\n"
                    '{"is_correct": true/false, "issues": "问题描述（如果正确则为空）", '
                    '"improved_answer": "改进后的回答（如果正确则为原回答）"}\n'
                    "只返回 JSON，不要其他文字。"
                ),
            },
            {
                "role": "user",
                "content": (
                    f"用户问题：{user_message}\n\n"
                    f"Agent 的回答：{initial_answer}"
                ),
            },
        ],
        temperature=0.1,
    )

    content = response.choices[0].message.content.strip()
    try:
        if content.startswith("```"):
            content = content.split("\n", 1)[1].rsplit("```", 1)[0]
        return json.loads(content)
    except json.JSONDecodeError:
        return {"is_correct": True, "issues": "", "improved_answer": initial_answer}


# ==================== 主流程 ====================

def run_with_reflection(user_message: str, max_reflections: int = 2):
    """
    带反思的 Agent 主流程

    参数：
        user_message: 用户的问题
        max_reflections: 最大反思次数（防止无限反思）
    """
    print(f"{'='*60}")
    print(f"用户：{user_message}")
    print(f"{'='*60}\n")

    # 第1步：正常执行
    print("【执行阶段】")
    answer = react_execute(user_message)
    print(f"\n初始回答：{answer}\n")

    # 第2步：反思
    for i in range(max_reflections):
        print(f"【反思阶段（第 {i+1} 次）】")
        reflection = reflect(user_message, answer)

        is_correct = reflection.get("is_correct", True)
        issues = reflection.get("issues", "")
        improved = reflection.get("improved_answer", answer)

        if is_correct:
            print(f"  检查结果：回答正确，无需修改。")
            break
        else:
            print(f"  发现问题：{issues}")
            print(f"  改进回答：{improved}")
            answer = improved
            print()

    print(f"\n{'='*60}")
    print(f"最终回答：{answer}")
    print(f"{'='*60}")
    return answer


# ==================== 交互入口 ====================
if __name__ == "__main__":
    print("Reflection Agent 已启动！输入 'quit' 退出\n")

    while True:
        user_input = input("你：").strip()
        if user_input.lower() in ("quit", "exit", "q"):
            print("再见！")
            break
        if not user_input:
            continue
        run_with_reflection(user_input)
        print()
```

### 运行演示

```bash
python reflection_agent.py
```

```
你：北京和上海的温差是多少？

============================================================
用户：北京和上海的温差是多少？
============================================================

【执行阶段】
  [工具] get_weather({"city": "北京"})
  [结果] {"temperature": 35, "weather": "晴天", "humidity": 40}
  [工具] get_weather({"city": "上海"})
  [结果] {"temperature": 28, "weather": "多云", "humidity": 65}

初始回答：北京温度35度，上海温度28度，温差是7度。

【反思阶段（第 1 次）】
  检查结果：回答正确，无需修改。

============================================================
最终回答：北京温度35度，上海温度28度，温差是7度。
============================================================
```

```
你：帮我算一下，如果北京温度是35度，上海温度是28度，温差是多少？然后如果温差超过5度，再算一下35*28

============================================================
用户：帮我算一下...
============================================================

【执行阶段】
  [工具] calculate({"expression": "35 - 28"})
  [结果] {"result": 7}
  [工具] calculate({"expression": "35*28"})
  [结果] {"result": 980}

初始回答：北京和上海的温差是7度。由于温差超过5度，35*28=980。

【反思阶段（第 1 次）】
  检查结果：回答正确，无需修改。

============================================================
最终回答：北京和上海的温差是7度。由于温差超过5度，35*28=980。
============================================================
```

---

## 7.4 动手练习

### 练习1：Plan-and-Solve + 反思结合（难度：中等）

把 7.2 节的 Plan-and-Solve 和 7.3 节的反思机制结合起来：先制定计划、按计划执行，执行完毕后对最终结果进行反思和修正。

提示：
```python
def run_plan_solve_reflect(user_task: str):
    # 1. 制定计划
    plan = make_plan(user_task)
    # 2. 逐步执行
    results = [execute_step(step) for step in plan]
    # 3. 汇总结果
    summary = summarize(user_task, results)
    # 4. 反思
    reflection = reflect(user_task, summary)
    if not reflection["is_correct"]:
        summary = reflection["improved_answer"]
    return summary
```

### 练习2：动态调整计划（难度：中等）

在执行计划的过程中，如果某一步的结果提示需要额外的步骤，让 Agent 能够动态调整计划。

提示：在执行每一步之后，让 LLM 判断是否需要添加新的步骤。

### 练习3：多轮反思（难度：困难）

实现多轮反思机制：如果第一次反思发现了问题并修正，再进行第二次反思，确认修正后的回答是否正确。最多反思3轮。

---

## 7.5 小结

本章我们学习了两种高级规划策略：

| 策略 | 核心思想 | 适用场景 |
|------|---------|---------|
| **Plan-and-Solve** | 先制定计划，再按计划执行 | 多步骤的复杂任务 |
| **Reflection** | 做完后自我检查和纠错 | 需要高准确性的任务 |

核心要点：
1. **Plan-and-Solve** 让 Agent 从"想到什么做什么"变成"有计划地做事"
2. **Reflection** 让 Agent 从"做完就交"变成"做完再检查一遍"
3. 两种策略可以组合使用，效果更好
4. 反思次数不宜过多，1-2次即可，否则浪费 token

### Agent 规划能力对比

```
Level 1：无规划（第4章的 ReAct Agent）
  能力：简单任务
  问题：复杂任务容易遗漏步骤

Level 2：Plan-and-Solve（本章 7.2 节）
  能力：中等复杂任务
  优势：有计划、有条理

Level 3：Plan-and-Solve + Reflection（本章 7.3 节组合）
  能力：复杂任务
  优势：有计划 + 自我纠错
```

---

## 下一章预告

恭喜你完成了"构建篇"的全部内容！你已经学会了：
- 从零手写 ReAct Agent（第4章）
- 给 Agent 加上记忆系统（第5章）
- 让 Agent 使用各种工具（第6章）
- 让 Agent 学会规划和反思（第7章）

接下来，我们将进入 **第三部分：框架篇**。

在 **第8章：LangChain 快速上手** 中，我们将：
- 学习 LangChain 框架的基本用法
- 用 LangChain 快速构建 Agent
- 对比"手写 Agent"和"框架 Agent"的优劣
- 学习如何在框架中自定义工具和记忆

有了前面的基础，学框架会非常轻松 —— 因为你已经理解了底层原理。

---

> 上一篇：[第6章：让 Agent 使用工具](../06-agent-tools/README.md) | 下一篇：[第8章：LangChain 快速上手](../08-langchain/README.md)
