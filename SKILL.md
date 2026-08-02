---
name: opencode-cli
description: This skill should be used when configuring or using the OpenCode CLI for headless LLM automation. Use when the user asks to "configure opencode", "use opencode cli", "set up opencode", "opencode run command", "opencode model selection", "opencode providers", "opencode vertex ai", "opencode mcp servers", "opencode ollama", "opencode local models", "opencode deepseek", "opencode kimi", "opencode mistral", "opencode serve", "opencode auth login", "headless llm cli", or "fallback cli tool". Covers command syntax, provider configuration, Vertex AI setup, MCP servers, local models, cloud providers, and subprocess integration patterns. The canonical source of truth is https://opencode.ai/docs.
---

# OpenCode CLI Skill

Use OpenCode CLI for headless LLM automation via subprocess invocation.

## Table of Contents

- [Quick Start](#quick-start)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Model Format](#model-format)
- [Configuration](#configuration)
- [Reference Guides](#reference-guides)
- [Vertex AI Setup](#vertex-ai-setup)
- [Subprocess Invocation](#subprocess-invocation)
- [Features vs Claude CLI](#features-vs-claude-cli)
- [Environment Variables](#environment-variables)
- [Verify Setup](#verify-setup)
- [Best Practices](#best-practices)

## Quick Start

1. Install OpenCode CLI (see [OpenCode documentation](https://opencode.ai/docs)). On Windows: `npm install -g opencode-ai`, `choco install opencode`, or `scoop install opencode`; WSL is recommended.
2. Authenticate with a provider:
   ```bash
   opencode auth login        # stores in ~/.local/share/opencode/auth.json
   # OR set env vars, e.g. ANTHROPIC_API_KEY / GOOGLE_CLOUD_PROJECT for Vertex AI
   ```
3. Verify installation:
   ```bash
   opencode --version
   ```
4. Test with a simple prompt:
   ```bash
   opencode run --model google/gemini-2.5-pro "Hello, world"
   ```

## Overview

OpenCode is a TypeScript/Bun-based CLI that provides access to many LLM providers through a unified interface. This skill focuses on the headless `run` command for automation and subprocess integration.

## Basic Usage

### Command Format

```bash
opencode run --model <provider/model> "<prompt>"
```

**Key points:**
- Use `run` subcommand for headless (non-interactive) mode
- Model format is always `provider/model`, set via `-m`/`--model`
- Prompt is a positional argument at the end
- Add `--format json` for machine-readable output
- Use `opencode serve` + `opencode run --attach <url>` to reuse a warm server

### Examples

```bash
# Using Anthropic Claude
opencode run --model anthropic/claude-sonnet-4-5 "Explain this code"

# Using Google Gemini
opencode run --model google/gemini-2.5-pro "Review this architecture"

# Using the configured default model
opencode run "Generate tests for this function"

# JSON output for scripting
opencode run --format json --model openai/gpt-4o-mini "Short summary"
```

## Model Format

Models use the pattern `provider/model-name`:

| Provider | Example Model |
|----------|---------------|
| `anthropic` | `anthropic/claude-sonnet-4-5` |
| `google` | `google/gemini-2.5-pro` |
| `openai` | `openai/gpt-4o` |
| `google-vertex` | `google-vertex/gemini-2.5-pro` |
| `ollama` | `ollama/llama3.2` (local) |

List available models with `opencode models [provider]` (add `--refresh` to update the cache).

## Configuration

### Config File Locations

Config files are **merged** (not replaced); later sources override earlier ones for conflicting keys. Effective order (lowest to highest):

1. **Remote config** — organizational defaults from `.well-known/opencode`
2. **Global**: `~/.config/opencode/opencode.json`
3. **Custom path**: `OPENCODE_CONFIG` env var
4. **Project**: `opencode.json` in project root
5. `.opencode/` directories (agents, commands, plugins)
6. **Inline**: `OPENCODE_CONFIG_CONTENT` env var
7. **Managed** (admin-controlled, not user-overridable)

In practice: project config overrides `OPENCODE_CONFIG` overrides global.

### Basic Configuration

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5"
}
```

### Authentication

Run `opencode auth login` for any provider supported by models.dev, or configure via environment variables. Credentials are stored in `~/.local/share/opencode/auth.json`.

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

## Vertex AI Setup

See [vertex-ai-setup.md](references/vertex-ai-setup.md) for Vertex AI configuration including environment variables and service account setup.

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

1. **Prefer `--format json`** - Use for stable, machine-readable output
2. **Use `serve` + `--attach`** - Avoid MCP/provider cold-boot on every invocation
3. **Implement fallback** - Consider Claude CLI as fallback if OpenCode fails
4. **Health check** - Use `opencode --version` to verify availability
5. **Timeout handling** - Set appropriate timeouts (default 600s for long generations)

See [integration-patterns.md](references/integration-patterns.md) for complete patterns.

## Features vs Claude CLI

| Feature | OpenCode | Claude CLI |
|---------|----------|------------|
| Headless mode | `run` subcommand | `-p` flag with stdin |
| Model format | `provider/model` | Short names (sonnet, opus) |
| Permissions/pre-approval | `permission` config + `run --auto` | `--allowedTools` flag |
| Session continuation | `run -c` / `run -s <id>` | `--resume` |
| Machine-readable output | `run --format json` | `--output-format` |
| Prompt input | Positional argument | Stdin or `-p` |

## Environment Variables

| Variable | Purpose |
|----------|---------|
| `OPENCODE_CONFIG` | Custom config file path |
| `OPENCODE_CONFIG_CONTENT` | Inline JSON config |
| `OPENCODE_SERVER_PASSWORD` | Basic auth for `serve`/`web` |
| `GOOGLE_CLOUD_PROJECT` | GCP project for Vertex AI |
| `GOOGLE_APPLICATION_CREDENTIALS` | Service account JSON path |
| `ANTHROPIC_API_KEY` | Anthropic API key |
| `OPENAI_API_KEY` | OpenAI API key |

## Verify Setup

Complete this checklist to verify a working installation:

1. **Check version** - Confirm CLI is installed:
   ```bash
   opencode --version
   ```
2. **Test default model** - Verify basic connectivity:
   ```bash
   opencode run --model google/gemini-2.5-pro "Say hello"
   ```
3. **List models** - Review available models:
   ```bash
   opencode models [provider]
   ```
4. **Check configuration** - Review active config:
   ```bash
   cat ~/.config/opencode/opencode.json
   ```
5. **Verify MCP servers** (if configured) - `opencode mcp list`

## Best Practices

1. **Use project-level config** - Create `opencode.json` for project-specific settings
2. **Prefer environment variables** - Use `{env:VAR_NAME}` / `{file:path}` syntax in config for secrets
3. **Implement retries** - Network failures are common; implement retry logic
4. **Log output** - Capture both stdout and stderr for debugging
5. **Use `serve` + `--attach`** - Reuse a warm server for repeated/parallel invocations
