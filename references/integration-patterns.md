# Integration Patterns for OpenCode CLI

Generic patterns for integrating OpenCode CLI into applications.

> **Note:** These are documentation examples showing integration patterns.
> Adapt to your project's specific subprocess handling utilities.

## Command Structure

OpenCode CLI uses this command structure for headless mode:

```
opencode run --model <provider/model> "<prompt>"
```

**Key components:**
- `run` - Required subcommand for headless execution
- `--model` - Provider and model in `provider/model` format
- `prompt` - Positional argument (the actual prompt text)

## Basic Invocation Pattern

### Command Building

```python
# Build the command as a list (safer than string concatenation)
cmd = [
    "opencode",           # CLI executable
    "run",                # Headless subcommand
    "--model", model,     # Model specification
    prompt                # Prompt as positional arg
]
```

### Expected Behavior

| Return Code | Meaning |
|-------------|---------|
| 0 | Success - output in stdout |
| Non-zero | Failure - check stderr |
| Timeout | Process exceeded time limit |
| Not Found | CLI not in PATH |

## Fallback Strategy

When the primary tool fails, fall back to an alternative:

```
Primary Tool (OpenCode)
    ↓ (on failure)
Fallback Tool (Claude CLI)
    ↓ (on failure)
Return Error
```

**Fallback triggers:**
1. Non-zero return code
2. Timeout exceeded
3. Tool not found in PATH

**Important:** Prevent infinite recursion by disabling fallback on the fallback call.

## Parallel Execution with Stagger Delay

When running multiple OpenCode processes in parallel:

```
Process 0: Start immediately
Process 1: Wait 5 seconds, then start
Process 2: Wait 10 seconds, then start
Process 3: Wait 15 seconds, then start
```

**Why stagger?** OpenCode's node_modules cache can experience race conditions when multiple processes initialize simultaneously. A 5-10 second delay between starts prevents conflicts.

### Stagger Calculation

```
delay = process_index * stagger_delay_seconds
```

Example with `stagger_delay = 5.0` and 4 parallel processes:
- Index 0: 0s delay
- Index 1: 5s delay
- Index 2: 10s delay
- Index 3: 15s delay

## Health Monitoring

### Availability Check

Verify OpenCode is available before invocation:

```bash
# Check if in PATH
which opencode

# Verify version
opencode --version
```

### Tool Selection

Check available tools and select appropriately:

```
if opencode available:
    use opencode
elif claude available:
    use claude
else:
    raise error
```

## Task-Specific Routing

Route different task types to appropriate tools/models:

| Task Type | Recommended Tool | Model |
|-----------|------------------|-------|
| TOC Generation | Claude CLI | sonnet |
| Content Drafting | Claude CLI | sonnet |
| Editing | OpenCode | google/gemini-2.5-pro |
| Research | Claude CLI | opus |

### Configuration Pattern

```toml
[tasks.toc_generation]
cli = "claude"
model = "sonnet"

[tasks.editing]
cli = "opencode"
model = "google/gemini-2.5-pro"

[tasks.research]
cli = "claude"
model = "opus"
```

## Error Handling

### Status Categories

```
SUCCESS    - Completed successfully (return code 0)
TIMEOUT    - Exceeded time limit
NOT_FOUND  - CLI not in PATH
FAILED     - Non-zero return code
FALLBACK   - Primary failed, fallback succeeded
```

### Recommended Handling

1. **Capture both stdout and stderr** - stderr contains error details
2. **Set appropriate timeout** - Default 600 seconds for long generations
3. **Log duration** - Track performance for optimization
4. **Implement retry** - Transient failures are common

## Configuration Structure

Example TOML configuration for CLI routing:

```toml
[cli]
primary = "claude"        # Primary CLI tool
fallback = "opencode"     # Fallback on failure
timeout = 600             # Seconds per invocation
stagger_delay = 5.0       # Seconds between parallel starts
max_parallel_chapters = 3 # Concurrent processes

[cli.opencode]
model = "google/gemini-2.5-pro"
command = "opencode"
args = ["run"]

[cli.claude]
model = "sonnet"
command = "claude"
args = ["-p"]
```

## Key Differences: OpenCode vs Claude CLI

| Aspect | OpenCode | Claude CLI |
|--------|----------|------------|
| Headless command | `opencode run` | `claude -p` |
| Prompt input | Positional arg | Stdin or `-p` flag |
| Model format | `provider/model` | Short names (sonnet, opus) |
| Hooks support | No | Yes (`--settings`) |
| Directory access | No | Yes (`--add-dir`) |
| Tool pre-approval | No | Yes (`--allowedTools`) |

## Best Practices Summary

1. **Always use timeouts** - LLM calls can hang indefinitely
2. **Implement fallback** - Network issues are common
3. **Stagger parallel calls** - Prevents cache race conditions (5-10s delay)
4. **Check availability** - Verify tool exists before invocation
5. **Capture stderr** - Essential for debugging failures
6. **Log durations** - Track performance and identify slow calls
7. **Route by task type** - Different tasks benefit from different models
8. **Use list-based commands** - Safer than string concatenation

## Environment Variables

Common environment variables for integration:

| Variable | Purpose |
|----------|---------|
| `OPENCODE_CONFIG` | Custom config file path |
| `GOOGLE_CLOUD_PROJECT` | GCP project for Vertex AI |
| `GOOGLE_APPLICATION_CREDENTIALS` | Service account JSON |
| `ANTHROPIC_API_KEY` | Anthropic API key |
| `OPENAI_API_KEY` | OpenAI API key |