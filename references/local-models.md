# Local Models for OpenCode

Run LLMs locally with Ollama, LM Studio, or other local inference servers.

## Ollama Setup

### Installation

```bash
# macOS/Linux
curl -fsSL https://ollama.com/install.sh | sh

# Start Ollama server
ollama serve
```

### Pull Models

```bash
# Coding models
ollama pull qwen3-coder:latest
ollama pull deepseek-coder:33b
ollama pull codellama:latest

# General models
ollama pull llama3.2:latest
ollama pull gemma3:latest
ollama pull mistral:latest
```

### OpenCode Configuration

```json
{
  "$schema": "https://opencode.dev/schema/opencode.json",
  "model": "ollama/qwen3-coder:latest",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "qwen3-coder:latest": {
          "name": "Qwen3 Coder"
        },
        "deepseek-coder:33b": {
          "name": "Deepseek Coder 33B"
        },
        "gemma3:latest": {
          "name": "Gemma3 Latest"
        },
        "llama3.2:latest": {
          "name": "Llama 3.2"
        }
      }
    }
  }
}
```

### Usage

```bash
opencode run --model ollama/qwen3-coder:latest "Explain this function"
```

## LM Studio Setup

### Installation

1. Download from https://lmstudio.ai
2. Launch LM Studio
3. Download models from the UI
4. Start local server (default port: 1234)

### OpenCode Configuration

```json
{
  "model": "lmstudio/local-model",
  "provider": {
    "lmstudio": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "LM Studio (local)",
      "options": {
        "baseURL": "http://127.0.0.1:1234/v1"
      },
      "models": {
        "local-model": {
          "name": "Current LM Studio Model"
        }
      }
    }
  }
}
```

## Recommended Local Models

### For Coding

| Model | Size | Description |
|-------|------|-------------|
| `qwen3-coder:latest` | ~14GB | Excellent code generation |
| `deepseek-coder:33b` | ~20GB | Strong reasoning |
| `codellama:34b` | ~20GB | Meta's code model |
| `devstral:latest` | ~14GB | Mistral's code model |

### For General Tasks

| Model | Size | Description |
|-------|------|-------------|
| `llama3.2:latest` | ~8GB | Meta's latest |
| `gemma3:latest` | ~8GB | Google's model |
| `mistral:latest` | ~7GB | Fast general purpose |
| `qwen3:latest` | ~8GB | Alibaba's model |

### Vision Models

| Model | Size | Description |
|-------|------|-------------|
| `llava:latest` | ~8GB | Vision + language |
| `qwen3-vl:32b` | ~20GB | Multimodal Qwen |

## Cloud-Routed Local Models

Some Ollama setups route to cloud models:

```json
{
  "models": {
    "deepseek-v3.2:cloud": {
      "name": "Deepseek V3.2 Cloud"
    },
    "kimi-k2:1t-cloud": {
      "name": "Kimi K2 1T Cloud"
    },
    "gemini-3-flash-preview:cloud": {
      "name": "Gemini 3 Flash Cloud"
    }
  }
}
```

These use local Ollama as proxy to cloud APIs.

## Hardware Requirements

### Minimum (7B models)

- 16GB RAM
- 8GB VRAM (GPU optional)

### Recommended (13-34B models)

- 32GB RAM
- 16GB+ VRAM
- Apple Silicon M2+ or RTX 3090+

### Optimal (70B+ models)

- 64GB+ RAM
- 24GB+ VRAM (A100, H100)

## Performance Tips

### GPU Acceleration

```bash
# Check GPU availability
ollama ps

# Force GPU layers
OLLAMA_NUM_GPU=999 ollama serve
```

### Context Size

Large contexts require more memory:

```bash
# Increase context (default: 2048)
ollama run qwen3-coder --ctx-size 8192
```

### Concurrent Requests

```bash
# Set max concurrent requests
OLLAMA_MAX_LOADED_MODELS=2 ollama serve
```

## Troubleshooting

### Model Not Loading

```bash
# Check Ollama status
ollama list

# Re-pull model
ollama pull model-name
```

### Out of Memory

- Use smaller quantization (q4 vs q8)
- Reduce context size
- Close other applications

### Slow Response

- Enable GPU acceleration
- Use smaller model
- Check available VRAM

### Connection Refused

```bash
# Verify server is running
curl http://localhost:11434/v1/models

# Restart Ollama
ollama serve
```

## Quantization Levels

| Level | Quality | Speed | Memory |
|-------|---------|-------|--------|
| Q8 | Highest | Slowest | Most |
| Q6 | High | Slow | High |
| Q4 | Good | Fast | Medium |
| Q3 | Acceptable | Fastest | Least |

Select quantization when pulling:

```bash
ollama pull qwen3-coder:latest-q4
```
