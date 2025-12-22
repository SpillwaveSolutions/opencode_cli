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

Configs are merged in precedence order:

1. `OPENCODE_CONFIG` environment variable (highest)
2. `opencode.json` in project root
3. `~/.config/opencode/opencode.json` (lowest)

## Built-in Providers

### Anthropic

```json
{
  "model": "anthropic/claude-sonnet-4-20250514",
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

### OpenCode Free Tier

No configuration needed - use directly:

```bash
opencode run --model opencode/grok-code "Your prompt"
```

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
        },
        "codellama": {
          "name": "Code Llama (local)"
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

Supports:
- Absolute paths: `/path/to/file`
- Home directory: `~/path/to/file`
- Relative paths: `./path/to/file` (relative to config directory)

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

```bash
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
export AWS_REGION="us-east-1"
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
        "llama3.2": { "name": "Llama 3.2" },
        "codellama": { "name": "Code Llama" }
      }
    }
  }
}
```

## Verification

List available models after configuration:

```bash
# In TUI mode
opencode
# Then type /models

# Or test headless
opencode run --model provider/model "Test prompt"
```
