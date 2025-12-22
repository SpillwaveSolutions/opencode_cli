# OpenCode CLI Skill

A Claude Code skill for headless LLM automation using the OpenCode CLI.

## Overview

This skill provides Claude with comprehensive knowledge about the [OpenCode CLI](https://opencode.ai) - a Go-based tool that provides access to 75+ LLM providers through a unified interface. The focus is on the headless `run` command for automation and subprocess integration.

## Installation

Place this skill in your Claude Code skills directory:

```
~/.claude/skills/opencode_cli/
```

## Usage

The skill activates automatically when you ask Claude about:

- Configuring OpenCode CLI
- Using OpenCode for headless automation
- Setting up providers (Vertex AI, Anthropic, OpenAI, etc.)
- Configuring MCP servers
- Running local models (Ollama, LM Studio)
- Subprocess integration patterns

### Example Prompts

```
"How do I configure opencode for Vertex AI?"
"Set up opencode with Gemini 2.5 Pro"
"What's the opencode run command syntax?"
"Configure opencode for local Ollama models"
```

## Structure

```
opencode_cli/
├── SKILL.md                      # Main skill entry point
├── README.md                     # This file
└── references/
    ├── vertex-ai-setup.md        # Google Vertex AI configuration
    ├── provider-config.md        # Provider configuration guide
    ├── cloud-providers.md        # Deepseek, Kimi, Mistral, etc.
    ├── local-models.md           # Ollama, LM Studio setup
    ├── mcp-servers.md            # MCP server configuration
    └── integration-patterns.md   # Subprocess integration patterns
```

## Quick Reference

### Basic Command

```bash
opencode run --model <provider/model> "<prompt>"
```

### Supported Providers

| Provider | Example Model |
|----------|---------------|
| `anthropic` | `anthropic/claude-sonnet-4-20250514` |
| `google` | `google/gemini-2.5-pro` |
| `google-vertex` | `google-vertex/gemini-2.5-pro` |
| `openai` | `openai/gpt-4o` |
| `opencode` | `opencode/grok-code` (free tier) |
| `ollama` | `ollama/llama3.2` (local) |

### Configuration Locations

1. `OPENCODE_CONFIG` environment variable
2. `opencode.json` in project root
3. `~/.config/opencode/opencode.json`

## Key Differences from Claude CLI

| Feature | OpenCode | Claude CLI |
|---------|----------|------------|
| Headless mode | `run` subcommand | `-p` flag with stdin |
| Hooks support | No | Yes |
| Directory access | No | Yes (`--add-dir`) |
| Prompt input | Positional argument | Stdin or `-p` |

## License

MIT
