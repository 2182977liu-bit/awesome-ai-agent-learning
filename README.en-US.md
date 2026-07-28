# AI Agent from Beginner to Practical Application

> Author: Qingsong and Sangye | Understand the essence, rather than stacking frameworks

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

## Foreword

From 2024 to 2025, AI Agents have experienced explosive growth. From AutoGPT to Manus, and from simple chatbots to intelligent agents capable of autonomously completing complex tasks, Agents are redefining how we interact with AI. However, when I tried to study this technology systematically, I found that most tutorials either remain at a conceptual level—ending once they explain "what an Agent is"—or throw a pile of framework code at you immediately, asking you to copy it without understanding the underlying principles. This experience of "knowing the what but not the why" led me to decide to write my own tutorial.

My teaching philosophy is simple: **understand the principles first, then learn the frameworks; write code first, then study the theory.** I believe that only by building an Agent from scratch using pure Python can you truly understand what frameworks like LangChain and Dify are doing for you. Otherwise, you are just calling APIs, not building intelligent agents. This tutorial will guide you from the most basic LLM calls, step-by-step, to building a complete Agent system, and then introduce frameworks to improve development efficiency.

The biggest difference between this tutorial and others is that it is **nanny-level**, **starts from zero**, and consists of **runnable steps** in original Chinese content. It will not assume you already know something, it will not skip any critical steps, and it will not confuse you with a mountain of jargon. Every line of code has comments, every concept has a plain-language explanation, and every chapter has runnable examples. If you are willing to invest the time to follow along, I guarantee you can transform from an Agent novice into a developer capable of independently building Agent applications.

## What You Will Gain

- 🧠 Understand the core principles of AI Agents (ReAct, Tool Calling, Memory Systems)
- 💻 Build a complete Agent from scratch using pure Python without relying on any frameworks
- 🔧 Master key technologies such as Function Calling, RAG, and Multi-Agent Collaboration
- 🏭 Learn to use frameworks like LangChain / Dify to quickly build production-grade applications
- 📝 Runnable code for every step—learn by doing
- 🎯 Complete a graduation project and have your own Agent portfolio
- 💡 Understand Agent design patterns to independently design and implement new Agents
- 🚀 Learn about production-level topics like Agent deployment, evaluation, and monitoring

## Who Should Read This

- Developers with basic Python skills (no AI background required)
- Engineers who want to understand Agent principles without slogged through academic papers
- Product Managers/Data Analysts who want to use Agent technology to improve work efficiency
- Students who want to systematically learn AI Agent technology

## Learning Roadmap

```
Part 1: Cognition ──→ Part 2: Construction ──→ Part 3: Frameworks ──→ Part 4: Practical Application
  Understand Essence       Build from Zero         Learn Main Frameworks       Graduation Project
```

### Part 1: Cognition —— Understanding the Essence of Agents

| Chapter | Title | Core Content | Status |
|------|------|----------|------|
| Ch 1 | [What is an AI Agent](docs/01-what-is-agent/README.md) | Definition of Agents, core capabilities, history of development | ✅ |
| Ch 2 | [LLM Basics](docs/02-llm-basics/README.md) | LLM principles, API calls, Prompt Engineering | ✅ |
| Ch 3 | [Core Components of an Agent](docs/03-agent-components/README.md) | Brain (LLM), Tools, Memory, Planning | ✅ |

### Part 2: Construction —— Building an AI Agent from Scratch

| Chapter | Title | Core Content | Status |
|------|------|----------|------|
| Ch 4 | [Writing Your First Agent](docs/04-first-agent/README.md) | Basic LLM calls → Adding Tools → ReAct loop | ✅ |
| Ch 5 | [Giving Agents Memory](docs/05-agent-memory/README.md) | Short-term memory, long-term memory, context management | ✅ |
| Ch 6 | [Giving Agents Tools](docs/06-agent-tools/README.md) | Function Calling, custom tools, tool chains | ✅ |
| Ch 7 | [Teaching Agents to Plan](docs/07-agent-planning/README.md) | Plan-and-Solve, reflection mechanisms, self-correction | 🚧 |

### Part 3: Advanced —— Agent Behavior Engineering & Framework Practice

| Chapter | Title | Core Content | Status |
|------|------|----------|------|
| Ch 8 | [Agent Behavior Engineering](docs/08-agent-behavior-engineering/README.md) | Skills as Code, De-rationalization, Gating systems, Sub-agent architecture, Persuasion psychology | ✅ |
| Ch 9 | [Quick Start with LangChain](docs/09-langchain/README.md) | Chain, Agent, Memory, Tool integration | 🚧 |
| Ch 10 | [LangGraph State Machines](docs/10-langgraph/README.md) | Graph structures, state management, Human-in-the-loop | 🚧 |
| Ch 11 | [Visual Construction with Dify](docs/11-dify/README.md) | Low-code Agents, workflow orchestration | 🚧 |

### Part 4: Practical Application —— Comprehensive Projects

