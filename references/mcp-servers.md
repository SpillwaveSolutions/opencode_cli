# MCP Server Configuration for OpenCode

Configure Model Context Protocol (MCP) servers to extend OpenCode with additional tools.

## Configuration Location

Add MCP servers in `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "server-name": {
      "type": "local",
      "command": ["npx", "-y", "@package/name"],
      "enabled": true,
      "environment": {}
    }
  }
}
```

## Server Types

| Type | Description |
|------|-------------|
| `local` | Run as a local subprocess |
| `remote` | Connect to a remote URL (supports OAuth) |

### Local Server Options

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | Must be `"local"` |
| `command` | Yes | Command array to execute |
| `cwd` | No | Working directory for the process |
| `environment` | No | Environment variables |
| `enabled` | No | Enable/disable on startup (default true) |
| `timeout` | No | Tool-fetch timeout in ms (default 5000) |

### Remote Server Options

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | Must be `"remote"` |
| `url` | Yes | Remote MCP server URL |
| `headers` | No | Headers (e.g. `Authorization`) |
| `oauth` | No | OAuth config, or `false` to disable auto-OAuth |
| `enabled` | No | Enable/disable on startup |

## Common MCP Servers

### Context7 (Documentation Lookup) — Remote (recommended)

```json
{
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

With an optional API key for higher rate limits:

```json
{
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    }
  }
}
```

### Brave Search

```json
{
  "mcp": {
    "brave-search": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-brave-search"],
      "enabled": true,
      "environment": {
        "BRAVE_API_KEY": "{env:BRAVE_API_KEY}"
      }
    }
  }
}
```

**Get API key:** https://brave.com/search/api/

### GitHub

```json
{
  "mcp": {
    "github": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-github"],
      "enabled": true,
      "environment": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "{env:GITHUB_TOKEN}"
      }
    }
  }
}
```

**Note:** The GitHub MCP server adds a lot of tokens to the context; disable when not needed.

### Filesystem

```json
{
  "mcp": {
    "filesystem": {
      "type": "local",
      "command": [
        "npx", "-y", "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ],
      "enabled": true
    }
  }
}
```

### Sequential Thinking / Memory

```json
{
  "mcp": {
    "sequential-thinking": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"],
      "enabled": true
    },
    "memory": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-memory"],
      "enabled": true
    }
  }
}
```

> **Package names:** The official reference servers are published under the `@modelcontextprotocol/*` scope. Older `@anthropic-ai/mcp-server-*` names are deprecated — verify current package names with `npm view <package>` before relying on them.

## CLI Management

```bash
opencode mcp add              # interactive add
opencode mcp list             # or `opencode mcp ls` — list servers + status
opencode mcp auth <name>      # OAuth auth for remote servers
opencode mcp auth list        # OAuth status for all servers
opencode mcp logout <name>    # remove OAuth credentials
opencode mcp debug <name>     # diagnose connection/OAuth issues
```

OAuth tokens are stored in `~/.local/share/opencode/mcp-auth.json`.

## Environment Variables

Use variable substitution for secrets:

```json
{
  "environment": {
    "API_KEY": "{env:MY_API_KEY}"
  }
}
```

**Best practice:** Never commit API keys. Use environment variables.

## Multiple Servers Example

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp"
    },
    "github": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-github"],
      "enabled": true,
      "environment": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "{env:GITHUB_TOKEN}"
      }
    }
  }
}
```

## Disabling Servers

Set `enabled: false` to disable without removing:

```json
{
  "mcp": {
    "expensive-server": {
      "type": "local",
      "command": ["..."],
      "enabled": false
    }
  }
}
```

You can also disable MCP tools globally via `tools` globs (tools are registered with the server name as a prefix, e.g. `my-mcp*`):

```json
{
  "mcp": {
    "my-mcp": { "type": "local", "command": ["bun", "x", "my-mcp-command"] }
  },
  "tools": {
    "my-mcp*": false
  }
}
```

## Troubleshooting

### Server Not Loading

1. Check `npx` is available
2. Verify package name is correct (`npm view <package>`)
3. Check environment variables are set
4. Run `opencode mcp list` / `opencode mcp debug <name>` and check logs

### Permission Errors

Ensure environment variables are accessible:

```bash
# Verify variable is set
echo $BRAVE_API_KEY
```

### Package Not Found

Update npm cache:

```bash
npx -y clear-npx-cache
npx -y @package/name
```
