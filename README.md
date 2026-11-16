# AI Agent 从入门到实战

> 作者：青松与桑叶 | 理解本质，而非堆砌框架

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

## 写在前面

2024 年到 2025 年，AI Agent 迎来了爆发式增长。从 AutoGPT 到 Manus，从简单的对话机器人到能自主完成复杂任务的智能体，Agent 正在重新定义我们与 AI 交互的方式。然而，当我试图系统学习这项技术时，发现大多数教程要么只停留在概念层面，讲完"什么是 Agent"就结束了；要么一上来就丢给你一堆框架代码，让你照着抄却完全不理解背后的原理。这种"知其然不知其所以然"的学习体验，让我决定自己写一个教程。

我的教学理念很简单：**先理解原理，再学框架；先动手写，再学理论。** 我相信，只有亲手用纯 Python 从零实现一个 Agent，你才能真正理解 LangChain、Dify 这些框架在帮你做什么。否则，你只是在调 API，而不是在构建智能体。这个教程会带你从最基础的 LLM 调用开始，一步步搭建出完整的 Agent 系统，然后再引入框架来提升开发效率。

这个教程和其他教程最大的区别在于：它是**保姆级**的、**从零开始**的、**每一步都可运行**的中文原创内容。不会假设你已经懂了什么，不会跳过任何关键步骤，不会用一堆术语把你绕晕。每一行代码都有中文注释，每一个概念都有通俗的解释，每一个章节都有可运行的示例。如果你愿意花时间跟着做，我保证你能从一个 Agent 小白变成能独立构建 Agent 应用的开发者。

## 你将收获

- 🧠 理解 AI Agent 的核心原理（ReAct、工具调用、记忆系统）
- 💻 不依赖任何框架，用纯 Python 从零构建一个完整的 Agent
- 🔧 掌握 Function Calling、RAG、多 Agent 协作等关键技术
- 🏭 学会用 LangChain / Dify 等框架快速构建生产级应用
- 📝 每一步都有可运行的代码，跟着做就能学会
- 🎯 完成毕业项目，拥有自己的 Agent 作品
- 💡 理解 Agent 设计模式，能独立设计和实现新 Agent
- 🚀 了解 Agent 部署、评估、监控等生产级话题

## 适合谁读

- 有基础 Python 能力的开发者（不需要 AI 背景）
- 想理解 Agent 原理但不想啃论文的工程师
- 想用 Agent 技术提升工作效率的产品经理/数据分析师
- 在校学生，想系统学习 AI Agent 技术

## 学习路线图

```
第一部分：认知篇 ──→ 第二部分：构建篇 ──→ 第三部分：框架篇 ──→ 第四部分：实战篇
  理解Agent本质       从零写Agent          学主流框架          做毕业项目
```

### 第一部分：认知篇 —— 理解 Agent 的本质

| 章节 | 标题 | 核心内容 | 状态 |
|------|------|----------|------|
| 第1章 | [什么是 AI Agent](docs/01-what-is-agent/README.md) | Agent 的定义、与 RL Agent 的术语辨析、核心能力、发展历程 | ✅ |
| 第2章 | [LLM 基础入门](docs/02-llm-basics/README.md) | LLM 原理、API 调用、Prompt Engineering | ✅ |
| 第3章 | [Agent 的核心组件](docs/03-agent-components/README.md) | 大脑（LLM）、工具（Tools）、记忆（Memory）、规划（Planning） | ✅ |

### 第二部分：构建篇 —— 从零构建 AI Agent

| 章节 | 标题 | 核心内容 | 状态 |
|------|------|----------|------|
| 第4章 | [手写第一个 Agent](docs/04-first-agent/README.md) | 基础 LLM 调用 → 添加工具 → ReAct 循环 | ✅ |
| 第5章 | [让 Agent 拥有记忆](docs/05-agent-memory/README.md) | 短期记忆、长期记忆、上下文管理 | ✅ |
| 第6章 | [让 Agent 使用工具](docs/06-agent-tools/README.md) | Function Calling、自定义工具、工具链 | ✅ |
| 第7章 | [让 Agent 学会规划](docs/07-agent-planning/README.md) | Plan-and-Solve、反思机制、自我纠错 | 🚧 |

### 第三部分：进阶篇 —— Agent 行为工程与框架实战

| 章节 | 标题 | 核心内容 | 状态 |
|------|------|----------|------|
| 第8章 | [Agent 行为工程](docs/08-agent-behavior-engineering/README.md) | 技能即代码、反合理化、门控系统、子代理架构、说服心理学 | ✅ |
| 第9章 | [LangChain 快速上手](docs/09-langchain/README.md) | Chain、Agent、Memory、Tool 集成 | 🚧 |
| 第10章 | [LangGraph 状态机](docs/10-langgraph/README.md) | 图结构、状态管理、人机协作 | 🚧 |
| 第11章 | [Dify 可视化构建](docs/11-dify/README.md) | 低代码 Agent、工作流编排 | 🚧 |

### 第四部分：实战篇 —— 综合项目

| 章节 | 标题 | 核心内容 | 状态 |
|------|------|----------|------|
| 第12章 | [RAG 知识库 Agent](docs/12-rag-agent/README.md) | 文档解析、向量检索、知识问答 | 🚧 |
| 第13章 | [多 Agent 协作系统](docs/13-multi-agent/README.md) | Agent 间通信、任务分配、协作模式 | 🚧 |
| 毕业项目 | [智能研究助手](docs/14-capstone/README.md) | 综合运用所有技术，构建完整 Agent 应用 | 🚧 |

> ✅ = 已完成 | 🚧 = 编写中 | ⏳ = 计划中

## 每章包含什么

每一章都遵循统一的教学结构，确保你获得完整的学习体验：

