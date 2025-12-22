---
name: opencode_cli
description: This skill should be used when configuring or using the OpenCode CLI for headless LLM automation. Use when the user asks to "configure opencode", "use opencode cli", "set up opencode", "opencode run command", "opencode model selection", "opencode providers", "opencode vertex ai", "opencode mcp servers", "opencode ollama", "opencode local models", "opencode deepseek", "opencode kimi", "opencode mistral", "fallback cli tool", or "headless llm cli". Covers command syntax, provider configuration, Vertex AI setup, MCP servers, local models, cloud providers, and subprocess integration patterns.
---

# OpenCode CLI Skill

Use OpenCode CLI for headless LLM automation via subprocess invocation.

## Overview

OpenCode is a Go-based CLI that provides access to 75+ LLM providers through a unified interface. This skill focuses on the headless `run` command for automation and subprocess integration.

## Basic Usage

### Command Format

```bash
opencode run --model <provider/model> "<prompt>"
```

**Key points:**
- Use `run` subcommand for headless (non-interactive) mode
- Model format is always `provider/model`
- Prompt is a positional argument at the end
- No stdin support (unlike Claude CLI's `-p` flag)

### Examples

```bash
# Using Anthropic Claude
opencode run --model anthropic/claude-sonnet-4-20250514 "Explain this code"

# Using Google Gemini
opencode run --model google/gemini-2.5-pro "Review this architecture"

# Using free Grok tier
opencode run --model opencode/grok-code "Generate tests for this function"
```

## Model Format

Models use the pattern `provider/model-name`:

| Provider | Example Model |
|----------|---------------|
| `anthropic` | `anthropic/claude-sonnet-4-20250514` |
| `google` | `google/gemini-2.5-pro` |
| `opencode` | `opencode/grok-code` (free tier) |
| `openai` | `openai/gpt-4o` |
| `google-vertex` | `google-vertex/gemini-2.5-pro` |

## Configuration

### Config File Locations

1. **Environment variable**: `OPENCODE_CONFIG` path
2. **Project-level**: `opencode.json` in project root
3. **Global**: `~/.config/opencode/opencode.json`

Configs are merged (project overrides global).

### Basic Configuration

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5"
}
```

### Authentication

Credentials stored in `~/.local/share/opencode/auth.json` after running `/connect` in TUI mode, or configure via environment variables.

## Reference Guides

Load the appropriate reference for detailed configuration:

| Task | Reference File |
|------|----------------|
| Setting up Google Vertex AI | [vertex-ai-setup.md](references/vertex-ai-setup.md) |
| Configuring providers (Anthropic, OpenAI, etc.) | [provider-config.md](references/provider-config.md) |
| Cloud providers (Deepseek, Kimi, Mistral, etc.) | [cloud-providers.md](references/cloud-providers.md) |
| Local models (Ollama, LM Studio) | [local-models.md](references/local-models.md) |
| MCP server configuration | [mcp-servers.md](references/mcp-servers.md) |
| Subprocess integration patterns | [integration-patterns.md](references/integration-patterns.md) |

## Quick Vertex AI Setup

For Google Cloud Vertex AI integration:

```bash
# Set required environment variables
export GOOGLE_CLOUD_PROJECT="your-project-id"
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
export VERTEX_LOCATION="us-central1"  # Optional, defaults to global
```

See [vertex-ai-setup.md](references/vertex-ai-setup.md) for detailed configuration.

## Subprocess Invocation

### Basic Pattern

```python
import subprocess

result = subprocess.run(
    ["opencode", "run", "--model", "google/gemini-2.5-pro", prompt],
    capture_output=True,
    text=True,
    timeout=600
)
output = result.stdout
```

### Key Considerations

1. **Stagger parallel calls** - Add 5-10 second delays between parallel invocations to avoid cache race conditions
2. **Implement fallback** - Consider Claude CLI as fallback if OpenCode fails
3. **Health check** - Use `opencode --version` to verify availability
4. **Timeout handling** - Set appropriate timeouts (default 600s for long generations)

See [integration-patterns.md](references/integration-patterns.md) for complete patterns.

## Limitations vs Claude CLI

| Feature | OpenCode | Claude CLI |
|---------|----------|------------|
| Headless mode | `run` subcommand | `-p` flag with stdin |
| Hooks/settings | Not supported | `--settings` flag |
| Directory access | Not supported | `--add-dir` flag |
| Tool pre-approval | Not supported | `--allowedTools` flag |
| Prompt input | Positional argument | Stdin or `-p` |

## Environment Variables

| Variable | Purpose |
|----------|---------|
| `OPENCODE_CONFIG` | Custom config file path |
| `GOOGLE_CLOUD_PROJECT` | GCP project for Vertex AI |
| `GOOGLE_APPLICATION_CREDENTIALS` | Service account JSON path |
| `VERTEX_LOCATION` | Vertex AI region |

## Health Monitoring

Check tool availability:

```bash
opencode --version
```

Returns version string if installed and accessible.

## Best Practices

1. **Use project-level config** - Create `opencode.json` for project-specific settings
2. **Prefer environment variables** - Use `{env:VAR_NAME}` syntax in config for secrets
3. **Implement retries** - Network failures are common; implement retry logic
4. **Log output** - Capture both stdout and stderr for debugging
5. **Stagger parallel calls** - Prevent cache race conditions with delays