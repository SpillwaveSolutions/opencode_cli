# Cloud Model Providers for OpenCode

Configure various cloud LLM providers beyond the major platforms.

## Deepseek

Chinese AI lab with strong coding models.

### Configuration

```json
{
  "model": "deepseek/deepseek-coder",
  "provider": {
    "deepseek": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Deepseek",
      "options": {
        "baseURL": "https://api.deepseek.com/v1",
        "apiKey": "{env:DEEPSEEK_API_KEY}"
      },
      "models": {
        "deepseek-coder": {
          "name": "Deepseek Coder",
          "limit": { "context": 128000, "output": 8192 }
        },
        "deepseek-chat": {
          "name": "Deepseek Chat",
          "limit": { "context": 128000, "output": 8192 }
        },
        "deepseek-reasoner": {
          "name": "Deepseek Reasoner",
          "limit": { "context": 128000, "output": 8192 }
        }
      }
    }
  }
}
```

**Get API key:** https://platform.deepseek.com/

### Models

| Model | Best For |
|-------|----------|
| `deepseek-coder` | Code generation |
| `deepseek-chat` | General conversation |
| `deepseek-reasoner` | Complex reasoning |

## Kimi (Moonshot)

Long-context specialist from Moonshot AI.

### Configuration

```json
{
  "model": "kimi/moonshot-v1-128k",
  "provider": {
    "kimi": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Kimi",
      "options": {
        "baseURL": "https://api.moonshot.cn/v1",
        "apiKey": "{env:MOONSHOT_API_KEY}"
      },
      "models": {
        "moonshot-v1-8k": {
          "name": "Moonshot 8K",
          "limit": { "context": 8000, "output": 4096 }
        },
        "moonshot-v1-32k": {
          "name": "Moonshot 32K",
          "limit": { "context": 32000, "output": 4096 }
        },
        "moonshot-v1-128k": {
          "name": "Moonshot 128K",
          "limit": { "context": 128000, "output": 4096 }
        },
        "kimi-k2": {
          "name": "Kimi K2",
          "limit": { "context": 200000, "output": 8192 }
        }
      }
    }
  }
}
```

**Get API key:** https://platform.moonshot.cn/

## Qwen (Alibaba)

Alibaba's multilingual models.

### Configuration

```json
{
  "model": "qwen/qwen-max",
  "provider": {
    "qwen": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Qwen",
      "options": {
        "baseURL": "https://dashscope.aliyuncs.com/compatible-mode/v1",
        "apiKey": "{env:DASHSCOPE_API_KEY}"
      },
      "models": {
        "qwen-max": {
          "name": "Qwen Max",
          "limit": { "context": 32000, "output": 8192 }
        },
        "qwen-plus": {
          "name": "Qwen Plus",
          "limit": { "context": 32000, "output": 8192 }
        },
        "qwen-turbo": {
          "name": "Qwen Turbo",
          "limit": { "context": 8000, "output": 4096 }
        },
        "qwen-coder-plus": {
          "name": "Qwen Coder Plus",
          "limit": { "context": 128000, "output": 8192 }
        }
      }
    }
  }
}
```

**Get API key:** https://dashscope.console.aliyun.com/

## Mistral

European AI with efficient models.

### Configuration

```json
{
  "model": "mistral/mistral-large-latest",
  "provider": {
    "mistral": {
      "npm": "@ai-sdk/mistral",
      "options": {
        "apiKey": "{env:MISTRAL_API_KEY}"
      },
      "models": {
        "mistral-large-latest": {
          "name": "Mistral Large",
          "limit": { "context": 128000, "output": 8192 }
        },
        "mistral-medium-latest": {
          "name": "Mistral Medium",
          "limit": { "context": 32000, "output": 8192 }
        },
        "codestral-latest": {
          "name": "Codestral",
          "limit": { "context": 32000, "output": 8192 }
        },
        "devstral-latest": {
          "name": "Devstral",
          "limit": { "context": 128000, "output": 8192 }
        }
      }
    }
  }
}
```

**Get API key:** https://console.mistral.ai/

## GLM (Zhipu)

Chinese multimodal models.

### Configuration

