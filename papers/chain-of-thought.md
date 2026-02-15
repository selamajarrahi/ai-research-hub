# Chain-of-Thought Prompting

> **arXiv**: 2201.11903  
> **Authors**: Jason Wei, Xuezhi Wang, Dale Schuurmans, et al.  
> **Organization**: Google  
> **Difficulty**: ⭐⭐ Intermediate

## 🎯 Key Innovation

**Show the model how to think step-by-step.** By including reasoning steps in few-shot examples, models dramatically improve on complex reasoning tasks.

## 📋 Summary

Standard few-shot prompting shows input-output pairs. Chain-of-thought (CoT) prompting shows input → reasoning → output.

### The Difference

```
Standard Prompting:
Q: Roger has 5 tennis balls. He buys 2 cans with 3 balls each. How many total?
A: 11

Chain-of-Thought Prompting:
Q: Roger has 5 tennis balls. He buys 2 cans with 3 balls each. How many total?
A: Roger starts with 5 balls. He buys 2 cans × 3 balls = 6 balls. 
   Total: 5 + 6 = 11
```

## 📊 Key Results

### GSM8K (Grade School Math)

| Method | Accuracy |
|--------|----------|
| Standard prompting (PaLM 540B) | 18% |
| **Chain-of-thought (PaLM 540B)** | **58%** |
| Fine-tuned verifier (PaLM 540B) | 55% |

### Emergence with Scale

CoT benefits appear primarily at scale:

| Model Size | Standard | Chain-of-Thought |
|------------|----------|------------------|
| 8B | 4% | 5% |
| 62B | 12% | 33% |
| 540B | 18% | 58% |

## 🔧 Implementation

### Manual CoT (Few-shot)

```python
MATH_COT_PROMPT = """
Q: There are 15 trees in the grove. Grove workers will plant trees today. 
After they are done, there will be 21 trees. How many trees did workers plant?

A: Let me solve this step by step.
1. Start with 15 trees
2. End with 21 trees
3. Trees planted = 21 - 15 = 6
The answer is 6.

Q: If there are 3 cars in the parking lot and 2 more cars arrive, 
how many cars are in the parking lot?

A: Let me solve this step by step.
1. Start with 3 cars
2. 2 more cars arrive
3. Total = 3 + 2 = 5
The answer is 5.

Q: {question}

A: Let me solve this step by step.
"""

response = model.generate(MATH_COT_PROMPT.format(question=user_question))
```

### Zero-Shot CoT

Simply add "Let's think step by step" to the prompt:

```python
ZERO_SHOT_COT = """
Q: {question}

A: Let's think step by step.
"""
```

This single phrase unlocks reasoning capabilities without any examples!

### Self-Consistency

Generate multiple reasoning paths, take majority vote:

```python
def self_consistency_cot(model, question, n_samples=5):
    answers = []
    for _ in range(n_samples):
        response = model.generate(
            COT_PROMPT.format(question=question),
            temperature=0.7  # Enable diversity
        )
        answer = extract_final_answer(response)
        answers.append(answer)
    
    # Majority vote
    return Counter(answers).most_common(1)[0][0]
```

## 💡 Key Insights

### 1. Reasoning is Learnable
Models can learn to reason by example, not just by fine-tuning.

### 2. Explicit > Implicit
Making reasoning steps explicit improves both accuracy and interpretability.

### 3. Scale Unlocks Capabilities
CoT benefits emerge primarily in large models (>50B parameters).

### 4. Task-Dependent
CoT helps most on multi-step reasoning tasks (math, logic, common sense).

## 📈 Variations

### Tree of Thoughts (ToT)
Explore multiple reasoning branches, backtrack if needed.

### Graph of Thoughts
Allow non-linear reasoning structures.

### Least-to-Most
Decompose complex problems into simpler subproblems.

### Program-Aided CoT
Generate code to verify reasoning steps.

## ⚠️ Limitations

1. **Faithful reasoning?** Models might produce plausible-sounding but wrong reasoning
2. **Token cost**: Longer outputs = more expensive
3. **Not universal**: Simple tasks don't benefit from CoT
4. **Scale dependent**: Small models don't benefit much

## 🛠️ Best Practices

### When to Use CoT

✅ Multi-step math problems
✅ Logical reasoning
✅ Common sense reasoning
✅ Complex decision making
✅ Any task requiring "thinking"

### When to Skip CoT

❌ Simple factual questions
❌ Classification tasks
❌ Very small models
❌ Latency-critical applications

### Prompt Design Tips

```
1. Be explicit: "Let me solve this step by step"
2. Number steps: "1. First... 2. Then... 3. Therefore..."
3. Show your work: Include intermediate calculations
4. State the answer clearly: "The answer is X"
5. Match task format: Math CoT differs from logic CoT
```

## 📄 Citation

```bibtex
@article{wei2022chain,
  title={Chain-of-Thought Prompting Elicits Reasoning in Large Language Models},
  author={Wei, Jason and Wang, Xuezhi and Schuurmans, Dale and others},
  journal={NeurIPS},
  year={2022}
}
```

## 🔗 Resources

- **Paper**: [arXiv:2201.11903](https://arxiv.org/abs/2201.11903)
- **Follow-up**: [Self-Consistency (arXiv:2203.11171)](https://arxiv.org/abs/2203.11171)
- **Zero-Shot CoT**: [arXiv:2205.11916](https://arxiv.org/abs/2205.11916)

## 📚 Related

- [ReAct](react.md) - CoT + actions
- [Tree of Thoughts](tree-of-thoughts.md) - Branching CoT
- [Constitutional AI](constitutional-ai.md) - CoT for safety

---

*Added: February 2026*
