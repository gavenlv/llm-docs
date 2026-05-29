# 大语言模型深度教程 - 第八章：Agent系统深度解析

> **学习目标**：深入理解Agent系统的核心架构和设计模式，掌握ReAct、Plan-and-Execute、Multi-Agent等主流Agent框架的实现，能够构建复杂、自主、协作的智能Agent系统

---

## 📚 章节导航

- [8.1 Agent系统核心概念](#81-agent系统核心概念)
- [8.2 ReAct Agent详解](#82-react-agent详解)
- [8.3 Plan-and-Execute Agent](#83-plan-and-execute-agent)
- [8.4 Multi-Agent协作系统](#84-multi-agent协作系统)
- [8.5 Self-Reflection Agent](#85-self-reflection-agent)
- [8.6 Agent记忆系统](#86-agent记忆系统)
- [8.7 Agent工具集成](#87-agent工具集成)
- [8.8 Agent评估与优化](#88-agent评估与优化)
- [8.9 实战项目：智能研究助手](#89-实战项目智能研究助手)
- [8.10 总结与练习](#810-总结与练习)

---

## 8.1 Agent系统核心概念

### 🎯 Agent的定义与特征

```
┌─────────────────────────────────────────────────────────┐
│         Agent：从被动响应到主动行动                      │
└─────────────────────────────────────────────────────────┘

核心定义：
┌─────────────────────────────────┐
│  Agent = LLM + 规划 + 工具 + 记忆│
│                                 │
│  Agent是一个能够：              │
│  - 感知环境                     │
│  - 制定计划                     │
│  - 执行行动                     │
│  - 反思改进                     │
│  - 达成目标                     │
│                                 │
│  的自主智能系统                 │
└─────────────────────────────────┘

Agent的四大特征：
┌─────────────────────────────────┐
│  1. 自主性（Autonomy）          │
│  - 能够自主决策                 │
│  - 不需要人类持续干预           │
│  - 能够独立完成任务             │
│                                 │
│  2. 反应性（Reactivity）        │
│  - 能够感知环境变化             │
│  - 能够及时响应                 │
│  - 能够调整策略                 │
│                                 │
│  3. 主动性（Proactivity）       │
│  - 能够主动采取行动             │
│  - 能够预判和规划               │
│  - 能够目标导向                 │
│                                 │
│  4. 社交性（Social Ability）    │
│  - 能够与其他Agent协作          │
│  - 能够与人类交互               │
│  - 能够理解和沟通               │
└─────────────────────────────────┘

对比：LLM vs Agent
┌─────────────────────────────────┐
│  LLM（被动）：                  │
│  用户："帮我写一篇文章"         │
│  LLM：直接生成文章              │
│                                 │
│  Agent（主动）：                │
│  用户："帮我写一篇文章"         │
│  Agent：                        │
│  1. 分析任务需求                │
│  2. 搜索相关资料                │
│  3. 制定文章结构                │
│  4. 撰写初稿                    │
│  5. 自我审查                    │
│  6. 修改完善                    │
│  7. 最终输出                    │
│                                 │
│  关键差异：                     │
│  - LLM：一次性生成              │
│  - Agent：多步骤、多工具、多轮次│
└─────────────────────────────────┘
```

### 📊 Agent系统架构

```
┌─────────────────────────────────────────────────────────┐
│         Agent系统的完整架构                              │
└─────────────────────────────────────────────────────────┘

架构层次：
┌─────────────────────────────────┐
│  Layer 1: 核心引擎              │
│  - LLM作为推理核心              │
│  - 提供理解和生成能力           │
│                                 │
│  Layer 2: 认知模块              │
│  - 规划模块：任务分解、策略制定 │
│  - 执行模块：工具调用、行动执行 │
│  - 反思模块：结果评估、策略调整 │
│                                 │
│  Layer 3: 记忆系统              │
│  - 短期记忆：当前任务上下文     │
│  - 长期记忆：历史经验、知识     │
│  - 工作记忆：中间状态、结果     │
│                                 │
│  Layer 4: 工具生态              │
│  - 搜索工具：信息检索           │
│  - 计算工具：数值计算           │
│  - 代码工具：代码执行           │
│  - API工具：外部服务            │
│                                 │
│  Layer 5: 交互接口              │
│  - 用户接口：人类交互           │
│  - Agent接口：Agent协作         │
│  - 系统接口：环境感知           │
└─────────────────────────────────┘

Agent工作流程：
┌─────────────────────────────────┐
│  ┌───────────────────────┐     │
│  │  用户任务              │     │
│  └───────────────────────┘     │
│           ↓                     │
│  ┌───────────────────────┐     │
│  │  任务理解              │     │
│  │  - 分析目标            │     │
│  │  - 提取关键信息        │     │
│  │  - 确定约束条件        │     │
│  └───────────────────────┘     │
│           ↓                     │
│  ┌───────────────────────┐     │
│  │  规划制定              │     │
│  │  - 分解子任务          │     │
│  │  - 制定执行计划        │     │
│  │  - 选择工具策略        │     │
│  └───────────────────────┘     │
│           ↓                     │
│  ┌───────────────────────┐     │
│  │  执行循环              │     │
│  │  ┌─────┬─────┬─────┐  │     │
│  │  │思考 │行动 │观察 │  │     │
│  │  │ ↓   │ ↓   │ ↓   │  │     │
│  │  │推理 │执行 │反馈 │  │     │
│  │  └─────┴─────┴─────┘  │     │
│  │  重复直到完成          │     │
│  └───────────────────────┘     │
│           ↓                     │
│  ┌───────────────────────┐     │
│  │  结果反思              │     │
│  │  - 评估完成度          │     │
│  │  - 检查质量            │     │
│  │  - 改进优化            │     │
│  └───────────────────────┘     │
│           ↓                     │
│  ┌───────────────────────┐     │
│  │  最终输出              │     │
│  └───────────────────────┘     │
└─────────────────────────────────┘
```

### 💻 Agent基础实现

```python
from typing import Dict, List, Any, Optional
import json
from abc import ABC, abstractmethod

class AgentState:
    def __init__(self):
        self.current_task = None
        self.plan = None
        self.current_step = 0
        self.history = []
        self.memory = {}
        self.context = {}
    
    def update(self, key: str, value: Any):
        self.context[key] = value
    
    def add_history(self, thought: str, action: str, observation: str):
        self.history.append({
            'thought': thought,
            'action': action,
            'observation': observation,
            'step': self.current_step
        })
    
    def get_context(self) -> Dict:
        return self.context
    
    def get_history(self) -> List[Dict]:
        return self.history

class Tool(ABC):
    @abstractmethod
    def name(self) -> str:
        pass
    
    @abstractmethod
    def description(self) -> str:
        pass
    
    @abstractmethod
    def parameters(self) -> Dict:
        pass
    
    @abstractmethod
    def execute(self, **kwargs) -> Any:
        pass

class BaseAgent:
    def __init__(self, llm_client, tools: List[Tool]):
        self.llm_client = llm_client
        self.tools = {tool.name(): tool for tool in tools}
        self.state = AgentState()
    
    def understand_task(self, task: str) -> Dict:
        prompt = f"""分析以下任务，提取关键信息：

任务：{task}

请以JSON格式输出：
{
    "goal": "任务目标",
    "constraints": ["约束条件"],
    "required_info": ["需要的信息"],
    "estimated_steps": "预计步骤数",
    "complexity": "简单/中等/复杂"
}

分析结果："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3
        )
        
        try:
            analysis = json.loads(response.choices[0].message.content)
        except:
            analysis = {
                'goal': task,
                'constraints': [],
                'required_info': [],
                'estimated_steps': 5,
                'complexity': 'medium'
            }
        
        self.state.current_task = task
        self.state.update('task_analysis', analysis)
        
        return analysis
    
    def get_available_tools(self) -> str:
        tools_info = []
        for name, tool in self.tools.items():
            tools_info.append(f"- {name}: {tool.description()}")
            params = tool.parameters()
            if params:
                tools_info.append(f"  参数: {json.dumps(params, ensure_ascii=False)}")
        
        return "\n".join(tools_info)
    
    def call_tool(self, tool_name: str, parameters: Dict) -> Any:
        if tool_name in self.tools:
            return self.tools[tool_name].execute(**parameters)
        return None
    
    def run(self, task: str, max_iterations: int = 10) -> Dict:
        analysis = self.understand_task(task)
        
        self.state.plan = self.create_plan(task, analysis)
        
        iteration = 0
        while iteration < max_iterations:
            thought = self.think()
            
            if self.should_stop(thought):
                break
            
            action = self.decide_action(thought)
            
            observation = self.execute_action(action)
            
            self.state.add_history(thought, json.dumps(action), observation)
            
            self.state.current_step += 1
            iteration += 1
        
        final_result = self.synthesize_result()
        
        reflection = self.reflect()
        
        return {
            'task': task,
            'analysis': analysis,
            'plan': self.state.plan,
            'history': self.state.get_history(),
            'result': final_result,
            'reflection': reflection
        }
    
    @abstractmethod
    def create_plan(self, task: str, analysis: Dict) -> List[Dict]:
        pass
    
    @abstractmethod
    def think(self) -> str:
        pass
    
    @abstractmethod
    def decide_action(self, thought: str) -> Dict:
        pass
    
    @abstractmethod
    def execute_action(self, action: Dict) -> str:
        pass
    
    @abstractmethod
    def should_stop(self, thought: str) -> bool:
        pass
    
    @abstractmethod
    def synthesize_result(self) -> str:
        pass
    
    @abstractmethod
    def reflect(self) -> str:
        pass

def test_base_agent():
    print("Agent基础架构测试")
    print("="*50)
    
    state = AgentState()
    state.update('test_key', 'test_value')
    state.add_history('思考1', '行动1', '观察1')
    
    print("状态上下文:", state.get_context())
    print("历史记录:", state.get_history())
    
    print("\nAgent核心组件：")
    print("1. LLM引擎：提供推理和生成能力")
    print("2. 规划模块：分解任务、制定策略")
    print("3. 执行模块：调用工具、执行行动")
    print("4. 反思模块：评估结果、改进优化")
    print("5. 记忆系统：存储历史、积累经验")
    print("6. 工具生态：扩展能力、连接外部")

if __name__ == "__main__":
    test_base_agent()
```

---

## 8.2 ReAct Agent详解

### 🎯 ReAct框架原理

```
┌─────────────────────────────────────────────────────────┐
│         ReAct：推理与行动的交替循环                      │
└─────────────────────────────────────────────────────────┘

ReAct核心思想：
┌─────────────────────────────────┐
│  ReAct = Reasoning + Acting     │
│                                 │
│  通过交替进行推理和行动，        │
│  让Agent能够：                  │
│  - 明确思考过程                 │
│  - 采取具体行动                 │
│  - 观察行动结果                 │
│  - 调整下一步策略               │
│                                 │
│  循环结构：                     │
│  Thought → Action → Observation │
│     ↓                            │
│  Thought → Action → Observation │
│     ↓                            │
│  ...                            │
│     ↓                            │
│  Final Answer                   │
└─────────────────────────────────┘

ReAct vs 传统方法：
┌─────────────────────────────────┐
│  传统方法（直接生成）：          │
│  问题："2024年诺贝尔物理学奖得主是谁？"│
│  直接回答："John Hopfield和Geoffrey Hinton"│
│                                 │
│  问题：                         │
│  - 可能记忆错误                 │
│  - 无法验证                     │
│  - 缺乏推理过程                 │
│                                 │
│  ReAct方法（推理+行动）：        │
│  Thought 1: 我需要搜索2024年诺贝尔物理学奖│
│  Action 1: Search["2024 Nobel Prize Physics"]│
│  Observation 1: 找到官方新闻... │
│                                 │
│  Thought 2: 从搜索结果中提取获奖者信息│
│  Action 2: Extract[search_result, "winner"]│
│  Observation 2: John Hopfield和Geoffrey Hinton│
│                                 │
│  Thought 3: 我需要验证这个信息  │
│  Action 3: Verify[winner_names] │
│  Observation 3: 信息正确        │
│                                 │
│  Final Answer: 2024年诺贝尔物理学奖│
│  得主是John Hopfield和Geoffrey Hinton│
│                                 │
│  优势：                         │
│  - 推理过程明确                 │
│  - 可以验证和纠错               │
│  - 可解释性强                   │
│  - 结果更可靠                   │
└─────────────────────────────────┘
```

### 📊 ReAct执行流程

```
┌─────────────────────────────────────────────────────────┐
│         ReAct的详细执行流程                              │
└─────────────────────────────────────────────────────────┘

流程图：
┌─────────────────────────────────┐
│  步骤1：任务输入                │
│  用户："找出AI领域2024年的重大突破"│
│           ↓                     │
│  步骤2：初始思考                │
│  Thought: 我需要搜索2024年AI新闻│
│           ↓                     │
│  步骤3：执行行动                │
│  Action: Search["AI breakthrough 2024"]│
│           ↓                     │
│  步骤4：观察结果                │
│  Observation: 找到10篇相关文章  │
│  - GPT-4V发布                   │
│  - Gemini发布                   │
│  - Claude 3发布                 │
│           ↓                     │
│  步骤5：继续思考                │
│  Thought: 我需要分析这些突破的重要性│
│           ↓                     │
│  步骤6：执行行动                │
│  Action: Analyze[articles, "importance"]│
│           ↓                     │
│  步骤7：观察结果                │
│  Observation:                   │
│  1. GPT-4V：多模态突破          │
│  2. Gemini：最强多模态          │
│  3. Claude 3：推理能力提升      │
│           ↓                     │
│  步骤8：继续思考                │
│  Thought: 我需要整理最终答案    │
│           ↓                     │
│  步骤9：执行行动                │
│  Action: Summarize[breakthroughs]│
│           ↓                     │
│  步骤10：最终答案               │
│  Final Answer:                  │
│  2024年AI重大突破：             │
│  1. GPT-4V：实现真正的多模态理解│
│  2. Gemini：Google最强多模态模型│
│  3. Claude 3：推理能力显著提升  │
└─────────────────────────────────┘

ReAct的关键组件：
┌─────────────────────────────────┐
│  1. Thought（思考）             │
│  - 分析当前状态                 │
│  - 确定下一步目标               │
│  - 选择合适策略                 │
│                                 │
│  2. Action（行动）              │
│  - 选择工具                     │
│  - 准备参数                     │
│  - 执行操作                     │
│                                 │
│  3. Observation（观察）         │
│  - 获取执行结果                 │
│  - 分析结果信息                 │
│  - 更新状态                     │
│                                 │
│  4. 循环控制                    │
│  - 判断是否完成                 │
│  - 决定继续或停止               │
│  - 处理异常情况                 │
└─────────────────────────────────┘
```

### 💻 ReAct Agent完整实现

```python
from typing import Dict, List, Any
import json
import re

class SearchTool(Tool):
    def name(self) -> str:
        return "search"
    
    def description(self) -> str:
        return "搜索网络信息，返回相关结果"
    
    def parameters(self) -> Dict:
        return {
            'query': {
                'type': 'string',
                'description': '搜索关键词'
            }
        }
    
    def execute(self, query: str) -> Any:
        return {
            'results': [
                {'title': f'搜索结果: {query}', 'content': '相关内容1'},
                {'title': f'相关新闻', 'content': '相关内容2'}
            ]
        }

class AnalyzeTool(Tool):
    def name(self) -> str:
        return "analyze"
    
    def description(self) -> str:
        return "分析文本内容，提取关键信息"
    
    def parameters(self) -> Dict:
        return {
            'text': {
                'type': 'string',
                'description': '要分析的文本'
            },
            'focus': {
                'type': 'string',
                'description': '分析重点'
            }
        }
    
    def execute(self, text: str, focus: str) -> Any:
        return {
            'summary': f'分析摘要',
            'key_points': ['要点1', '要点2'],
            'focus_result': f'{focus}相关结果'
        }

class ReActAgent(BaseAgent):
    def __init__(self, llm_client, tools: List[Tool], max_iterations: int = 10):
        super().__init__(llm_client, tools)
        self.max_iterations = max_iterations
    
    def create_plan(self, task: str, analysis: Dict) -> List[Dict]:
        prompt = f"""为以下任务制定执行计划：

任务：{task}
分析：{json.dumps(analysis, ensure_ascii=False)}

可用工具：
{self.get_available_tools()}

请制定详细的执行计划，以JSON格式输出：
{
    "steps": [
        {
            "step_id": 1,
            "thought": "思考内容",
            "action": "行动描述",
            "expected_observation": "预期观察"
        }
    ]
}

计划："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        try:
            plan = json.loads(response.choices[0].message.content)
            return plan['steps']
        except:
            return [{'step_id': 1, 'thought': '开始执行任务', 'action': 'search', 'expected_observation': '获取信息'}]
    
    def think(self) -> str:
        history = self.state.get_history()
        history_text = "\n".join([
            f"Step {h['step']}: Thought: {h['thought']}\nAction: {h['action']}\nObservation: {h['observation']}"
            for h in history[-3:]
        ])
        
        prompt = f"""当前任务：{self.state.current_task}

历史记录：
{history_text if history_text else '无'}

可用工具：
{self.get_available_tools()}

请思考下一步应该做什么：

Thought："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.7
        )
        
        return response.choices[0].message.content
    
    def decide_action(self, thought: str) -> Dict:
        prompt = f"""根据以下思考，决定要执行的行动：

思考：{thought}

可用工具：
{self.get_available_tools()}

请以JSON格式输出行动：
{
    "tool": "工具名称",
    "parameters": {
        "参数名": "参数值"
    },
    "reason": "选择原因"
}

Action："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3
        )
        
        try:
            action = json.loads(response.choices[0].message.content)
            return action
        except:
            return {'tool': 'none', 'parameters': {}, 'reason': '无法解析'}
    
    def execute_action(self, action: Dict) -> str:
        tool_name = action.get('tool')
        parameters = action.get('parameters', {})
        
        if tool_name == 'none':
            return "无行动执行"
        
        if tool_name in self.tools:
            result = self.call_tool(tool_name, parameters)
            return json.dumps(result, ensure_ascii=False)
        
        return f"工具 {tool_name} 不存在"
    
    def should_stop(self, thought: str) -> bool:
        stop_keywords = ['任务完成', 'Final Answer', '已经完成', '达到目标']
        
        for keyword in stop_keywords:
            if keyword in thought:
                return True
        
        if self.state.current_step >= self.max_iterations:
            return True
        
        return False
    
    def synthesize_result(self) -> str:
        history = self.state.get_history()
        
        history_text = "\n".join([
            f"Step {h['step']}: {h['thought']}\n→ {h['action']}\n→ {h['observation']}"
            for h in history
        ])
        
        prompt = f"""任务：{self.state.current_task}

执行历史：
{history_text}

请综合所有信息，给出最终答案：

Final Answer："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        return response.choices[0].message.content
    
    def reflect(self) -> str:
        history = self.state.get_history()
        result = self.state.context.get('final_result', '')
        
        history_text = "\n".join([
            f"Step {h['step']}: {h['thought']}"
            for h in history
        ])
        
        prompt = f"""反思执行过程：

任务：{self.state.current_task}
最终结果：{result}

执行步骤：
{history_text}

请反思：
1. 任务是否完成？
2. 执行过程是否高效？
3. 有哪些可以改进的地方？
4. 学到了什么经验？

反思："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.6
        )
        
        return response.choices[0].message.content

def test_react_agent():
    from openai import OpenAI
    
    client = OpenAI(api_key="your-api-key")
    
    tools = [SearchTool(), AnalyzeTool()]
    
    agent = ReActAgent(client, tools, max_iterations=5)
    
    task = "找出2024年AI领域的重大突破"
    
    print(f"任务：{task}")
    print("="*50)
    
    result = agent.run(task)
    
    print("\n执行历史：")
    for h in result['history']:
        print(f"Step {h['step']}:")
        print(f"  Thought: {h['thought']}")
        print(f"  Action: {h['action']}")
        print(f"  Observation: {h['observation'][:100]}...")
    
    print("\n最终结果：")
    print(result['result'])
    
    print("\n反思：")
    print(result['reflection'])

if __name__ == "__main__":
    test_react_agent()
```

---

## 8.3 Plan-and-Execute Agent

### 🎯 Plan-and-Execute框架

```
┌─────────────────────────────────────────────────────────┐
│         Plan-and-Execute：先规划后执行                    │
└─────────────────────────────────────────────────────────┘

核心思想：
┌─────────────────────────────────┐
│  分离规划和执行                  │
│                                 │
│  优势：                         │
│  1. 更清晰的执行路径            │
│  2. 可以并行执行                │
│  3. 更容易调试                  │
│  4. 更可控的执行过程            │
│                                 │
│  流程：                         │
│  Phase 1: Planning              │
│  - 分析任务                     │
│  - 制定完整计划                 │
│  - 生成步骤列表                 │
│                                 │
│  Phase 2: Execution             │
│  - 按计划执行                   │
│  - 处理执行结果                 │
│  - 调整计划（如果需要）         │
│                                 │
│  Phase 3: Verification          │
│  - 检查完成度                   │
│  - 验证结果质量                 │
│  - 修改完善                     │
└─────────────────────────────────┘

对比：ReAct vs Plan-and-Execute
┌─────────────────────────────────┐
│  ReAct（动态规划）：            │
│  - 每步都重新思考               │
│  - 灵活调整                     │
│  - 适合不确定任务               │
│                                 │
│  示例：                         │
│  Thought 1 → Action 1 → Obs 1  │
│  Thought 2 → Action 2 → Obs 2  │
│  （根据Obs调整下一步）          │
│                                 │
│  Plan-and-Execute（预先规划）： │
│  - 先制定完整计划               │
│  - 按计划执行                   │
│  - 适合明确任务                 │
│                                 │
│  示例：                         │
│  Plan: [Step1, Step2, Step3]   │
│  Execute:                       │
│  - Execute Step1                │
│  - Execute Step2                │
│  - Execute Step3                │
│  （按计划顺序执行）              │
└─────────────────────────────────┘
```

### 💻 Plan-and-Execute Agent实现

```python
from typing import Dict, List, Any
import json

class PlanAndExecuteAgent(BaseAgent):
    def __init__(self, llm_client, tools: List[Tool]):
        super().__init__(llm_client, tools)
        self.execution_results = []
    
    def create_plan(self, task: str, analysis: Dict) -> List[Dict]:
        prompt = f"""为以下任务制定详细的执行计划：

任务：{task}
分析：{json.dumps(analysis, ensure_ascii=False)}

可用工具：
{self.get_available_tools()}

请制定完整的执行计划，包括：
1. 明确的步骤顺序
2. 每步的具体行动
3. 预期的结果
4. 依赖关系

以JSON格式输出：
{
    "plan": [
        {
            "step_id": 1,
            "description": "步骤描述",
            "tool": "工具名称",
            "parameters": {},
            "expected_output": "预期输出",
            "dependencies": []
        }
    ],
    "estimated_time": "预计时间",
    "success_criteria": ["成功标准"]
}

计划："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        try:
            plan_data = json.loads(response.choices[0].message.content)
            return plan_data['plan']
        except:
            return []
    
    def think(self) -> str:
        current_step = self.state.plan[self.state.current_step] if self.state.current_step < len(self.state.plan) else None
        
        if current_step:
            return f"执行计划中的步骤{current_step['step_id']}: {current_step['description']}"
        
        return "计划执行完成"
    
    def decide_action(self, thought: str) -> Dict:
        current_step = self.state.plan[self.state.current_step] if self.state.current_step < len(self.state.plan) else None
        
        if current_step:
            return {
                'tool': current_step.get('tool', 'none'),
                'parameters': current_step.get('parameters', {}),
                'reason': f"执行计划步骤{current_step['step_id']}"
            }
        
        return {'tool': 'none', 'parameters': {}, 'reason': '无步骤'}
    
    def execute_action(self, action: Dict) -> str:
        tool_name = action.get('tool')
        parameters = action.get('parameters', {})
        
        if tool_name == 'none':
            return "无行动"
        
        if tool_name in self.tools:
            result = self.call_tool(tool_name, parameters)
            
            self.execution_results.append({
                'step': self.state.current_step,
                'tool': tool_name,
                'result': result
            })
            
            return json.dumps(result, ensure_ascii=False)
        
        return f"工具不存在"
    
    def should_stop(self, thought: str) -> bool:
        return self.state.current_step >= len(self.state.plan)
    
    def synthesize_result(self) -> str:
        results_text = "\n".join([
            f"步骤{r['step']}: {r['tool']} → {json.dumps(r['result'], ensure_ascii=False)[:100]}"
            for r in self.execution_results
        ])
        
        prompt = f"""任务：{self.state.current_task}

执行计划：{json.dumps(self.state.plan, ensure_ascii=False, indent=2)}

执行结果：
{results_text}

请综合所有结果，给出最终答案：

Final Answer："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        return response.choices[0].message.content
    
    def reflect(self) -> str:
        prompt = f"""反思计划执行：

任务：{self.state.current_task}
计划步骤数：{len(self.state.plan)}
实际执行步骤：{len(self.execution_results)}

请反思：
1. 计划是否合理？
2. 执行是否顺利？
3. 有哪些偏差？
4. 如何改进计划？

反思："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.6
        )
        
        return response.choices[0].message.content
    
    def adjust_plan(self, step_index: int, observation: str) -> bool:
        prompt = f"""当前执行遇到问题，需要调整计划：

原计划步骤：{json.dumps(self.state.plan[step_index], ensure_ascii=False)}
执行观察：{observation}

请判断是否需要调整后续计划：

{
    "need_adjustment": true/false,
    "reason": "原因",
    "new_steps": [
        {
            "step_id": step_index + 1,
            "description": "新步骤",
            "tool": "工具",
            "parameters": {}
        }
    ]
}

调整建议："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        try:
            adjustment = json.loads(response.choices[0].message.content)
            
            if adjustment['need_adjustment']:
                self.state.plan = self.state.plan[:step_index+1] + adjustment['new_steps']
                return True
        except:
            pass
        
        return False

def test_plan_and_execute():
    from openai import OpenAI
    
    client = OpenAI(api_key="your-api-key")
    
    tools = [SearchTool(), AnalyzeTool()]
    
    agent = PlanAndExecuteAgent(client, tools)
    
    task = "写一份关于AI发展趋势的报告"
    
    print(f"任务：{task}")
    print("="*50)
    
    result = agent.run(task)
    
    print("\n执行计划：")
    for step in result['plan']:
        print(f"步骤{step['step_id']}: {step['description']}")
    
    print("\n执行结果：")
    print(result['result'])
    
    print("\n反思：")
    print(result['reflection'])

if __name__ == "__main__":
    test_plan_and_execute()
```

---

## 8.4 Multi-Agent协作系统

### 🎯 Multi-Agent架构

```
┌─────────────────────────────────────────────────────────┐
│         Multi-Agent：分工协作的智能团队                   │
└─────────────────────────────────────────────────────────┘

核心思想：
┌─────────────────────────────────┐
│  多个Agent协作完成复杂任务      │
│                                 │
│  优势：                         │
│  1. 专业分工                     │
│  2. 并行执行                     │
│  3. 相互验证                     │
│  4. 更高质量                     │
│                                 │
│  协作模式：                      │
│  1. 顺序协作：A → B → C         │
│  2. 并行协作：A, B, C同时执行    │
│  3. 层级协作：Manager → Workers │
│  4. 竞争协作：多个方案，选最佳   │
└─────────────────────────────────┘

角色分工示例：
┌─────────────────────────────────┐
│  项目：完成一份市场调研报告      │
│                                 │
│  Agent角色：                    │
│                                 │
│  1. 研究Agent（Researcher）     │
│  职责：                         │
│  - 搜索市场数据                 │
│  - 收集行业报告                 │
│  - 整理关键信息                 │
│                                 │
│  2. 分析Agent（Analyst）        │
│  职责：                         │
│  - 分析市场趋势                 │
│  - 计算市场份额                 │
│  - 预测未来发展                 │
│                                 │
│  3. 写作Agent（Writer）         │
│  职责：                         │
│  - 撰写报告内容                 │
│  - 组织文章结构                 │
│  - 确保逻辑连贯                 │
│                                 │
│  4. 编辑Agent（Editor）         │
│  职责：                         │
│  - 检查内容质量                 │
│  - 修改语言表达                 │
│  - 完善格式排版                 │
│                                 │
│  5. 协调Agent（Coordinator）    │
│  职责：                         │
│  - 分配任务                     │
│  - 协调进度                     │
│  - 整合结果                     │
└─────────────────────────────────┘
```

### 💻 Multi-Agent系统实现

```python
from typing import Dict, List, Any
import json
from concurrent.futures import ThreadPoolExecutor, as_completed

class AgentRole:
    def __init__(self, name: str, responsibilities: List[str], tools: List[Tool]):
        self.name = name
        self.responsibilities = responsibilities
        self.tools = tools

class WorkerAgent(BaseAgent):
    def __init__(self, llm_client, role: AgentRole):
        super().__init__(llm_client, role.tools)
        self.role = role
    
    def create_plan(self, task: str, analysis: Dict) -> List[Dict]:
        prompt = f"""作为{self.role.name}，为以下任务制定计划：

角色职责：{', '.join(self.role.responsibilities)}
任务：{task}

可用工具：
{self.get_available_tools()}

制定执行计划："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        return [{'description': response.choices[0].message.content}]
    
    def think(self) -> str:
        return f"作为{self.role.name}，执行任务"
    
    def decide_action(self, thought: str) -> Dict:
        return {'tool': 'search', 'parameters': {'query': self.state.current_task}}
    
    def execute_action(self, action: Dict) -> str:
        return "执行结果"
    
    def should_stop(self, thought: str) -> bool:
        return True
    
    def synthesize_result(self) -> str:
        return f"{self.role.name}完成任务"
    
    def reflect(self) -> str:
        return f"{self.role.name}反思"

class CoordinatorAgent:
    def __init__(self, llm_client, worker_agents: Dict[str, WorkerAgent]):
        self.llm_client = llm_client
        self.worker_agents = worker_agents
    
    def assign_tasks(self, main_task: str) -> Dict[str, str]:
        prompt = f"""作为协调者，将以下任务分配给各个Agent：

主任务：{main_task}

可用Agent：
{chr(10).join(f'- {name}: {agent.role.responsibilities}' for name, agent in self.worker_agents.items())}

请分配任务，以JSON格式输出：
{
    "assignments": {
        "agent_name": "分配的任务"
    },
    "execution_order": ["agent1", "agent2"],
    "dependencies": {
        "agent2": ["agent1"]
    }
}

任务分配："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        try:
            assignment = json.loads(response.choices[0].message.content)
            return assignment
        except:
            return {'assignments': {}, 'execution_order': [], 'dependencies': {}}
    
    def execute_sequential(self, assignments: Dict) -> Dict[str, Any]:
        results = {}
        
        execution_order = assignments.get('execution_order', list(assignments['assignments'].keys()))
        
        for agent_name in execution_order:
            task = assignments['assignments'].get(agent_name)
            
            if agent_name in self.worker_agents:
                result = self.worker_agents[agent_name].run(task)
                results[agent_name] = result
        
        return results
    
    def execute_parallel(self, assignments: Dict) -> Dict[str, Any]:
        results = {}
        
        with ThreadPoolExecutor(max_workers=len(self.worker_agents)) as executor:
            futures = {}
            
            for agent_name, task in assignments['assignments'].items():
                if agent_name in self.worker_agents:
                    future = executor.submit(
                        self.worker_agents[agent_name].run,
                        task
                    )
                    futures[future] = agent_name
            
            for future in as_completed(futures):
                agent_name = futures[future]
                results[agent_name] = future.result()
        
        return results
    
    def integrate_results(self, results: Dict[str, Any]) -> str:
        results_text = "\n".join([
            f"{name}: {result['result']}"
            for name, result in results.items()
        ])
        
        prompt = f"""整合各个Agent的结果：

Agent结果：
{results_text}

请整合所有结果，生成最终输出：

整合结果："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        return response.choices[0].message.content
    
    def run(self, main_task: str, mode: str = "sequential") -> Dict:
        assignments = self.assign_tasks(main_task)
        
        if mode == "sequential":
            results = self.execute_sequential(assignments)
        else:
            results = self.execute_parallel(assignments)
        
        final_result = self.integrate_results(results)
        
        return {
            'main_task': main_task,
            'assignments': assignments,
            'agent_results': results,
            'final_result': final_result
        }

def test_multi_agent():
    from openai import OpenAI
    
    client = OpenAI(api_key="your-api-key")
    
    researcher_role = AgentRole(
        "Researcher",
        ["搜索信息", "收集数据"],
        [SearchTool()]
    )
    
    analyst_role = AgentRole(
        "Analyst",
        ["分析数据", "提取洞察"],
        [AnalyzeTool()]
    )
    
    writer_role = AgentRole(
        "Writer",
        ["撰写内容", "组织结构"],
        []
    )
    
    worker_agents = {
        'researcher': WorkerAgent(client, researcher_role),
        'analyst': WorkerAgent(client, analyst_role),
        'writer': WorkerAgent(client, writer_role)
    }
    
    coordinator = CoordinatorAgent(client, worker_agents)
    
    task = "完成一份关于AI市场的研究报告"
    
    print(f"主任务：{task}")
    print("="*50)
    
    result = coordinator.run(task, mode="sequential")
    
    print("\n任务分配：")
    print(json.dumps(result['assignments'], ensure_ascii=False, indent=2))
    
    print("\n各Agent结果：")
    for name, agent_result in result['agent_results'].items():
        print(f"{name}: {agent_result['result']}")
    
    print("\n最终整合结果：")
    print(result['final_result'])

if __name__ == "__main__":
    test_multi_agent()
```

---

## 8.5 Self-Reflection Agent

### 🎯 Self-Reflection机制

```
┌─────────────────────────────────────────────────────────┐
│         Self-Reflection：自我反思与改进                  │
└─────────────────────────────────────────────────────────┘

核心思想：
┌─────────────────────────────────┐
│  Agent能够自我评估和改进         │
│                                 │
│  循环：                         │
│  1. 执行任务                     │
│  2. 自我评估                     │
│  3. 发现问题                     │
│  4. 改进优化                     │
│  5. 重新执行                     │
│  直到满意                        │
│                                 │
│  优势：                         │
│  - 提高质量                     │
│  - 减少错误                     │
│  - 持续改进                     │
│  - 自我学习                     │
└─────────────────────────────────┘

Self-Reflection流程：
┌─────────────────────────────────┐
│  任务："优化这段Python代码"      │
│                                 │
│  第1轮：                         │
│  执行：优化代码                  │
│  结果：优化版本1                 │
│  反思：                         │
│  - 性能提升30%                  │
│  - 问题：仍有冗余代码            │
│  - 改进：进一步简化              │
│                                 │
│  第2轮：                         │
│  执行：基于反思改进              │
│  结果：优化版本2                 │
│  反思：                         │
│  - 性能提升50%                  │
│  - 问题：可读性下降              │
│  - 改进：增加注释                │
│                                 │
│  第3轮：                         │
│  执行：基于反思改进              │
│  结果：优化版本3                 │
│  反思：                         │
│  - 性能提升50%                  │
│  - 可读性好                     │
│  - 问题：无明显问题              │
│  - 决定：满意，停止              │
│                                 │
│  最终输出：优化版本3             │
└─────────────────────────────────┘
```

### 💻 Self-Reflection Agent实现

```python
from typing import Dict, List, Any
import json

class SelfReflectionAgent(BaseAgent):
    def __init__(self, llm_client, tools: List[Tool], max_reflections: int = 3):
        super().__init__(llm_client, tools)
        self.max_reflections = max_reflections
        self.reflection_history = []
        self.current_result = None
        self.satisfaction_threshold = 0.8
    
    def create_plan(self, task: str, analysis: Dict) -> List[Dict]:
        return [{'description': '执行并反思'}]
    
    def think(self) -> str:
        if self.current_result is None:
            return "开始执行任务"
        else:
            return "基于反思改进"
    
    def decide_action(self, thought: str) -> Dict:
        return {'tool': 'execute', 'parameters': {'task': self.state.current_task}}
    
    def execute_action(self, action: Dict) -> str:
        prompt = f"""执行任务：

任务：{self.state.current_task}
当前结果：{self.current_result if self.current_result else '无'}
反思历史：{json.dumps(self.reflection_history, ensure_ascii=False) if self.reflection_history else '无'}

请执行或改进："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.7
        )
        
        self.current_result = response.choices[0].message.content
        
        return self.current_result
    
    def should_stop(self, thought: str) -> bool:
        if len(self.reflection_history) >= self.max_reflections:
            return True
        
        if self.reflection_history:
            last_reflection = self.reflection_history[-1]
            if last_reflection.get('satisfaction', 0) >= self.satisfaction_threshold:
                return True
        
        return False
    
    def synthesize_result(self) -> str:
        return self.current_result
    
    def reflect(self) -> str:
        prompt = f"""自我反思：

任务：{self.state.current_task}
当前结果：{self.current_result}

请评估：
1. 结果质量如何？
2. 是否达到目标？
3. 有什么问题？
4. 如何改进？

以JSON格式输出：
{
    "quality_score": 0.8,
    "satisfaction": 0.8,
    "issues": ["问题列表"],
    "improvements": ["改进建议"],
    "should_continue": true/false
}

反思："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.6
        )
        
        try:
            reflection = json.loads(response.choices[0].message.content)
            self.reflection_history.append(reflection)
            return json.dumps(reflection, ensure_ascii=False)
        except:
            return "反思失败"
    
    def run_with_reflection(self, task: str) -> Dict:
        analysis = self.understand_task(task)
        
        self.state.plan = self.create_plan(task, analysis)
        
        reflection_round = 0
        while reflection_round < self.max_reflections:
            thought = self.think()
            action = self.decide_action(thought)
            observation = self.execute_action(action)
            
            self.state.add_history(thought, json.dumps(action), observation)
            
            reflection = self.reflect()
            
            if self.should_stop(thought):
                break
            
            reflection_round += 1
        
        final_result = self.synthesize_result()
        
        return {
            'task': task,
            'analysis': analysis,
            'history': self.state.get_history(),
            'reflection_history': self.reflection_history,
            'final_result': final_result,
            'reflection_rounds': reflection_round + 1
        }

def test_self_reflection():
    from openai import OpenAI
    
    client = OpenAI(api_key="your-api-key")
    
    agent = SelfReflectionAgent(client, [], max_reflections=3)
    
    task = "优化这段代码：def fibonacci(n): if n <= 1: return n else: return fibonacci(n-1) + fibonacci(n-2)"
    
    print(f"任务：{task}")
    print("="*50)
    
    result = agent.run_with_reflection(task)
    
    print("\n反思轮次：", result['reflection_rounds'])
    
    print("\n反思历史：")
    for i, reflection in enumerate(result['reflection_history']):
        print(f"轮次{i+1}:")
        print(f"  质量: {reflection.get('quality_score', 0)}")
        print(f"  满意度: {reflection.get('satisfaction', 0)}")
        print(f"  问题: {reflection.get('issues', [])}")
        print(f"  改进: {reflection.get('improvements', [])}")
    
    print("\n最终结果：")
    print(result['final_result'])

if __name__ == "__main__":
    test_self_reflection()
```

---

## 8.6 Agent记忆系统

### 🎯 记忆系统架构

```
┌─────────────────────────────────────────────────────────┐
│         Agent记忆系统：从短期到长期                        │
└─────────────────────────────────────────────────────────┘

记忆类型：
┌─────────────────────────────────┐
│  1. 短期记忆（Short-term）      │
│  - 当前任务上下文               │
│  - 最近几步的执行               │
│  - 临时状态信息                 │
│  - 容量有限                     │
│                                 │
│  2. 工作记忆（Working）         │
│  - 当前正在处理的信息           │
│  - 中间计算结果                 │
│  - 任务相关数据                 │
│                                 │
│  3. 长期记忆（Long-term）       │
│  - 历史经验                     │
│  - 学到的知识                   │
│  - 成功案例                     │
│  - 失败教训                     │
│                                 │
│  4. 语义记忆（Semantic）        │
│  - 事实知识                     │
│  - 概念理解                     │
│  - 规则原理                     │
│                                 │
│  5. 情景记忆（Episodic）        │
│  - 具体事件                     │
│  - 执行场景                     │
│  - 时间序列                     │
└─────────────────────────────────┘

记忆管理策略：
┌─────────────────────────────────┐
│  1. 记忆编码                    │
│  - 将信息转换为可存储格式       │
│  - 提取关键特征                 │
│  - 压缩冗余信息                 │
│                                 │
│  2. 记忆存储                    │
│  - 选择合适的存储位置           │
│  - 组织记忆结构                 │
│  - 建立索引                     │
│                                 │
│  3. 记忆检索                    │
│  - 根据需求检索                 │
│  - 相似度匹配                   │
│  - 时间范围查询                 │
│                                 │
│  4. 记忆更新                    │
│  - 添加新记忆                   │
│  - 修改旧记忆                   │
│  - 删除过期记忆                 │
│                                 │
│  5. 记忆遗忘                    │
│  - 清理不重要记忆               │
│  - 释放存储空间                 │
│  - 防止记忆过载                 │
└─────────────────────────────────┘
```

### 💻 记忆系统实现

```python
from typing import Dict, List, Any, Optional
import json
from datetime import datetime
from collections import defaultdict

class MemoryItem:
    def __init__(self, content: str, memory_type: str, metadata: Dict = None):
        self.content = content
        self.memory_type = memory_type
        self.timestamp = datetime.now()
        self.metadata = metadata or {}
        self.importance = metadata.get('importance', 0.5)
        self.access_count = 0
    
    def access(self):
        self.access_count += 1
    
    def to_dict(self) -> Dict:
        return {
            'content': self.content,
            'memory_type': self.memory_type,
            'timestamp': self.timestamp.isoformat(),
            'metadata': self.metadata,
            'importance': self.importance,
            'access_count': self.access_count
        }

class ShortTermMemory:
    def __init__(self, max_size: int = 10):
        self.max_size = max_size
        self.memories: List[MemoryItem] = []
    
    def add(self, content: str, metadata: Dict = None):
        memory = MemoryItem(content, 'short_term', metadata)
        
        self.memories.append(memory)
        
        if len(self.memories) > self.max_size:
            self.memories.pop(0)
    
    def get_recent(self, n: int = 5) -> List[Dict]:
        recent = self.memories[-n:]
        for memory in recent:
            memory.access()
        return [m.to_dict() for m in recent]
    
    def clear(self):
        self.memories = []

class LongTermMemory:
    def __init__(self):
        self.memories: Dict[str, List[MemoryItem]] = defaultdict(list)
        self.index = {}
    
    def add(self, category: str, content: str, metadata: Dict = None):
        memory = MemoryItem(content, 'long_term', metadata)
        
        self.memories[category].append(memory)
        
        keywords = metadata.get('keywords', [])
        for keyword in keywords:
            if keyword not in self.index:
                self.index[keyword] = []
            self.index[keyword].append(memory)
    
    def search_by_category(self, category: str) -> List[Dict]:
        memories = self.memories.get(category, [])
        for memory in memories:
            memory.access()
        return [m.to_dict() for m in memories]
    
    def search_by_keywords(self, keywords: List[str]) -> List[Dict]:
        results = []
        for keyword in keywords:
            if keyword in self.index:
                for memory in self.index[keyword]:
                    memory.access()
                    results.append(memory.to_dict())
        return results
    
    def get_most_accessed(self, category: str, n: int = 5) -> List[Dict]:
        memories = self.memories.get(category, [])
        sorted_memories = sorted(memories, key=lambda m: m.access_count, reverse=True)
        return [m.to_dict() for m in sorted_memories[:n]]
    
    def forget_low_importance(self, threshold: float = 0.3):
        for category in self.memories:
            self.memories[category] = [
                m for m in self.memories[category]
                if m.importance >= threshold
            ]

class EpisodicMemory:
    def __init__(self):
        self.episodes: List[Dict] = []
    
    def add_episode(self, task: str, steps: List[Dict], result: str, success: bool):
        episode = {
            'task': task,
            'steps': steps,
            'result': result,
            'success': success,
            'timestamp': datetime.now().isoformat(),
            'lessons': self.extract_lessons(steps, result, success)
        }
        
        self.episodes.append(episode)
    
    def extract_lessons(self, steps: List[Dict], result: str, success: bool) -> List[str]:
        lessons = []
        
        if success:
            lessons.append("成功策略：记录有效的执行路径")
        else:
            lessons.append("失败原因：分析错误和问题")
        
        return lessons
    
    def get_similar_episodes(self, task: str) -> List[Dict]:
        similar = []
        
        for episode in self.episodes:
            if self.calculate_similarity(task, episode['task']) > 0.5:
                similar.append(episode)
        
        return similar
    
    def calculate_similarity(self, task1: str, task2: str) -> float:
        words1 = set(task1.lower().split())
        words2 = set(task2.lower().split())
        
        intersection = words1 & words2
        union = words1 | words2
        
        return len(intersection) / len(union) if union else 0

class AgentMemorySystem:
    def __init__(self):
        self.short_term = ShortTermMemory()
        self.long_term = LongTermMemory()
        self.episodic = EpisodicMemory()
    
    def add_short_term(self, content: str, metadata: Dict = None):
        self.short_term.add(content, metadata)
    
    def add_long_term(self, category: str, content: str, metadata: Dict = None):
        self.long_term.add(category, content, metadata)
    
    def add_episode(self, task: str, steps: List[Dict], result: str, success: bool):
        self.episodic.add_episode(task, steps, result, success)
    
    def get_context(self) -> Dict:
        return {
            'short_term': self.short_term.get_recent(),
            'relevant_long_term': self.long_term.search_by_keywords(['relevant']),
            'similar_episodes': self.episodic.get_similar_episodes('current_task')
        }
    
    def learn_from_experience(self):
        successful_episodes = [
            e for e in self.episodic.episodes if e['success']
        ]
        
        for episode in successful_episodes:
            self.add_long_term(
                'successful_strategies',
                episode['task'],
                {
                    'steps': episode['steps'],
                    'result': episode['result'],
                    'keywords': episode['task'].split()
                }
            )

def test_memory_system():
    memory_system = AgentMemorySystem()
    
    memory_system.add_short_term("当前任务：搜索AI新闻")
    memory_system.add_short_term("执行步骤：调用搜索工具")
    
    memory_system.add_long_term(
        "knowledge",
        "AI基础知识",
        {"keywords": ["AI", "机器学习"], "importance": 0.8}
    )
    
    steps = [
        {"thought": "需要搜索", "action": "search", "observation": "找到结果"},
        {"thought": "需要分析", "action": "analyze", "observation": "分析完成"}
    ]
    
    memory_system.add_episode("搜索AI新闻", steps, "成功找到新闻", True)
    
    print("短期记忆：")
    print(json.dumps(memory_system.short_term.get_recent(), ensure_ascii=False, indent=2))
    
    print("\n长期记忆（知识类别）：")
    print(json.dumps(memory_system.long_term.search_by_category("knowledge"), ensure_ascii=False, indent=2))
    
    print("\n情景记忆：")
    print(json.dumps(memory_system.episodic.episodes, ensure_ascii=False, indent=2))
    
    memory_system.learn_from_experience()
    
    print("\n学习后的长期记忆（成功策略）：")
    print(json.dumps(memory_system.long_term.search_by_category("successful_strategies"), ensure_ascii=False, indent=2))

if __name__ == "__main__":
    test_memory_system()
```

---

## 8.7 Agent工具集成

### 🎯 工具生态架构

```
┌─────────────────────────────────────────────────────────┐
│         Agent工具集成：扩展能力边界                        │
└─────────────────────────────────────────────────────────┘

工具分类：
┌─────────────────────────────────┐
│  1. 信息获取工具                │
│  - 搜索引擎                     │
│  - 知识库查询                   │
│  - API调用                      │
│  - 数据库访问                   │
│                                 │
│  2. 计算处理工具                │
│  - 数学计算                     │
│  - 数据分析                     │
│  - 统计处理                     │
│  - 机器学习                     │
│                                 │
│  3. 代码执行工具                │
│  - Python执行                   │
│  - Shell命令                    │
│  - 文件操作                     │
│  - 系统调用                     │
│                                 │
│  4. 内容生成工具                │
│  - 文本生成                     │
│  - 图像生成                     │
│  - 代码生成                     │
│  - 数据可视化                   │
│                                 │
│  5. 外部服务工具                │
│  - 云服务API                    │
│  - 第三方服务                   │
│  - 社交媒体                     │
│  - 通讯工具                     │
└─────────────────────────────────┘

工具设计原则：
┌─────────────────────────────────┐
│  1. 清晰的接口                  │
│  - 明确的输入参数               │
│  - 明确的输出格式               │
│  - 完整的文档                   │
│                                 │
│  2. 安全的执行                  │
│  - 权限控制                     │
│  - 输入验证                     │
│  - 异常处理                     │
│  - 结果过滤                     │
│                                 │
│  3. 高效的性能                  │
│  - 快速响应                     │
│  - 资源优化                     │
│  - 并发支持                     │
│  - 缓存机制                     │
│                                 │
│  4. 可扩展性                    │
│  - 插件机制                     │
│  - 动态加载                     │
│  - 版本管理                     │
│  - 配置灵活                     │
└─────────────────────────────────┘
```

### 💻 工具集成实现

```python
from typing import Dict, List, Any, Callable
import json
import subprocess
import requests
from abc import ABC, abstractmethod

class ToolRegistry:
    def __init__(self):
        self.tools: Dict[str, Tool] = {}
        self.categories: Dict[str, List[str]] = defaultdict(list)
    
    def register(self, tool: Tool):
        self.tools[tool.name()] = tool
        
        category = tool.category()
        self.categories[category].append(tool.name())
    
    def get_tool(self, name: str) -> Optional[Tool]:
        return self.tools.get(name)
    
    def list_tools(self, category: str = None) -> List[Dict]:
        if category:
            tool_names = self.categories.get(category, [])
        else:
            tool_names = list(self.tools.keys())
        
        return [
            {
                'name': name,
                'description': self.tools[name].description(),
                'parameters': self.tools[name].parameters(),
                'category': self.tools[name].category()
            }
            for name in tool_names
        ]

class WebSearchTool(Tool):
    def name(self) -> str:
        return "web_search"
    
    def description(self) -> str:
        return "搜索网络信息"
    
    def parameters(self) -> Dict:
        return {
            'query': {'type': 'string', 'description': '搜索关键词'},
            'num_results': {'type': 'integer', 'description': '返回结果数量', 'default': 5}
        }
    
    def category(self) -> str:
        return "information"
    
    def execute(self, query: str, num_results: int = 5) -> Any:
        return {
            'results': [
                {'title': f'搜索结果{i}', 'url': f'http://example.com/{i}', 'snippet': f'内容{i}'}
                for i in range(num_results)
            ]
        }

class CodeExecutionTool(Tool):
    def name(self) -> str:
        return "execute_code"
    
    def description(self) -> str:
        return "执行Python代码"
    
    def parameters(self) -> Dict:
        return {
            'code': {'type': 'string', 'description': 'Python代码'},
            'timeout': {'type': 'integer', 'description': '执行超时时间', 'default': 30}
        }
    
    def category(self) -> str:
        return "computation"
    
    def execute(self, code: str, timeout: int = 30) -> Any:
        try:
            result = subprocess.run(
                ['python', '-c', code],
                capture_output=True,
                text=True,
                timeout=timeout
            )
            
            return {
                'stdout': result.stdout,
                'stderr': result.stderr,
                'returncode': result.returncode
            }
        except subprocess.TimeoutExpired:
            return {'error': '执行超时'}
        except Exception as e:
            return {'error': str(e)}

class APICallTool(Tool):
    def name(self) -> str:
        return "api_call"
    
    def description(self) -> str:
        return "调用外部API"
    
    def parameters(self) -> Dict:
        return {
            'url': {'type': 'string', 'description': 'API URL'},
            'method': {'type': 'string', 'description': 'HTTP方法', 'default': 'GET'},
            'headers': {'type': 'object', 'description': '请求头'},
            'params': {'type': 'object', 'description': '请求参数'}
        }
    
    def category(self) -> str:
        return "external_service"
    
    def execute(self, url: str, method: str = 'GET', headers: Dict = None, params: Dict = None) -> Any:
        try:
            response = requests.request(
                method,
                url,
                headers=headers,
                params=params,
                timeout=10
            )
            
            return {
                'status_code': response.status_code,
                'data': response.json() if response.headers.get('content-type', '').includes('application/json') else response.text
            }
        except Exception as e:
            return {'error': str(e)}

class ToolCallingSystem:
    def __init__(self, llm_client, tool_registry: ToolRegistry):
        self.llm_client = llm_client
        self.registry = tool_registry
    
    def analyze_tool_need(self, task: str) -> Dict:
        tools_info = self.registry.list_tools()
        
        prompt = f"""分析任务需要哪些工具：

任务：{task}

可用工具：
{json.dumps(tools_info, ensure_ascii=False, indent=2)}

请以JSON格式输出：
{
    "needed_tools": ["工具名称列表"],
    "execution_plan": [
        {
            "step": 1,
            "tool": "工具名称",
            "parameters": {},
            "reason": "使用原因"
        }
    ]
}

分析结果："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3
        )
        
        try:
            analysis = json.loads(response.choices[0].message.content)
            return analysis
        except:
            return {'needed_tools': [], 'execution_plan': []}
    
    def execute_tool_plan(self, plan: List[Dict]) -> List[Dict]:
        results = []
        
        for step in plan:
            tool_name = step.get('tool')
            parameters = step.get('parameters', {})
            
            tool = self.registry.get_tool(tool_name)
            
            if tool:
                result = tool.execute(**parameters)
                
                results.append({
                    'step': step['step'],
                    'tool': tool_name,
                    'parameters': parameters,
                    'result': result,
                    'success': 'error' not in result
                })
            else:
                results.append({
                    'step': step['step'],
                    'tool': tool_name,
                    'result': {'error': '工具不存在'},
                    'success': False
                })
        
        return results
    
    def process_with_tools(self, task: str) -> Dict:
        analysis = self.analyze_tool_need(task)
        
        results = self.execute_tool_plan(analysis['execution_plan'])
        
        prompt = f"""基于工具执行结果完成任务：

任务：{task}
工具执行结果：
{json.dumps(results, ensure_ascii=False, indent=2)}

请给出最终答案："""
        
        response = self.llm_client.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.5
        )
        
        return {
            'task': task,
            'tool_analysis': analysis,
            'tool_results': results,
            'final_answer': response.choices[0].message.content
        }

def test_tool_integration():
    registry = ToolRegistry()
    
    registry.register(WebSearchTool())
    registry.register(CodeExecutionTool())
    registry.register(APICallTool())
    
    print("已注册工具：")
    for category, tools in registry.categories.items():
        print(f"\n{category}类别：")
        for tool_name in tools:
            tool = registry.tools[tool_name]
            print(f"  - {tool_name}: {tool.description()}")
    
    print("\n工具参数示例：")
    for tool_name in ['web_search', 'execute_code']:
        tool = registry.get_tool(tool_name)
        print(f"\n{tool_name}参数：")
        print(json.dumps(tool.parameters(), ensure_ascii=False, indent=2))

if __name__ == "__main__":
    test_tool_integration()
```

---

## 8.8 Agent评估与优化

### 🎯 Agent评估指标

```
┌─────────────────────────────────────────────────────────┐
│         Agent性能评估体系                                │
└─────────────────────────────────────────────────────────┘

评估维度：
┌─────────────────────────────────┐
│  1. 任务完成度                  │
│  - 是否完成任务                 │
│  - 完成质量如何                 │
│  - 是否达到目标                 │
│                                 │
│  2. 执行效率                    │
│  - 步骤数量                     │
│  - 执行时间                     │
│  - 资源消耗                     │
│                                 │
│  3. 决策质量                    │
│  - 工具选择是否合理             │
│  - 参数设置是否正确             │
│  - 策略是否有效                 │
│                                 │
│  4. 错误处理                    │
│  - 是否能检测错误