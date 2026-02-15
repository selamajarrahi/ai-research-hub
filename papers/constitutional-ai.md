# Constitutional AI: Harmlessness from AI Feedback

> **arXiv**: 2212.08073  
> **Authors**: Yuntao Bai, Saurav Kadavath, Sandipan Kundu, et al.  
> **Organization**: Anthropic  
> **Difficulty**: ⭐⭐⭐ Advanced

## 🎯 Key Innovation

**Self-supervised preference learning using a constitution.** Instead of relying entirely on human feedback, the model critiques and revises its own outputs based on a set of principles (the "constitution").

## 📋 Summary

Traditional RLHF requires extensive human labeling to teach models what's harmful. Constitutional AI (CAI) reduces this dependency by:

1. Having the model generate responses
2. Asking the model to critique those responses against principles
3. Having the model revise based on critiques
4. Using the critiques to train a preference model

### The CAI Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│  Phase 1: Supervised Learning (SL-CAI)                     │
│  ────────────────────────────────────                       │
│  1. Model generates response to prompt                      │
│  2. Model critiques response using constitution             │
│  3. Model revises response based on critique                │
│  4. Fine-tune on (prompt, revised_response) pairs          │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  Phase 2: RL from AI Feedback (RLAIF)                      │
│  ────────────────────────────────────                       │
│  1. Generate pairs of responses                             │
│  2. AI ranks responses using constitution                   │
│  3. Train preference model on AI rankings                   │
│  4. Use preference model for RL (like RLHF)                │
└─────────────────────────────────────────────────────────────┘
```

## 📜 The Constitution

A set of principles that guide the model's behavior:

### Example Principles

```
1. Please choose the response that is the most helpful, 
   honest, and harmless.

2. Please choose the response that is least likely to 
   be used for harmful purposes.

3. Choose the response that a wise, ethical AI assistant 
   would give.

4. Which response would be more suitable for a family-
   friendly audience?

5. Choose the response that demonstrates more thoughtful
   consideration of the consequences.
```

### Applying Principles

```
Prompt: How do I pick a lock?

Initial Response: Here's how to pick a lock: [detailed instructions]

Critique (using principle 2): This response provides detailed 
instructions that could facilitate illegal entry. A more helpful 
response would acknowledge the question while redirecting toward 
legitimate uses.

Revised Response: Lock picking is a skill used by locksmiths and 
security professionals. If you're locked out, I'd recommend 
contacting a licensed locksmith. If you're interested in lock 
sport (recreational lock picking), I can suggest beginner 
resources and emphasize the importance of only practicing on 
locks you own.
```

## 🔧 Implementation Details

### SL-CAI Training

```python
def sl_cai_revision(model, prompt, constitution):
    """Generate revised response using constitutional critique."""
    
    # Generate initial response
    initial = model.generate(prompt)
    
    # Critique using random principle from constitution
    principle = random.choice(constitution)
    critique_prompt = f"""
    Human: {prompt}
    
    Assistant: {initial}
    
    Critique Request: Identify specific ways in which this 
    response fails to meet the following principle:
    
    Principle: {principle}
    
    Critique:"""
    
    critique = model.generate(critique_prompt)
    
    # Generate revision
    revision_prompt = f"""
    {critique_prompt}
    {critique}
    
    Based on the above critique, please rewrite the response 
    to better satisfy the principle:
    
    Revised Response:"""
    
    revised = model.generate(revision_prompt)
    
    return revised
```

### RLAIF Preference Model

```python
def generate_preference_pair(model, prompt, constitution):
    """Generate preference pair for RLAIF training."""
    
    # Generate two responses
    response_a = model.generate(prompt)
    response_b = model.generate(prompt)
    
    # Ask model to rank using constitution
    principle = random.choice(constitution)
    ranking_prompt = f"""
    Consider the principle: {principle}
    
    Response A: {response_a}
    Response B: {response_b}
    
    Which response better satisfies the principle? Answer A or B.
    """
    
    ranking = model.generate(ranking_prompt)
    
    # Parse preference
    preferred = response_a if "A" in ranking else response_b
    rejected = response_b if "A" in ranking else response_a
    
    return (prompt, preferred, rejected)
```

## 📊 Key Results

### Harmlessness Evaluation

| Model | Harmlessness Score | Helpfulness |
|-------|-------------------|-------------|
| Base (no RLHF) | 32% | 74% |
| RLHF | 78% | 72% |
| **CAI** | **84%** | **73%** |

CAI achieves better harmlessness while maintaining helpfulness.

### Human Labor Reduction

| Training Method | Human Labels Required |
|-----------------|----------------------|
| Pure RLHF | ~100,000 |
| CAI + minimal RLHF | ~10,000 |
| CAI (RLAIF only) | 0 (plus constitution design) |

## 🔑 Key Insights

### 1. Principles > Examples
A small set of well-crafted principles can outperform large labeled datasets.

### 2. Self-Improvement is Possible
Models can meaningfully critique and improve their own outputs.

### 3. Transparency
The constitution makes the training values explicit and auditable.

### 4. Scaling Supervision
AI feedback can scale to cover more scenarios than human labeling.

## 💡 Implications

### For Alignment Research
- Constitutional approach provides interpretable alignment
- Principles can be updated without retraining from scratch
- Enables research on value specification

### For Deployment
- Reduced labeling costs
- More consistent enforcement of values
- Auditable decision-making

### For Safety
- Explicit articulation of principles
- Easier to identify and fix problematic behaviors
- Constitution can include diverse perspectives

## ⚠️ Limitations

1. **Constitution design**: Requires careful crafting of principles
2. **Model capability**: Works best with capable base models
3. **Edge cases**: May not handle novel situations not covered by principles
4. **Gaming**: Model might learn to superficially satisfy principles

## 📈 Extensions

### Chain-of-Thought Constitutional
Adding reasoning steps to the critique process.

### Multi-Stakeholder Constitutions
Different principles for different contexts/users.

### Adaptive Constitutions
Updating principles based on deployment feedback.

## 📄 Citation

```bibtex
@article{bai2022constitutional,
  title={Constitutional AI: Harmlessness from AI Feedback},
  author={Bai, Yuntao and Kadavath, Saurav and Kundu, Sandipan and others},
  journal={arXiv preprint arXiv:2212.08073},
  year={2022}
}
```

## 🔗 Resources

- **Paper**: [arXiv:2212.08073](https://arxiv.org/abs/2212.08073)
- **Anthropic Blog**: [anthropic.com/constitutional-ai](https://anthropic.com/research/constitutional-ai)
- **Claude Model Card**: [anthropic.com/claude](https://anthropic.com/claude)

## 📚 Related Papers

- [RLHF](rlhf.md) - Original reinforcement learning from human feedback
- [InstructGPT](instructgpt.md) - OpenAI's instruction-following approach
- [Training-Free GRPO](training-free-grpo.md) - RL without fine-tuning

---

*Added: February 2026*
