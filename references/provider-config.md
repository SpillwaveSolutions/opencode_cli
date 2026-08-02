# Provider Configuration for OpenCode

Configure multiple LLM providers with OpenCode CLI.

## Configuration File Format

OpenCode uses JSON/JSONC format with schema validation:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "provider/model-name",
  "small_model": "provider/cheaper-model",
  "provider": {
    "provider-name": {
      "npm": "@ai-sdk/package-name",
      "name": "Display Name",
      "options": {},
      "models": {}
    }
  }
}
```

## File Locations

Config files are **merged** (not replaced). Later sources override earlier ones for conflicting keys. Effective order (lowest to highest):

1. Remote organizational config (`.well-known/opencode`)
2. Global: `~/.config/opencode/opencode.json`
3. Custom path: `OPENCODE_CONFIG` env var
4. Project: `opencode.json` in project root
5. `.opencode/` directories
6. Inline: `OPENCODE_CONFIG_CONTENT` env var

In practice: project config overrides `OPENCODE_CONFIG` overrides global.

## Built-in Providers

Most well-known providers (Anthropic, OpenAI, Google, etc.) need **no provider block** — just authenticate via `opencode auth login` or set the environment variable. OpenCode is powered by the provider list at models.dev.

### Anthropic

```json
{
  "model": "anthropic/claude-sonnet-4-5",
  "provider": {
    "anthropic": {
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    }
  }
}
```

**Environment:** `ANTHROPIC_API_KEY`

### OpenAI

```json
{
  "model": "openai/gpt-4o",
  "provider": {
    "openai": {
      "options": {
        "apiKey": "{env:OPENAI_API_KEY}"
      }
    }
  }
}
```

**Environment:** `OPENAI_API_KEY`

### Google AI (Direct)

```json
{
  "model": "google/gemini-2.5-pro",
  "provider": {
    "google": {
      "options": {
        "apiKey": "{env:GOOGLE_GENERATIVE_AI_API_KEY}"
      }
    }
  }
}
```

**Environment:** `GOOGLE_GENERATIVE_AI_API_KEY`

## Custom Provider Setup

### OpenAI-Compatible APIs

For providers with OpenAI-compatible endpoints:

```json
{
  "model": "custom/my-model",
  "provider": {
    "custom": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Custom Provider",
      "options": {
        "baseURL": "https://api.custom-provider.com/v1",
        "apiKey": "{env:CUSTOM_API_KEY}",
        "headers": {
          "X-Custom-Header": "value"
        }
      },
      "models": {
        "my-model": {
          "name": "My Custom Model",
          "limit": {
            "context": 128000,
            "output": 4096
          }
        }
      }
    }
  }
}
```

### Local Models (Ollama)

```json
{
  "model": "ollama/llama3.2",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "llama3.2": {
          "name": "Llama 3.2 (local)"
        }
      }
    }
  }
}
```

### LM Studio

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
          "name": "Local Model"
        }
      }
    }
  }
}
```

## Provider Control

### Disable Specific Providers

Prevent providers from loading even with valid credentials:

```json
{
  "disabled_providers": ["openai", "google"]
}
```

### Allowlist Providers

Only allow specific providers:

```json
{
  "enabled_providers": ["anthropic", "google-vertex"]
}
```

**Note:** `disabled_providers` takes priority over `enabled_providers`.

## Variable Substitution

### Environment Variables

```json
{
  "model": "{env:OPENCODE_MODEL}",
  "provider": {
    "anthropic": {
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    }
  }
}
```

If an environment variable is not set, it is replaced with an empty string.

### File References

```json
{
  "provider": {
    "openai": {
      "options": {
        "apiKey": "{file:~/.secrets/openai-key}"
      }
    }
  }
}
```

File paths can be relative to the config file directory, or absolute paths starting with `/` or `~`.

## Model Configuration

### Token Limits

Specify context window and output limits:

```json
{
  "models": {
    "my-model": {
      "name": "Display Name",
      "limit": {
        "context": 200000,
        "output": 8192
      }
    }
  }
}
```

### Small Model

Configure a cheaper model for lightweight tasks:

```json
{
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5"
}
```

OpenCode automatically uses `small_model` for tasks like title generation.

### Provider Options

```json
{
  "provider": {
    "anthropic": {
      "options": {
        "timeout": 600000,
        "chunkTimeout": 30000,
        "setCacheKey": true
      }
    }
  }
}
```

- `timeout` - Request timeout in ms (default 300000; set `false` to disable)
- `chunkTimeout` - Timeout between streamed chunks
- `setCacheKey` - Always set a cache key

## Authentication Methods

### API Key (Most Common)

```json
{
  "options": {
    "apiKey": "{env:API_KEY}"
  }
}
```

### AWS Bedrock

```json
{
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "my-aws-profile"
      }
    }
  }
}
```

### Azure OpenAI

```json
{
  "provider": {
    "azure": {
      "npm": "@ai-sdk/azure",
      "options": {
        "resourceName": "{env:AZURE_RESOURCE_NAME}",
        "apiKey": "{env:AZURE_API_KEY}"
      }
    }
  }
}
```

### Google Cloud (ADC)

Use Application Default Credentials:

```bash
gcloud auth application-default login
```

Or service account:

```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"
```

## Complete Example

Multi-provider configuration:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "google/gemini-2.5-flash",

  "disabled_providers": ["openai"],

  "provider": {
    "anthropic": {
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    },
    "google": {
      "options": {
        "apiKey": "{env:GOOGLE_GENERATIVE_AI_API_KEY}"
      }
    },
    "google-vertex": {
      "npm": "@ai-sdk/google-vertex",
      "options": {
        "project": "{env:GOOGLE_CLOUD_PROJECT}",
        "location": "us-central1"
      },
      "models": {
        "gemini-2.5-pro": {
          "name": "Gemini 2.5 Pro (Vertex)"
        }
      }
    },
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama Local",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "llama3.2": { "name": "Llama 3.2" }
      }
    }
  }
}
```

## Verification

List available models after configuration:

```bash
opencode models [provider]   # or: opencode models --refresh

# Or test headless
opencode run --model provider/model "Test prompt"
```
