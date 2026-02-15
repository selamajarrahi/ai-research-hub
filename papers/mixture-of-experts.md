# Mixture of Experts: Scaling with Sparse Activation

> **Key Papers**: Shazeer et al. 2017, Fedus et al. 2022 (Switch), DeepSeek-MoE  
> **Difficulty**: ⭐⭐⭐ Advanced

## 🎯 Key Innovation

**Scale parameters without scaling compute.** Only a subset of "experts" activates for each token, giving the capacity of a huge model with the cost of a smaller one.

## 📋 Summary

Traditional dense models use all parameters for every input. MoE models route each token to a subset of "expert" networks, typically MLPs.

### The Basic Idea

```
Dense Transformer:
  Every token → ALL parameters
  1T params = 1T FLOPs per token

MoE Transformer:
  Every token → Router → Top-K Experts only
  1T params = 100B FLOPs per token (if K=2, 8 experts)
```

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  Standard MoE Layer                                         │
│  ───────────────────                                        │
│                                                              │
│  Input Token                                                 │
│       │                                                      │
│       ▼                                                      │
│  ┌─────────┐                                                │
│  │ Router  │ → Compute scores for each expert               │
│  └─────────┘                                                │
│       │                                                      │
│       ▼ Select Top-K                                        │
│  ┌─────────────────────────────────────────┐                │
│  │  Expert 1  │  Expert 2  │ ... │ Expert N │               │
│  │   (MLP)    │   (MLP)    │     │  (MLP)   │               │
│  └─────────────────────────────────────────┘                │
│       │           │                   │                      │
│       ▼ Weighted Sum                                        │
│  Output Token                                                │
└─────────────────────────────────────────────────────────────┘
```

## 🔧 Key Components

### Router

```python
class Router(nn.Module):
    def __init__(self, hidden_dim, num_experts, top_k=2):
        self.gate = nn.Linear(hidden_dim, num_experts)
        self.top_k = top_k
    
    def forward(self, x):
        # Compute expert scores
        scores = self.gate(x)  # [batch, seq, num_experts]
        
        # Top-K selection
        top_k_scores, top_k_indices = torch.topk(scores, self.top_k, dim=-1)
        top_k_weights = F.softmax(top_k_scores, dim=-1)
        
        return top_k_indices, top_k_weights
```

### Load Balancing

Without balancing, some experts get all the tokens ("rich get richer"). Solutions:

```python
# Auxiliary load balancing loss
def load_balance_loss(router_probs, expert_mask):
    """Encourage uniform expert usage."""
    # router_probs: probability assigned to each expert
    # expert_mask: which expert was actually selected
    
    router_prob_per_expert = router_probs.mean(dim=0)  # avg prob per expert
    tokens_per_expert = expert_mask.float().mean(dim=0)  # actual usage
    
    # Minimize mismatch
    return (router_prob_per_expert * tokens_per_expert).sum() * num_experts
```

## 📊 Scaling Properties

### Efficiency

| Model | Params | Active Params | Relative Cost |
|-------|--------|---------------|---------------|
| Dense 70B | 70B | 70B | 100% |
| MoE 400B (8 experts, top-2) | 400B | 52B | 75% |
| MoE 1T (64 experts, top-8) | 1T | 125B | ~180% |

### Quality vs Dense

At equivalent FLOPs:
- MoE typically matches dense model with **2-4x fewer FLOPs**
- Or: Given same FLOPs, MoE achieves **lower loss**

## 💡 Key Variants

### Switch Transformer (Google)
- **Top-1 routing**: Only one expert per token
- Simpler, faster, but potentially less expressive

### GShard
- **Top-2 routing**: Balance between expressiveness and efficiency
- Better load balancing algorithms

### DeepSeek-MoE
- **Fine-grained experts**: More, smaller experts
- **Shared experts**: Some experts always active
- Better quality at same cost

### Mixtral
- **Practical MoE**: 8 experts, top-2
- First widely-adopted open-source MoE

## ⚠️ Challenges

### 1. Training Instability
Router can collapse to always selecting same experts.

**Solutions**: Aux loss, noise injection, capacity factors

### 2. Inference Complexity
Need to efficiently handle variable expert activation.

**Solutions**: Expert parallelism, load-aware routing

### 3. Memory
All experts need to be in memory even if only few used.

**Solutions**: Expert offloading, quantization

## 🛠️ Implementation

### Basic MoE Layer

```python
class MoELayer(nn.Module):
    def __init__(self, hidden_dim, num_experts=8, top_k=2):
        self.num_experts = num_experts
        self.top_k = top_k
        
        # Create experts
        self.experts = nn.ModuleList([
            nn.Sequential(
                nn.Linear(hidden_dim, hidden_dim * 4),
                nn.GELU(),
                nn.Linear(hidden_dim * 4, hidden_dim)
            )
            for _ in range(num_experts)
        ])
        
        # Router
        self.router = nn.Linear(hidden_dim, num_experts)
    
    def forward(self, x):
        batch, seq, hidden = x.shape
        x_flat = x.view(-1, hidden)
        
        # Route
        router_logits = self.router(x_flat)
        top_k_logits, top_k_indices = torch.topk(router_logits, self.top_k, dim=-1)
        top_k_weights = F.softmax(top_k_logits, dim=-1)
        
        # Compute expert outputs
        output = torch.zeros_like(x_flat)
        for i in range(self.num_experts):
            # Find tokens routed to this expert
            mask = (top_k_indices == i).any(dim=-1)
            if mask.any():
                expert_input = x_flat[mask]
                expert_output = self.experts[i](expert_input)
                
                # Weight by routing probability
                weight_mask = (top_k_indices[mask] == i)
                weights = top_k_weights[mask][weight_mask]
                output[mask] += expert_output * weights.unsqueeze(-1)
        
        return output.view(batch, seq, hidden)
```

## 📄 Citations

```bibtex
@article{shazeer2017outrageously,
  title={Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer},
  author={Shazeer, Noam and others},
  journal={arXiv:1701.06538},
  year={2017}
}

@article{fedus2022switch,
  title={Switch Transformers: Scaling to Trillion Parameter Models},
  author={Fedus, William and Zoph, Barret and Shazeer, Noam},
  journal={JMLR},
  year={2022}
}
```

## 🔗 Related

- [Mixtral](../tools/mixtral.md) - Open-source MoE
- [DeepSeek-MoE](deepseek-moe.md) - Fine-grained MoE
- [Flash Attention](flash-attention-3.md) - Efficient attention for all models

---

*Added: February 2026*