| Chapter | Title | Core Content | Status |
|------|------|----------|------|
| Ch 12 | [RAG Knowledge Base Agent](docs/12-rag-agent/README.md) | Document parsing, vector retrieval, knowledge Q&A | 🚧 |
| Ch 13 | [Multi-Agent Collaboration Systems](docs/13-multi-agent/README.md) | Inter-agent communication, task allocation, collaboration patterns | 🚧 |
| Capstone | [Intelligent Research Assistant](docs/14-capstone/README.md) | Comprehensive application of all techniques to build a full Agent app | 🚧 |

> ✅ = Completed | 🚧 = In Progress | ⏳ = Planned

## What Each Chapter Contains

Every chapter follows a consistent teaching structure to ensure a complete learning experience:

- **Concept Explanation**: Core concepts explained in plain language with diagrams, avoiding jargon overload.
- **Complete Code**: Runnable Python code for every step, accompanied by detailed comments.
- **Execution Demo**: Showcases the code execution process and expected output so you know what the result should look like.
- **Hands-on Exercises**: Exercises at the end of each chapter to help consolidate learning and deepen understanding.
- **Pitfall Records**: Documentation of common errors and troubleshooting methods to help you avoid common mistakes.

## Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/2182977liu-bit/awesome-ai-agent-learning.git
cd awesome-ai-agent-learning

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure API Key (Supports OpenAI / DeepSeek / Qwen, etc.)
export API_KEY="your-api-key"
export BASE_URL="https://api.openai.com/v1"
export MODEL="gpt-4o-mini"

# 4. Start learning! Begin with Chapter 1
```

## Recommended Learning Resources

The following are Agent-related projects that I found most valuable during my learning process, recommended as supplementary reading:

| Project | Description | Link |
|------|------|------|
| Datawhale Hello-Agents | Most systematic Chinese Agent tutorial | [GitHub](https://github.com/datawhalechina/hello-agents) |
| Microsoft AI Agents for Beginners | Official Microsoft Agent introductory course | [GitHub](https://github.com/microsoft/ai-agents-for-beginners) |
| AI Agents From Scratch | Build Agents from zero, no framework dependency | [GitHub](https://github.com/pguso/ai-agents-from-scratch) |
| GenAI Agents | Collection of 45+ Agent implementations | [GitHub](https://github.com/NirDiamant/GenAI_Agents) |
| Dify | Open-source LLM application development platform | [GitHub](https://github.com/langgenius/dify) |
| LangChain | Most popular LLM application framework | [GitHub](https://github.com/langchain-ai/langchain) |
| MetaGPT | Multi-Agent meta-programming framework | [GitHub](https://github.com/FoundationAgents/MetaGPT) |
| Microsoft AutoGen | Microsoft's multi-agent framework | [GitHub](https://github.com/microsoft/autogen) |
| Langchain-Chatchat | Chinese knowledge base Q&A system | [GitHub](https://github.com/chatchat-space/Langchain-Chatchat) |
| Awesome Chinese AI Agents | Chinese Agent resource library | [GitHub](https://github.com/happydog-intj/awesome-chinese-ai-agents) |

## FAQ

**What foundations do I need?**

Basic Python knowledge is enough. If you can write simple Python functions and understand lists and dictionaries, you can start. No AI or Machine Learning background is required; the tutorial starts from the most basic concepts.

**Do I need a paid API?**

No. The tutorial supports multiple LLM providers. You can use free quotas provided by DeepSeek, Qwen, etc., to complete all exercises. Of course, if you have an OpenAI API Key, you can use it directly.

**Can the code run locally?**

Yes. All code has been tested in actual environments to ensure it runs correctly locally. Each chapter specifies the required dependencies and environment; just follow the steps.

**What is the suggested learning pace?**

I recommend studying 1-2 chapters per week, with each chapter taking about 2-4 hours (including reading, coding, and exercises). At this pace, you can complete all content in about 2-3 months.

**How can I contribute?**

Your participation is very welcome! You can report issues or suggest improvements via Issues, or contribute code or new chapters via Pull Requests. See the [Contribution Guide](#contribution-guide) below.

## Contribution Guide

The growth of this tutorial depends on the help of every contributor. You can participate in the following ways:

- **Submit Issues**: Report errors, suggest improvements, or request new content.
- **Submit PRs**: Fix bugs, optimize code, supplement content, or translate documentation.
- **Write New Chapters**: If you want to contribute new content, please open an Issue to discuss the outline first.
- **Share**: If you find this tutorial helpful, feel free to share it with others interested in learning Agents.

## Acknowledgements

Thanks to the open-source community and all the excellent educational projects. Your work has made the dissemination of AI technology much easier. Special thanks to Datawhale, Microsoft, and the LangChain community for their continuous investment in AI education. Also, thanks to everyone who reads this tutorial, provides suggestions, and contributes.

## License

This project is open-sourced under the [MIT License](https://opensource.org/licenses/MIT). You are free to use, modify, and distribute it.

## About the Author

**Qingsong and Sangye** —— A developer passionate about AI technology who believes the best way to learn is through practice. The original intent for writing this tutorial was simple: I hope everyone who wants to learn Agents can find a clear, actionable learning path. If you have any questions during your learning process, feel free to communicate with me via GitHub Issues.