- **概念讲解**：用通俗的语言解释核心概念，配合图解，不堆砌术语
- **完整代码**：每一步都有可运行的 Python 代码，附详细中文注释
- **运行演示**：展示代码的运行过程和预期输出，让你知道结果应该长什么样
- **动手练习**：每章末尾有练习题，帮你巩固所学，加深理解
- **踩坑记录**：记录常见错误和排查方法，帮你少走弯路

## 快速开始

```bash
# 1. 克隆仓库
git clone https://github.com/2182977liu-bit/awesome-ai-agent-learning.git
cd awesome-ai-agent-learning

# 2. 安装依赖
pip install -r requirements.txt

# 3. 配置 API Key（支持 OpenAI / DeepSeek / 通义千问等）
export API_KEY="your-api-key"
export BASE_URL="https://api.openai.com/v1"
export MODEL="gpt-4o-mini"

# 4. 开始学习！从第1章开始
```

## 推荐学习资源

以下是我在学习过程中觉得最有价值的 Agent 相关项目，推荐给你作为补充阅读：

| 项目 | 说明 | 链接 |
|------|------|------|
| Datawhale Hello-Agents | 最系统的中文 Agent 教程 | [GitHub](https://github.com/datawhalechina/hello-agents) |
| Microsoft AI Agents for Beginners | 微软官方 Agent 入门课程 | [GitHub](https://github.com/microsoft/ai-agents-for-beginners) |
| AI Agents From Scratch | 从零构建 Agent，无框架依赖 | [GitHub](https://github.com/pguso/ai-agents-from-scratch) |
| GenAI Agents | 45+ 个 Agent 实现合集 | [GitHub](https://github.com/NirDiamant/GenAI_Agents) |
| Dify | 开源 LLM 应用开发平台 | [GitHub](https://github.com/langgenius/dify) |
| LangChain | 最流行的 LLM 应用框架 | [GitHub](https://github.com/langchain-ai/langchain) |
| MetaGPT | 多 Agent 元编程框架 | [GitHub](https://github.com/FoundationAgents/MetaGPT) |
| Microsoft AutoGen | 微软多 Agent 框架 | [GitHub](https://github.com/microsoft/autogen) |
| Langchain-Chatchat | 中文知识库问答系统 | [GitHub](https://github.com/chatchat-space/Langchain-Chatchat) |
| Awesome Chinese AI Agents | 中文 Agent 资源库 | [GitHub](https://github.com/happydog-intj/awesome-chinese-ai-agents) |

**收录标准**：本表只收**公开可验证、对学习者有直接帮助**的教学项目或开源代码库——能读到源码、能复现、不是纯导流页。第三方 Skill/工具市场、需付费才能看到内容的平台、以推广为目的的投稿不在收录范围。如果你觉得有项目符合以上标准，欢迎提交 PR 说明它解决什么问题、怎么复现。

## 常见问题

**需要什么基础？**

只需要 Python 基础就够了。如果你能写简单的 Python 函数、理解列表和字典，就可以开始学习。不需要任何 AI 或机器学习背景，教程会从最基础的概念讲起。

**需要付费 API 吗？**

不需要。教程支持多种 LLM 服务商，你可以使用 DeepSeek、通义千问等提供的免费额度来完成所有练习。当然，如果你有 OpenAI 的 API Key 也可以直接使用。

**代码能在本地运行吗？**

可以。所有代码都经过实际测试，确保可以在本地环境正常运行。每章都会说明所需的依赖和运行环境，跟着步骤操作即可。

**教程的学习节奏是怎样的？**

建议每周学习 1-2 章，每章大约需要 2-4 小时（包括阅读、敲代码和做练习）。按照这个节奏，大约 2-3 个月可以完成全部内容。

**如何参与贡献？**

非常欢迎你的参与！你可以通过提交 Issue 报告问题或提出建议，也可以通过 Pull Request 贡献代码或新章节。详见下方[贡献指南](#贡献指南)。

**这里的"Agent"和"规划"跟强化学习里的是同一个意思吗？**

不是。RL 里的 agent 指 MDP 中与环境交互的抽象决策主体，planning 指在已知环境模型下搜索最优动作序列；本教程的 AI Agent 是"LLM 作为决策核心"的实现，规划指任务分解与步骤生成，没有显式环境模型、也不保证最优。完整辨析和术语对照表见[第1章 1.2 / 1.4 节](docs/01-what-is-agent/README.md)。

## 贡献指南

这个教程的成长离不开每一位贡献者的帮助。你可以通过以下方式参与：

- **提交 Issue**：发现错误、有改进建议、或者想请求新内容，都可以开一个 Issue
- **提交 PR**：修复 Bug、优化代码、补充内容、翻译文档，欢迎直接提交 Pull Request
- **编写新章节**：如果你想贡献新的章节内容，可以先开 Issue 讨论大纲，确认后开始编写
- **分享传播**：如果你觉得这个教程对你有帮助，欢迎分享给更多想学习 Agent 的朋友

## 致谢

感谢开源社区和所有优秀的教学项目，是你们的工作让 AI 技术的传播变得更加容易。特别感谢 Datawhale、Microsoft、LangChain 社区等在 AI 教育领域的持续投入。也感谢每一位阅读这个教程、提出建议、提交贡献的你。

## 许可证

本项目基于 [MIT License](https://opensource.org/licenses/MIT) 开源，你可以自由使用、修改和分发。

## 关于作者

**青松与桑叶** —— 一个热爱 AI 技术的开发者，相信最好的学习方式是动手实践。写这个教程的初衷很简单：希望让每一个想学 Agent 的人都能找到一条清晰、可操作的学习路径。如果你在学习过程中有任何问题，欢迎通过 GitHub Issue 与我交流。
