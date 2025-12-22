# MCP Server Configuration for OpenCode

Configure Model Context Protocol (MCP) servers to extend OpenCode with additional tools.

## Configuration Location

Add MCP servers in `opencode.json`:

```json
{
  "$schema": "https://opencode.dev/schema/opencode.json",
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

## Common MCP Servers

### Brave Search

Web search capability:

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

### Context7 (Documentation Lookup)

Library documentation access:

```json
{
  "mcp": {
    "context7": {
      "type": "local",
      "command": ["npx", "-y", "@upstash/context7-mcp"],
      "enabled": true
    }
  }
}
```

No API key required.

### Perplexity Ask

AI-powered search:

```json
{
  "mcp": {
    "perplexity-ask": {
      "type": "local",
      "command": ["npx", "-y", "server-perplexity-ask"],
      "enabled": true,
      "environment": {
        "PERPLEXITY_API_KEY": "{env:PERPLEXITY_API_KEY}"
      }
    }
  }
}
```

**Get API key:** https://www.perplexity.ai/settings/api

### Notion API

Notion workspace access:

```json
{
  "mcp": {
    "notionApi": {
      "type": "local",
      "command": ["npx", "-y", "@notionhq/notion-mcp-server"],
      "enabled": true,
      "environment": {
        "OPENAPI_MCP_HEADERS": "{\"Authorization\": \"Bearer {env:NOTION_API_KEY}\", \"Notion-Version\": \"2022-06-28\"}"
      }
    }
  }
}
```

**Get API key:** https://www.notion.so/my-integrations

### GitHub

Repository access:

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

### Filesystem

Local file access:

```json
{
  "mcp": {
    "filesystem": {
      "type": "local",
      "command": [
        "npx", "-y", "@anthropic-ai/mcp-server-filesystem",
        "/path/to/allowed/directory"
      ],
      "enabled": true
    }
  }
}
```

### Sequential Thinking

Chain-of-thought reasoning:

```json
{
  "mcp": {
    "sequential-thinking": {
      "type": "local",
      "command": ["npx", "-y", "@anthropic-ai/mcp-server-sequential-thinking"],
      "enabled": true
    }
  }
}
```

### Memory

Persistent memory across sessions:

```json
{
  "mcp": {
    "memory": {
      "type": "local",
      "command": ["npx", "-y", "@anthropic-ai/mcp-server-memory"],
      "enabled": true
    }
  }
}
```

## Configuration Options

### Server Types

| Type | Description |
|------|-------------|
| `local` | Run as local subprocess |
| `remote` | Connect to remote server |

### Common Fields

```json
{
  "server-name": {
    "type": "local",
    "command": ["executable", "arg1", "arg2"],
    "enabled": true,
    "environment": {
      "VAR_NAME": "value"
    }
  }
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | Server type (local/remote) |
| `command` | Yes | Command array to execute |
| `enabled` | No | Enable/disable server (default: true) |
| `environment` | No | Environment variables |

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
  "$schema": "https://opencode.dev/schema/opencode.json",
  "mcp": {
    "brave-search": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-brave-search"],
      "enabled": true,
      "environment": {
        "BRAVE_API_KEY": "{env:BRAVE_API_KEY}"
      }
    },
    "context7": {
      "type": "local",
      "command": ["npx", "-y", "@upstash/context7-mcp"],
      "enabled": true
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

## Troubleshooting

### Server Not Loading

1. Check `npx` is available
2. Verify package name is correct
3. Check environment variables are set
4. Look at OpenCode logs for errors

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