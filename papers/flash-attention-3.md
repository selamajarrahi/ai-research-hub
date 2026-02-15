# Flash Attention 3

> **arXiv**: 2407.08608  
> **Authors**: Tri Dao, Jay Shah, et al.  
> **Organization**: Princeton, Together AI  
> **Difficulty**: ⭐⭐⭐⭐ Expert

## 🎯 Key Innovation

**Asymptotically optimal attention on Hopper GPUs.** Flash Attention 3 achieves near-peak GPU utilization by exploiting H100's unique features: TMA, WGMMA, and asynchronous execution.

## 📋 Summary

Flash Attention revolutionized transformer inference by being **IO-aware** — minimizing memory reads/writes rather than just FLOPs. FA3 continues this philosophy while adapting to the new H100/Hopper architecture.

### The Evolution

```
┌─────────────────────────────────────────────────────────────┐
│  Standard Attention                                         │
│  ─────────────────────────                                  │
│  Memory bound: O(N²) reads/writes                           │
│  GPU utilization: 20-40%                                    │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  Flash Attention 1 & 2 (Ampere/A100)                       │
│  ─────────────────────────────────────                      │
│  IO-aware tiling, single SRAM read                         │
│  GPU utilization: 60-70%                                    │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  Flash Attention 3 (Hopper/H100)                           │
│  ─────────────────────────────────                         │
│  TMA + WGMMA + Async execution                             │
│  GPU utilization: 75-85%                                    │
└─────────────────────────────────────────────────────────────┘
```

## 🔧 Technical Details

### Key H100 Features Exploited

1. **TMA (Tensor Memory Accelerator)**
   - Hardware-accelerated memory copies
   - Asynchronous with computation
   - Reduces software overhead

2. **WGMMA (Warp Group Matrix Multiply)**
   - Persistent threads across warps
   - Better scheduling flexibility
   - Higher tensor core utilization

3. **FP8 Support**
   - Lower precision, 2x throughput
   - Careful accumulation strategies
   - Quality-preserving techniques

### Asynchronous Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│  Traditional Pipeline                                       │
│  ─────────────────────────                                  │
│  Load → Compute → Store → Load → Compute → Store           │
│       ↑_________gap________↑                                │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  FA3 Async Pipeline                                         │
│  ─────────────────────                                      │
│  Load₁ → Compute₁ → Store₁                                  │
│         Load₂ → Compute₂ → Store₂                           │
│                Load₃ → Compute₃ → Store₃                    │
│  [Overlapped execution - no gaps]                           │
└─────────────────────────────────────────────────────────────┘
```

## 📊 Performance Results

### Speed (H100 SXM5)

| Sequence Length | FA2 | FA3 | Speedup |
|-----------------|-----|-----|---------|
| 1K | 410 TFLOPs | 600 TFLOPs | 1.46x |
| 4K | 450 TFLOPs | 640 TFLOPs | 1.42x |
| 16K | 480 TFLOPs | 690 TFLOPs | 1.44x |

### Peak Utilization

| Method | FP16 % Peak | FP8 % Peak |
|--------|-------------|------------|
| Standard | 35% | N/A |
| FA2 | 62% | N/A |
| FA3 | 75% | 82% |

## 💡 Key Insights

1. **Architecture-specific optimization matters**: FA3 is designed specifically for Hopper. Different GPUs need different approaches.

2. **Async everything**: The key to high utilization is keeping all parts of the GPU busy simultaneously.

3. **FP8 requires care**: Lower precision can maintain quality with proper accumulation strategies.

4. **Memory hierarchy mastery**: Understanding SRAM, L2, HBM interactions is crucial.

## 🛠️ Implementation

### Using Flash Attention 3

```python
# pip install flash-attn>=2.6.0
from flash_attn import flash_attn_func

# Standard usage
output = flash_attn_func(q, k, v, causal=True)

# With FP8 (H100 only)
output = flash_attn_func(q, k, v, causal=True, dtype=torch.float8_e4m3fn)
```

### Framework Support

| Framework | Version | Status |
|-----------|---------|--------|
| PyTorch | 2.4+ | ✅ Native |
| JAX | 0.4.30+ | ✅ Via Pallas |
| TensorFlow | 2.18+ | ✅ XLA |
| vLLM | 0.6+ | ✅ Default |
| TensorRT-LLM | 0.12+ | ✅ Default |

## ⚠️ Requirements & Limitations

### Hardware Requirements
- NVIDIA Hopper (H100, H200)
- CUDA 12.0+
- At least 80GB HBM for large models

### Limitations
- **Hopper only**: Falls back to FA2 on older GPUs
- **Compilation time**: First run includes kernel compilation
- **Custom attention patterns**: Some patterns not yet supported

## 📄 Citation

```bibtex
@article{shah2024flashattention3,
  title={FlashAttention-3: Fast and Accurate Attention with Asynchrony and Low-precision},
  author={Shah, Jay and Bikshandi, Ganesh and Zhang, Ying and Thakkar, Vijay and Ramani, Pradeep and Dao, Tri},
  journal={arXiv preprint arXiv:2407.08608},
  year={2024}
}
```

## 🔗 Resources

- **Paper**: [arXiv:2407.08608](https://arxiv.org/abs/2407.08608)
- **Code**: [github.com/Dao-AILab/flash-attention](https://github.com/Dao-AILab/flash-attention)
- **PyPI**: `pip install flash-attn`
- **Blog**: [Princeton CS Blog](https://www.cs.princeton.edu/~danqic/blog/)

## 📚 Related Papers

- [Flash Attention 1](flash-attention-1.md) - Original paper
- [Flash Attention 2](flash-attention-2.md) - Better parallelism
- [Ring Attention](ring-attention.md) - Context length scaling
- [Paged Attention](paged-attention.md) - Memory-efficient KV cache

---

*Added: February 2026*
