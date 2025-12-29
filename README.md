# OpenCode CLI Skill

A Claude Code skill for headless LLM automation using the OpenCode CLI.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
  - [Installing with Skilz](#installing-with-skilz)
  - [Manual Installation](#manual-installation)
- [Usage](#usage)
  - [Example Prompts](#example-prompts)
- [Quick Reference](#quick-reference)
  - [Basic Command](#basic-command)
  - [Supported Providers](#supported-providers)
  - [Configuration Locations](#configuration-locations)
- [Project Structure](#project-structure)
- [Key Differences from Claude CLI](#key-differences-from-claude-cli)
- [License](#license)

## Overview

This skill provides Claude with comprehensive knowledge about the [OpenCode CLI](https://opencode.ai), a Go-based tool that provides access to 75+ LLM providers through a unified interface. The skill focuses on the headless `run` command for automation and subprocess integration.

## Features

- **Multi-Provider Support**: Access 75+ LLM providers including Anthropic, Google, OpenAI, and more
- **Headless Automation**: Use the `run` command for non-interactive LLM operations
- **Subprocess Integration**: Patterns for integrating OpenCode into your automation pipelines
- **Local Model Support**: Configuration guides for Ollama and LM Studio
- **MCP Server Configuration**: Set up and manage Model Context Protocol servers

## Installation

### Installing with Skilz

The easiest way to install this skill is using the [Skilz universal installer](https://github.com/rickbsgu/skilz):

```bash
# Install skilz if you haven't already
npm install -g skilz

# Install this skill
skilz install SpillwaveSolutions_opencode_cli/opencode_cli
```

The skill will be installed to `~/.claude/skills/opencode_cli/`.

View this skill on the [Skilz Marketplace](https://skillzwave.ai/skill/SpillwaveSolutions__opencode_cli__opencode_cli__SKILL/).

### Manual Installation

Clone or download this repository to your Claude Code skills directory:

```bash
git clone https://github.com/SpillwaveSolutions/opencode_cli.git ~/.claude/skills/opencode_cli
```

Or manually place the skill files in:

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
"How do I integrate opencode into a Python subprocess?"
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

Configuration files are searched in the following order (project settings override global):

1. **Environment Variable**: `OPENCODE_CONFIG` path
2. **Project-Level**: `opencode.json` in project root
3. **Global**: `~/.config/opencode/opencode.json`

## Project Structure

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

## Key Differences from Claude CLI

| Feature | OpenCode | Claude CLI |
|---------|----------|------------|
| Headless mode | `run` subcommand | `-p` flag with stdin |
| Hooks support | No | Yes |
| Directory access | No | Yes (`--add-dir`) |
| Prompt input | Positional argument | Stdin or `-p` |

## License

MIT
