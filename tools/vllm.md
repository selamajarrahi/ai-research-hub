# vLLM

> **Category**: LLM Inference Engine  
> **Website**: [vllm.ai](https://vllm.ai)  
> **GitHub**: [vllm-project/vllm](https://github.com/vllm-project/vllm)  
> **Status**: Production | Industry Standard

## 🎯 What is vLLM?

vLLM is a high-throughput, memory-efficient inference engine for large language models. It's become the de facto standard for serving LLMs in production due to its PagedAttention innovation and continuous batching.

## 🧠 Core Innovation: PagedAttention

The key insight: **KV cache memory fragmentation is the bottleneck.**

```
┌─────────────────────────────────────────────────────────────┐
│  Traditional KV Cache (per-request contiguous allocation)  │
│  ─────────────────────────────────────────────────────────  │
│  [Request 1 ████████████████        ]                      │
│  [Request 2 ██████████████████████  ]                      │
│  [Request 3 ████████                ]                      │
│  [  Empty  ░░░░░░░░░░░░░░░░░░░░░░░░] ← Wasted             │
│                                                             │
│  Problem: Pre-allocate for max_length, waste memory        │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  PagedAttention (virtual memory for KV cache)              │
│  ─────────────────────────────────────────────             │
│  Logical blocks → Physical pages                           │
│  [R1:0][R2:0][R3:0][R1:1][R2:1][R1:2][R2:2][R3:1]...      │
│                                                             │
│  Benefit: Near-zero fragmentation, >10x more concurrent    │
└─────────────────────────────────────────────────────────────┘
```

## 🔑 Key Features

### 1. PagedAttention
- Virtual memory for KV cache
- Near-zero fragmentation
- Efficient memory sharing (prefix caching)

### 2. Continuous Batching
- Dynamic request insertion
- No waiting for batch completion
- Maximizes GPU utilization

### 3. Quantization Support
- AWQ, GPTQ, FP8, GGUF
- Automatic mixed precision
- Speculative decoding support

### 4. Distributed Inference
- Tensor parallelism
- Pipeline parallelism
- Multi-GPU serving

## 💻 Quick Start

### Installation

```bash
pip install vllm

# With FlashAttention (recommended)
pip install vllm[flash-attn]
```

### Basic Usage

```python
from vllm import LLM, SamplingParams

# Load model
llm = LLM(model="meta-llama/Llama-3.1-70B-Instruct")

# Generate
prompts = ["The capital of France is", "def fibonacci(n):"]
sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=100)

outputs = llm.generate(prompts, sampling_params)
for output in outputs:
    print(output.outputs[0].text)
```

### OpenAI-Compatible Server

```bash
# Start server
python -m vllm.entrypoints.openai.api_server \
    --model meta-llama/Llama-3.1-70B-Instruct \
    --tensor-parallel-size 4

# Use with OpenAI client
from openai import OpenAI
client = OpenAI(base_url="http://localhost:8000/v1", api_key="none")

response = client.chat.completions.create(
    model="meta-llama/Llama-3.1-70B-Instruct",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## 📊 Performance

### Throughput (Llama-70B on 4x A100)

| Method | Requests/sec | Latency P50 |
|--------|--------------|-------------|
| Naive batching | 2.1 | 5.2s |
| Hugging Face + Flash | 8.3 | 1.8s |
| vLLM | 24.6 | 0.6s |

### Memory Efficiency

| Method | Max Concurrent (70B, 80GB GPU) |
|--------|-------------------------------|
| Naive | 4 |
| vLLM | 48 |

## 🔧 Configuration

### Common Options

```python
llm = LLM(
    model="meta-llama/Llama-3.1-70B-Instruct",
    tensor_parallel_size=4,          # Multi-GPU
    dtype="auto",                     # auto, float16, bfloat16, float32
    quantization="awq",               # awq, gptq, fp8, None
    max_model_len=32768,              # Maximum sequence length
    gpu_memory_utilization=0.9,       # How much VRAM to use
    enable_prefix_caching=True,       # Cache common prefixes
    enable_chunked_prefill=True,      # Better long-context handling
)
```

### Server Configuration

```yaml
# config.yaml for production
model: meta-llama/Llama-3.1-70B-Instruct
tensor_parallel_size: 4
max_model_len: 32768
gpu_memory_utilization: 0.9
enable_prefix_caching: true

# Rate limiting
max_num_seqs: 256
max_num_batched_tokens: 32768

# Logging
disable_log_requests: false
```

## 🔌 Integrations

| Tool | Integration Type | Status |
|------|------------------|--------|
| LangChain | LLM provider | ✅ |
| LlamaIndex | LLM provider | ✅ |
| Ray Serve | Deployment | ✅ |
| Kubernetes | Deployment | ✅ |
| Prometheus | Monitoring | ✅ |
| Grafana | Dashboards | ✅ |

## 📈 Production Deployment

### Docker

```dockerfile
FROM vllm/vllm-openai:latest

# Pre-download model
RUN python -c "from vllm import LLM; LLM('meta-llama/Llama-3.1-8B-Instruct')"

CMD ["python", "-m", "vllm.entrypoints.openai.api_server", \
     "--model", "meta-llama/Llama-3.1-8B-Instruct"]
```

### Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vllm-server
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: vllm
        image: vllm/vllm-openai:latest
        resources:
          limits:
            nvidia.com/gpu: 4
        args:
        - --model=meta-llama/Llama-3.1-70B-Instruct
        - --tensor-parallel-size=4
        ports:
        - containerPort: 8000
```

## ⚠️ Considerations

### When to Use vLLM
- ✅ High-throughput serving
- ✅ Many concurrent users
- ✅ Standard LLM architectures
- ✅ Production deployments

### When to Consider Alternatives
- ❌ Edge/mobile deployment → Use llama.cpp
- ❌ Custom attention patterns → May need modifications
- ❌ Research/prototyping → Hugging Face Transformers simpler
- ❌ Very small models → Overhead may not be worth it

## 🔗 Resources

- **Documentation**: [docs.vllm.ai](https://docs.vllm.ai)
- **GitHub**: [vllm-project/vllm](https://github.com/vllm-project/vllm)
- **Discord**: [vLLM Discord](https://discord.gg/vllm)
- **Paper**: [arXiv:2309.06180](https://arxiv.org/abs/2309.06180)

## 📚 Related Tools

- [SGLang](sglang.md) - Structured generation focus
- [TensorRT-LLM](tensorrt-llm.md) - NVIDIA's solution
- [Ollama](ollama.md) - Local deployment

---

*Added: February 2026*
