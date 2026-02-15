# 🔬 AI Research Tools & Papers Hub

> A curated collection of cutting-edge AI research, developer tools, and emerging protocols shaping the future of artificial intelligence.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/selamajarrahi/ai-research-hub/pulls)

## 🎯 What is This?

This hub tracks the **frontier of AI research** — papers, tools, and protocols that are actively changing how we build and deploy AI systems. Unlike general ML resource lists, we focus on:

- 📄 **Papers** that introduce genuinely novel techniques
- 🛠️ **Tools** that significantly improve AI development workflows
- 📡 **Protocols** that standardize AI agent interactions
- 📖 **Guides** for putting research into practice

---

## 🗺️ Quick Navigation

| Section | Description | Go To |
|---------|-------------|-------|
| 📄 Papers | Research paper summaries with key insights | [papers/](papers/) |
| 🛠️ Tools | Developer tools & frameworks | [tools/](tools/) |
| 📡 Protocols | Standards like MCP, A2UI | [protocols/](protocols/) |
| 📖 Guides | Practical how-to guides | [guides/](guides/) |

---

## 📄 Featured Papers

### Training & Optimization

| Paper | Key Innovation | Difficulty | Implementation |
|-------|---------------|------------|----------------|
| [Training-Free GRPO](papers/training-free-grpo.md) | RL without fine-tuning via semantic advantages | ⭐⭐⭐ | ✅ Available |
| [Old Optimizer New Norm](papers/old-optimizer-new-norm.md) | Unified view of optimizers as steepest descent | ⭐⭐⭐⭐ | ✅ Muon |
| [Scaling Monosemanticity](papers/scaling-monosemanticity.md) | Interpretable features in Claude | ⭐⭐⭐⭐ | 🔬 Research |
| [Constitutional AI](papers/constitutional-ai.md) | Self-supervised preference learning | ⭐⭐⭐ | ✅ Claude |

### Agent Systems

| Paper | Key Innovation | Difficulty | Implementation |
|-------|---------------|------------|----------------|
| [ReAct](papers/react.md) | Reasoning + Acting paradigm | ⭐⭐ | ✅ LangChain |
| [Toolformer](papers/toolformer.md) | Self-taught tool use | ⭐⭐⭐ | ✅ Hugging Face |
| [Voyager](papers/voyager.md) | Lifelong learning agent | ⭐⭐⭐ | ✅ GitHub |

### Efficiency & Inference

| Paper | Key Innovation | Difficulty | Implementation |
|-------|---------------|------------|----------------|
| [Flash Attention 3](papers/flash-attention-3.md) | IO-aware exact attention | ⭐⭐⭐⭐ | ✅ Production |
| [Speculative Decoding](papers/speculative-decoding.md) | Draft-then-verify speedup | ⭐⭐⭐ | ✅ vLLM |
| [Mixture of Experts](papers/mixture-of-experts.md) | Sparse activation patterns | ⭐⭐⭐ | ✅ Mixtral, DeepSeek |

---

## 🛠️ Featured Tools

### Development Frameworks

| Tool | Category | What It Does | Guide |
|------|----------|--------------|-------|
| [Augment Code](tools/augment-code.md) | Coding Assistant | Context Engine for AI-powered development | [→](tools/augment-code.md) |
| [Cursor](tools/cursor.md) | IDE | AI-first code editor | [→](tools/cursor.md) |
| [Aider](tools/aider.md) | CLI | Terminal-based AI pair programming | [→](tools/aider.md) |
| [Continue](tools/continue.md) | Extension | Open-source Copilot alternative | [→](tools/continue.md) |

### Infrastructure

| Tool | Category | What It Does | Guide |
|------|----------|--------------|-------|
| [vLLM](tools/vllm.md) | Inference | High-throughput LLM serving | [→](tools/vllm.md) |
| [SGLang](tools/sglang.md) | Inference | Structured generation runtime | [→](tools/sglang.md) |
| [Ollama](tools/ollama.md) | Local | Run LLMs locally | [→](tools/ollama.md) |

### Evaluation & Testing

| Tool | Category | What It Does | Guide |
|------|----------|--------------|-------|
| [LMSIS Arena](tools/lmsys-arena.md) | Benchmarking | Crowdsourced model comparison | [→](tools/lmsys-arena.md) |
| [Inspect AI](tools/inspect-ai.md) | Evaluation | Framework for LLM evals | [→](tools/inspect-ai.md) |
| [PromptFoo](tools/promptfoo.md) | Testing | Prompt evaluation & testing | [→](tools/promptfoo.md) |

---

## 📡 Featured Protocols

