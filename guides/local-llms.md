# Setting Up Local LLMs

> A practical guide to running language models on your own hardware.

---

## 🎯 Why Run Local?

| Benefit | Description |
|---------|-------------|
| **Privacy** | Data never leaves your machine |
| **Cost** | No per-token fees after setup |
| **Latency** | No network round-trips |
| **Offline** | Works without internet |
| **Customization** | Full control over the model |

---

## 🖥️ Hardware Requirements

### Minimum Specs by Model Size

| Model Size | VRAM Required | Consumer GPU | Notes |
|------------|---------------|--------------|-------|
| 1-3B | 4-6GB | GTX 1660, RTX 3060 | Fast, good for prototyping |
| 7B | 8-16GB | RTX 3080, 4070 | Balanced capability |
| 13B | 16-24GB | RTX 4080, 4090 | Strong performance |
| 30B | 24-48GB | RTX 4090, A6000 | Near-frontier quality |
| 70B | 40-80GB+ | A100, H100, or multi-GPU | Maximum capability |

### CPU-Only Options

| Model Size | RAM Required | Speed |
|------------|--------------|-------|
| 3B (Q4) | 4GB | ~10 tokens/sec |
| 7B (Q4) | 8GB | ~5 tokens/sec |
| 13B (Q4) | 16GB | ~2 tokens/sec |

---

## 🚀 Quick Start: Ollama

**Ollama** is the easiest way to get started.

### Installation

```bash
# macOS / Linux
curl -fsSL https://ollama.com/install.sh | sh

# Windows
# Download from https://ollama.com/download
```

### Usage

```bash
# Download and run a model
ollama run llama3.1

# Chat
>>> Hello! How are you?
I'm doing well, thank you! How can I help you today?

# List models
ollama list

# Pull specific model
ollama pull codellama:13b

# Run with custom settings
ollama run llama3.1 --verbose
```

### API Access

```python
import requests

response = requests.post(
    "http://localhost:11434/api/generate",
    json={
        "model": "llama3.1",
        "prompt": "Why is the sky blue?"
    }
)
print(response.json()["response"])
```

---

## 🔧 LM Studio (GUI)

**LM Studio** provides a user-friendly desktop app.

### Setup

1. Download from [lmstudio.ai](https://lmstudio.ai)
2. Install and launch
3. Browse models in the "Discover" tab
4. Download your chosen model
5. Load in the "Chat" tab

### Features

- Visual model browser
- One-click download
- Built-in chat interface
- OpenAI-compatible API server
- Parameter tuning UI

---

## ⚡ vLLM (Production)

For maximum performance and throughput.

### Installation

```bash
pip install vllm
```

### Usage

```python
from vllm import LLM, SamplingParams

# Load model
llm = LLM(model="meta-llama/Llama-3.1-8B-Instruct")

# Generate
sampling_params = SamplingParams(temperature=0.7, max_tokens=100)
outputs = llm.generate(["Hello, how are you?"], sampling_params)
print(outputs[0].outputs[0].text)
```

### OpenAI-Compatible Server

```bash
python -m vllm.entrypoints.openai.api_server \
    --model meta-llama/Llama-3.1-8B-Instruct

# Now use with OpenAI client
from openai import OpenAI
client = OpenAI(base_url="http://localhost:8000/v1", api_key="none")
```

---

## 🦙 llama.cpp (C++ Performance)

Best for running on limited hardware.

### Installation

```bash
# Clone and build
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make

# With CUDA support
make GGML_CUDA=1
```

### Download Models (GGUF format)

```bash
# Use Hugging Face Hub
pip install huggingface_hub
huggingface-cli download TheBloke/Llama-2-7B-GGUF \
    llama-2-7b.Q4_K_M.gguf --local-dir ./models
```

### Run

```bash
# Interactive chat
./llama-cli -m models/llama-2-7b.Q4_K_M.gguf \
    --interactive \
    --color \
    -c 4096

# Server mode
./llama-server -m models/llama-2-7b.Q4_K_M.gguf --port 8080
```

---

## 🐍 Python Integration Options

### transformers (Hugging Face)

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

# Load model
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B-Instruct",
    torch_dtype=torch.float16,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-8B-Instruct")

# Generate
inputs = tokenizer("Hello!", return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_new_tokens=50)
print(tokenizer.decode(outputs[0]))
```

### With Quantization

```python
from transformers import BitsAndBytesConfig

# 4-bit quantization
quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-70B-Instruct",
    quantization_config=quantization_config,
    device_map="auto"
)
```

---

## 📊 Model Recommendations

### By Use Case

| Use Case | Recommended Model | Size |
|----------|-------------------|------|
| General chat | Llama 3.1 8B Instruct | 8B |
| Coding | DeepSeek-Coder-V2 | 16B |
| Creative writing | Mistral 7B Instruct | 7B |
| RAG/embeddings | Qwen 2 7B | 7B |
| Maximum quality | Llama 3.1 70B Instruct | 70B |

### By Hardware

| GPU | Best Model |
|-----|------------|
| RTX 3060 (12GB) | Mistral 7B Q8 |
| RTX 3080 (10GB) | Llama 3.1 8B Q4 |
| RTX 4090 (24GB) | Llama 3.1 70B Q4 |
| CPU only | Phi-3 3.8B Q4 |

---

## 🔒 Privacy Considerations

### What Stays Local

- ✅ All inference computation
- ✅ Your prompts
- ✅ Generated responses
- ✅ Model weights (after download)

### What May Leave Your Machine

- ⚠️ Initial model download (from HuggingFace, etc.)
- ⚠️ Telemetry (disable in settings)
- ⚠️ Crash reports (if enabled)

### Fully Offline Setup

```bash
# Download model once
huggingface-cli download meta-llama/Llama-3.1-8B-Instruct \
    --local-dir ./models/llama-3.1-8b

# Then use offline
export HF_DATASETS_OFFLINE=1
export TRANSFORMERS_OFFLINE=1
```

---

## 🎛️ Performance Tuning

### Memory Optimization

```python
# Reduce memory with Flash Attention
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    torch_dtype=torch.float16,
    attn_implementation="flash_attention_2"
)

# Use KV cache efficiently
model.config.use_cache = True
```

### Speed Optimization

```bash
# For llama.cpp, use more threads
./llama-cli -m model.gguf -t 8

# Use GPU layers
./llama-cli -m model.gguf -ngl 35  # 35 layers on GPU
```

### Batch Processing

```python
# Process multiple prompts together
prompts = ["Question 1?", "Question 2?", "Question 3?"]
inputs = tokenizer(prompts, return_tensors="pt", padding=True)
outputs = model.generate(**inputs)
```

---

## 🔗 Resources

- **Ollama**: [ollama.com](https://ollama.com)
- **LM Studio**: [lmstudio.ai](https://lmstudio.ai)
- **vLLM**: [docs.vllm.ai](https://docs.vllm.ai)
- **llama.cpp**: [github.com/ggerganov/llama.cpp](https://github.com/ggerganov/llama.cpp)
- **Hugging Face Models**: [huggingface.co/models](https://huggingface.co/models)

---

*Added: February 2026*
