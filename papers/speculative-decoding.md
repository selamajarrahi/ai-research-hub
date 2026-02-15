# Speculative Decoding

> **arXiv**: 2211.17192 (original), multiple follow-ups  
> **Authors**: Various (DeepMind, Meta, Google)  
> **Difficulty**: ⭐⭐⭐ Advanced

## 🎯 Key Innovation

**Use a small "draft" model to propose tokens, verify with the large "target" model.** This achieves 2-3x speedup with mathematically guaranteed identical outputs.

## 📋 Summary

Traditional autoregressive generation is slow because:
1. Each token requires a full forward pass
2. Large models have high per-token latency
3. Cannot parallelize across sequence positions

Speculative decoding parallelizes by having a small, fast model "speculate" multiple tokens ahead, then verifying them in one batch with the large model.

### The Key Insight

```
Traditional (sequential):
Token 1 → Token 2 → Token 3 → Token 4 → Token 5
  100ms    100ms     100ms     100ms     100ms = 500ms

Speculative (parallel verification):
Draft:  Token 1,2,3,4,5 (small model, fast)    = 50ms
Verify: Check all 5 at once (large model)      = 150ms
Accept: 3 tokens verified ✓                     = 200ms total
```

## 🔧 How It Works

### Step-by-Step Algorithm

```python
def speculative_decode(target_model, draft_model, prompt, k=5):
    """
    k: number of tokens to speculate ahead
    """
    tokens = prompt
    
    while not done:
        # 1. Draft: Generate k tokens with small model
        draft_tokens = []
        draft_probs = []
        for _ in range(k):
            logits = draft_model(tokens + draft_tokens)
            token = sample(logits)
            draft_tokens.append(token)
            draft_probs.append(softmax(logits))
        
        # 2. Verify: Run large model on all positions at once
        all_positions = tokens + draft_tokens
        target_logits = target_model(all_positions)  # Single forward pass
        
        # 3. Accept/Reject: Compare distributions
        n_accepted = 0
        for i in range(k):
            target_prob = softmax(target_logits[i])
            if accept(draft_probs[i], target_prob, draft_tokens[i]):
                n_accepted += 1
            else:
                # Resample from adjusted distribution
                new_token = sample_adjusted(target_prob, draft_probs[i])
                draft_tokens[i] = new_token
                break
        
        # 4. Add accepted tokens
        tokens.extend(draft_tokens[:n_accepted + 1])
    
    return tokens
```

### Acceptance Criterion

For each speculated token, accept with probability:
```
min(1, p_target(x) / p_draft(x))
```

If rejected, sample from the "remainder" distribution:
```
p_adjusted(x) = max(0, p_target(x) - p_draft(x)) / Z
```

This guarantees the output distribution is **exactly** equal to the target model.

## 📊 Performance

### Speedup Results

| Task | Target Model | Draft Model | Speedup |
|------|--------------|-------------|---------|
| Code gen | Llama-70B | Llama-7B | 2.7x |
| Summarization | GPT-4 | GPT-3.5 | 2.3x |
| QA | Claude-Opus | Claude-Haiku | 2.5x |
| Translation | PaLM-540B | PaLM-8B | 3.1x |

### Acceptance Rate

Higher acceptance rate = better speedup:

| Draft/Target Pair | Acceptance Rate |
|-------------------|-----------------|
| Same family (Llama 7B/70B) | 70-85% |
| Different family | 50-70% |
| Distilled draft | 80-90% |

## 💻 Implementation

### With vLLM

```python
from vllm import LLM, SamplingParams

# vLLM handles speculative decoding automatically
llm = LLM(
    model="meta-llama/Llama-3.1-70B",
    speculative_model="meta-llama/Llama-3.1-8B",
    num_speculative_tokens=5
)

outputs = llm.generate(prompts, SamplingParams())
```

### With HuggingFace

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load models
target = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.1-70B")
draft = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.1-8B")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-70B")

# Generate with assisted decoding
inputs = tokenizer("Hello", return_tensors="pt")
outputs = target.generate(
    **inputs,
    assistant_model=draft,
    max_new_tokens=100
)
```

## 🔑 Key Factors for Success

### 1. Draft Model Quality
```
Better draft model → Higher acceptance rate → More speedup

Ideal draft model:
- Much faster than target (5-10x)
- Similar distribution (same training data)
- Same vocabulary/tokenizer
```

### 2. Speculation Length (k)
```
k too small: Not enough parallelism
k too large: Many rejections, wasted compute

Optimal k ≈ 3-7 for most setups
```

### 3. Temperature/Sampling
```
Low temperature (greedy): Very high acceptance rate
High temperature: Lower acceptance, still works
```

## 📈 Variations

### Medusa (Multi-Head Speculation)
Instead of a separate draft model, add small prediction heads to the target model itself.

```
Target Model
    └── Head 1: Predicts token t+1
    └── Head 2: Predicts token t+2
    └── Head 3: Predicts token t+3
```

### Lookahead Decoding
Use n-gram patterns from the generation history to speculate.

### Self-Speculative Decoding
Draft using early exit from the same model.

### Staged Speculative Decoding
Multiple draft models of increasing size:
```
Tiny draft → Small draft → Target verification
```

## ⚠️ Limitations

1. **Memory overhead**: Need to load both models
2. **Draft model dependency**: Need good draft model for each target
3. **Batch size trade-off**: Works best with small batches
4. **Training cost**: May need to train draft model

## 💡 When to Use

✅ **Good for:**
- Single-request latency optimization
- Interactive applications (chat, coding)
- Long generations
- Greedy/low-temperature sampling

❌ **Not ideal for:**
- High-throughput batch processing (continuous batching better)
- Very high temperature sampling
- When memory is constrained
- When no good draft model exists

## 📄 Citations

```bibtex
@article{leviathan2023spec,
  title={Fast Inference from Transformers via Speculative Decoding},
  author={Leviathan, Yaniv and Kalman, Matan and Matias, Yossi},
  journal={ICML},
  year={2023}
}

@article{chen2023accelerating,
  title={Accelerating Large Language Model Decoding with Speculative Sampling},
  author={Chen, Charlie and Borgeaud, Sebastian and others},
  journal={arXiv:2302.01318},
  year={2023}
}
```

## 🔗 Resources

- **DeepMind Paper**: [arXiv:2211.17192](https://arxiv.org/abs/2211.17192)
- **Google Paper**: [arXiv:2302.01318](https://arxiv.org/abs/2302.01318)
- **vLLM Docs**: [docs.vllm.ai/en/latest/models/spec_decode.html](https://docs.vllm.ai/en/latest/models/spec_decode.html)
- **Medusa**: [github.com/FasterDecoding/Medusa](https://github.com/FasterDecoding/Medusa)

## 📚 Related Papers

- [Medusa](medusa.md) - Multi-head speculation
- [Flash Attention](flash-attention-3.md) - Complementary optimization
- [Paged Attention](paged-attention.md) - Memory efficiency

---

*Added: February 2026*