| Protocol | Organization | Purpose | Guide |
|----------|--------------|---------|-------|
| [Model Context Protocol (MCP)](protocols/mcp.md) | Anthropic | Standardized tool & context interface | [→](protocols/mcp.md) |
| [A2UI](protocols/a2ui.md) | Google | Agent-to-UI interaction standard | [→](protocols/a2ui.md) |
| [OpenAI Function Calling](protocols/openai-functions.md) | OpenAI | Structured output & tool use | [→](protocols/openai-functions.md) |
| [Agent Protocol](protocols/agent-protocol.md) | AI Engineer Foundation | Universal agent API | [→](protocols/agent-protocol.md) |

---

## 📖 Guides

### Getting Started
| Guide | Description |
|-------|-------------|
| [Reading AI Papers](guides/reading-papers.md) | How to efficiently read ML papers |
| [Setting Up Local LLMs](guides/local-llms.md) | Run models on your own hardware |
| [Building Your First Agent](guides/first-agent.md) | From zero to working agent |

### Deep Dives
| Guide | Description |
|-------|-------------|
| [Implementing Research Papers](guides/implementing-papers.md) | Tips for reproducing results |
| [Benchmarking Best Practices](guides/benchmarking.md) | How to evaluate fairly |
| [Prompt Engineering Patterns](guides/prompt-patterns.md) | Advanced prompting techniques |

---

## 📚 Reading Lists

### By Topic

| Topic | Papers | Estimated Time |
|-------|--------|----------------|
| [Reinforcement Learning from Human Feedback](guides/reading-list-rlhf.md) | 8 papers | ~4 hours |
| [Attention Mechanisms](guides/reading-list-attention.md) | 6 papers | ~3 hours |
| [Agent Architectures](guides/reading-list-agents.md) | 10 papers | ~5 hours |
| [Efficient Inference](guides/reading-list-inference.md) | 7 papers | ~3.5 hours |

### By Difficulty

| Level | Description | Start Here |
|-------|-------------|------------|
| ⭐ Beginner | Blog posts, explainers | [Attention is All You Need (Annotated)](papers/attention-annotated.md) |
| ⭐⭐ Intermediate | Core papers with math | [ReAct](papers/react.md) |
| ⭐⭐⭐ Advanced | Novel techniques | [Training-Free GRPO](papers/training-free-grpo.md) |
| ⭐⭐⭐⭐ Expert | Frontier research | [Scaling Monosemanticity](papers/scaling-monosemanticity.md) |

---

## 🔬 Labs & Researchers to Follow

### Major Labs
| Lab | Focus Areas | Notable Work |
|-----|-------------|--------------|
| [Anthropic](https://anthropic.com) | Safety, Interpretability | Claude, Constitutional AI |
| [OpenAI](https://openai.com) | Capabilities, RLHF | GPT series, o1 |
| [DeepMind](https://deepmind.google) | RL, Multimodal | AlphaFold, Gemini |
| [Meta FAIR](https://ai.meta.com) | Open Research | Llama, OPT |
| [Mistral AI](https://mistral.ai) | Efficient Models | Mixtral, Mistral |
| [DeepSeek](https://deepseek.com) | Open MoE | DeepSeek-V3, R1 |

### Key Researchers
| Researcher | Affiliation | Known For |
|------------|-------------|-----------|
| Ilya Sutskever | SSI | Scaling laws, GPT |
| Jan Leike | Anthropic | Alignment, RLHF |
| Jeremy Bernstein | Modula | Muon optimizer |
| Jason Wei | OpenAI | Chain-of-thought |
| Tri Dao | Princeton/Together | FlashAttention |

---

## 🗂️ Repository Structure

```
ai-research-hub/
├── README.md              # You are here
├── papers/                # Research paper summaries
│   ├── training-free-grpo.md
│   ├── react.md
│   ├── flash-attention-3.md
│   └── ...
├── tools/                 # Developer tools & frameworks
│   ├── augment-code.md
│   ├── vllm.md
│   ├── cursor.md
│   └── ...
├── protocols/             # Standards & specifications
│   ├── mcp.md
│   ├── a2ui.md
│   └── ...
└── guides/                # Practical how-to guides
    ├── reading-papers.md
    ├── implementing-papers.md
    └── ...
```

---

## 🔄 Update Log

| Date | Update |
|------|--------|
| Feb 2026 | Initial release with 20+ papers, 10+ tools, 4 protocols |

---

## 🤝 Contributing

We welcome contributions! To add:

1. **Papers**: Create `papers/<paper-name>.md` with summary, key insights, implementation status
2. **Tools**: Create `tools/<tool-name>.md` with overview, use cases, getting started
3. **Protocols**: Create `protocols/<protocol-name>.md` with spec summary, examples, adoption status

---

*Maintained by codmire_'s assistant 🐈⬛*

*Last updated: February 2026*
