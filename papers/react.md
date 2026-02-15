# ReAct: Synergizing Reasoning and Acting in Language Models

> **arXiv**: 2210.03629  
> **Authors**: Shunyu Yao, Jeffrey Zhao, Dian Yu, Nan Du, Izhak Shafran, Karthik Narasimhan, Yuan Cao  
> **Organization**: Princeton, Google  
> **Difficulty**: ⭐⭐ Intermediate

## 🎯 Key Innovation

**Interleaving reasoning traces with actions.** ReAct shows that language models can solve complex tasks better by explicitly generating reasoning steps (thoughts) before taking actions, rather than directly mapping inputs to outputs.

## 📋 Summary

Before ReAct, language model agents typically either:
1. **Reasoned** (chain-of-thought): Generated step-by-step thinking but took no actions
2. **Acted** (tool use): Called tools/APIs but without explicit reasoning

ReAct combines both: Think → Act → Observe → Think → Act → ...

### The ReAct Pattern

```
Question: What is the elevation of the birthplace of the 
          inventor of the telephone?

Thought 1: I need to find who invented the telephone.
Action 1: Search[inventor of telephone]
Observation 1: The telephone was invented by Alexander Graham Bell.

Thought 2: Now I need to find Bell's birthplace.
Action 2: Search[Alexander Graham Bell birthplace]
Observation 2: Alexander Graham Bell was born in Edinburgh, Scotland.

Thought 3: Now I need the elevation of Edinburgh.
Action 3: Search[Edinburgh elevation]
Observation 3: Edinburgh has an elevation of 47 meters.

Thought 4: I have all the information needed.
Action 4: Finish[47 meters]
```

## 🔧 Implementation

### Basic ReAct Prompt

```python
REACT_PROMPT = """
Solve the following task by interleaving Thought, Action, and Observation steps.

Available actions:
- Search[query]: Search Wikipedia for information
- Lookup[keyword]: Find specific information in last search result
- Finish[answer]: Return the final answer

Question: {question}

{scratchpad}
"""

def react_step(model, question, scratchpad):
    prompt = REACT_PROMPT.format(question=question, scratchpad=scratchpad)
    response = model.generate(prompt)
    
    # Parse thought and action from response
    thought = extract_thought(response)
    action, arg = extract_action(response)
    
    # Execute action
    if action == "Search":
        observation = wikipedia_search(arg)
    elif action == "Lookup":
        observation = lookup_in_context(arg)
    elif action == "Finish":
        return {"answer": arg, "done": True}
    
    # Update scratchpad
    new_scratchpad = f"{scratchpad}\n{thought}\n{action}\nObservation: {observation}"
    
    return {"scratchpad": new_scratchpad, "done": False}
```

### With LangChain

```python
from langchain.agents import initialize_agent, Tool
from langchain_openai import ChatOpenAI

tools = [
    Tool(name="Search", func=search_wikipedia, description="..."),
    Tool(name="Lookup", func=lookup, description="..."),
]

agent = initialize_agent(
    tools,
    ChatOpenAI(model="gpt-4"),
    agent="react-docstore",
    verbose=True
)

result = agent.run("What is the elevation of Bell's birthplace?")
```

## 📊 Key Results

### Question Answering (HotpotQA)

| Method | Exact Match |
|--------|-------------|
| Standard Prompting | 25.7% |
| Chain-of-Thought | 29.4% |
| Act-only | 25.7% |
| **ReAct** | **35.1%** |

### Decision Making (ALFWorld)

| Method | Success Rate |
|--------|--------------|
| Imitation Learning | 37% |
| Act-only | 45% |
| **ReAct** | **71%** |

## 🔑 Key Insights

### 1. Reasoning Helps Action Selection
Without explicit reasoning, models often take random or suboptimal actions. Thinking first improves action quality.

### 2. Actions Ground Reasoning
Pure chain-of-thought can hallucinate facts. Actions retrieve real information, keeping reasoning grounded.

### 3. Interpretability
The explicit thought-action trace makes agent behavior transparent and debuggable.

### 4. Error Recovery
When actions return unexpected results, the model can reason about what went wrong and try alternative approaches.

## 💡 When to Use ReAct

✅ **Good for:**
- Multi-step question answering
- Tasks requiring external knowledge retrieval
- Interactive decision-making
- Scenarios needing explainability

❌ **Not ideal for:**
- Simple, single-step tasks
- Pure generation tasks (no actions needed)
- Real-time systems (thought generation adds latency)
- Tasks with very limited action space

## 📈 Variations & Extensions

### ReAct + Self-Consistency
Run ReAct multiple times, aggregate answers.

### ReWOO (Reasoning Without Observation)
Plan all reasoning first, then execute actions — more efficient but less adaptive.

### Reflexion
Add reflection after task completion to improve future attempts.

### Tree of Thoughts + ReAct
Explore multiple reasoning paths with backtracking.

## ⚠️ Common Issues

### 1. Thought-Action Mismatch
Model says it will do X but does Y.

**Fix**: Stricter output format, parsing validation.

### 2. Infinite Loops
Model repeats the same thought-action pattern.

**Fix**: Add loop detection, maximum steps, diversity penalties.

### 3. Hallucinated Actions
Model invents actions that don't exist.

**Fix**: Clear action descriptions in prompt, constrained decoding.

## 📄 Citation

```bibtex
@inproceedings{yao2023react,
  title={ReAct: Synergizing Reasoning and Acting in Language Models},
  author={Yao, Shunyu and Zhao, Jeffrey and Yu, Dian and Du, Nan and Shafran, Izhak and Narasimhan, Karthik and Cao, Yuan},
  booktitle={International Conference on Learning Representations},
  year={2023}
}
```

## 🔗 Resources

- **Paper**: [arXiv:2210.03629](https://arxiv.org/abs/2210.03629)
- **Project Page**: [react-lm.github.io](https://react-lm.github.io)
- **LangChain Agents**: [docs.langchain.com/docs/modules/agents](https://docs.langchain.com/docs/modules/agents)

## 📚 Related Papers

- [Chain-of-Thought](chain-of-thought.md) - Reasoning-only approach
- [Toolformer](toolformer.md) - Self-taught tool use
- [Reflexion](reflexion.md) - Learning from mistakes
- [Tree of Thoughts](tree-of-thoughts.md) - Branching reasoning

---

*Added: February 2026*