```json
{
  "model": "zhipu/glm-4",
  "provider": {
    "zhipu": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Zhipu GLM",
      "options": {
        "baseURL": "https://open.bigmodel.cn/api/paas/v4",
        "apiKey": "{env:ZHIPU_API_KEY}"
      },
      "models": {
        "glm-4": {
          "name": "GLM-4",
          "limit": { "context": 128000, "output": 4096 }
        },
        "glm-4-plus": {
          "name": "GLM-4 Plus",
          "limit": { "context": 128000, "output": 4096 }
        },
        "glm-4v": {
          "name": "GLM-4V (Vision)",
          "limit": { "context": 8000, "output": 4096 }
        }
      }
    }
  }
}
```

**Get API key:** https://open.bigmodel.cn/

## Groq

Hardware-accelerated inference.

### Configuration

```json
{
  "model": "groq/llama-3.3-70b-versatile",
  "provider": {
    "groq": {
      "npm": "@ai-sdk/groq",
      "options": {
        "apiKey": "{env:GROQ_API_KEY}"
      },
      "models": {
        "llama-3.3-70b-versatile": {
          "name": "Llama 3.3 70B",
          "limit": { "context": 128000, "output": 32768 }
        },
        "mixtral-8x7b-32768": {
          "name": "Mixtral 8x7B",
          "limit": { "context": 32768, "output": 8192 }
        },
        "gemma2-9b-it": {
          "name": "Gemma2 9B",
          "limit": { "context": 8192, "output": 8192 }
        }
      }
    }
  }
}
```

**Get API key:** https://console.groq.com/

## Together AI

Open model hosting.

### Configuration

```json
{
  "model": "together/meta-llama/Meta-Llama-3.1-70B-Instruct-Turbo",
  "provider": {
    "together": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Together AI",
      "options": {
        "baseURL": "https://api.together.xyz/v1",
        "apiKey": "{env:TOGETHER_API_KEY}"
      },
      "models": {
        "meta-llama/Meta-Llama-3.1-70B-Instruct-Turbo": {
          "name": "Llama 3.1 70B Turbo"
        },
        "Qwen/Qwen2.5-Coder-32B-Instruct": {
          "name": "Qwen 2.5 Coder 32B"
        },
        "deepseek-ai/DeepSeek-R1-Distill-Llama-70B": {
          "name": "DeepSeek R1 Distill"
        }
      }
    }
  }
}
```

**Get API key:** https://api.together.xyz/

## Fireworks AI

Fast inference platform.

### Configuration

```json
{
  "model": "fireworks/accounts/fireworks/models/llama-v3p1-70b-instruct",
  "provider": {
    "fireworks": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Fireworks AI",
      "options": {
        "baseURL": "https://api.fireworks.ai/inference/v1",
        "apiKey": "{env:FIREWORKS_API_KEY}"
      },
      "models": {
        "accounts/fireworks/models/llama-v3p1-70b-instruct": {
          "name": "Llama 3.1 70B"
        },
        "accounts/fireworks/models/qwen2p5-coder-32b-instruct": {
          "name": "Qwen 2.5 Coder 32B"
        }
      }
    }
  }
}
```

**Get API key:** https://fireworks.ai/

## Provider Comparison

| Provider | Strengths | Best For |
|----------|-----------|----------|
| Deepseek | Cost-effective, strong coding | Code generation |
| Kimi | Long context (200K+) | Document analysis |
| Qwen | Multilingual, vision | Chinese content |
| Mistral | European, fast | General tasks |
| GLM | Multimodal | Chinese market |
| Groq | Extremely fast | Low-latency apps |
| Together | Many models | Experimentation |
| Fireworks | Scalable | Production loads |

## Multi-Provider Setup

Combine multiple providers:

```json
{
  "model": "deepseek/deepseek-coder",
  "small_model": "groq/gemma2-9b-it",
  "provider": {
    "deepseek": { ... },
    "groq": { ... },
    "mistral": { ... }
  }
}
```

Switch models easily:

```bash
opencode run --model groq/llama-3.3-70b-versatile "Fast query"
opencode run --model deepseek/deepseek-coder "Complex code task"
```
