# Vertex AI Setup for OpenCode

Configure OpenCode to use Google Cloud Vertex AI for access to Gemini and Claude models.

## Prerequisites

1. Google Cloud project with Vertex AI API enabled
2. Service account with Vertex AI User role (or equivalent)
3. Service account JSON key file
4. OpenCode CLI installed

## Environment Variables

Set these before running OpenCode:

```bash
# Required
export GOOGLE_CLOUD_PROJECT="your-project-id"

# Authentication (choose one)
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
# OR use gcloud CLI authentication:
# gcloud auth application-default login

# Optional - defaults to 'global'
export VERTEX_LOCATION="us-central1"
```

**Tip:** Use `global` for the location to improve availability without extra cost.

## Configuration File

### Basic Vertex AI Setup

Create `~/.config/opencode/opencode.json` or project-level `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "google-vertex/gemini-2.5-pro",
  "provider": {
    "google-vertex": {
      "npm": "@ai-sdk/google-vertex",
      "options": {
        "project": "{env:GOOGLE_CLOUD_PROJECT}",
        "location": "{env:VERTEX_LOCATION}"
      },
      "models": {
        "gemini-2.5-pro": {
          "name": "Gemini 2.5 Pro",
          "limit": {
            "context": 1000000,
            "output": 65536
          }
        },
        "gemini-2.5-flash": {
          "name": "Gemini 2.5 Flash",
          "limit": {
            "context": 1000000,
            "output": 65536
          }
        }
      }
    }
  }
}
```

### Claude on Vertex AI

Claude models on Vertex AI require a different SDK package:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "vertex-anthropic/claude-sonnet-4",
  "provider": {
    "vertex-anthropic": {
      "npm": "@ai-sdk/google-vertex/anthropic",
      "options": {
        "project": "{env:GOOGLE_CLOUD_PROJECT}",
        "location": "us-east5"
      },
      "models": {
        "claude-sonnet-4": {
          "name": "Claude Sonnet 4 (Vertex)",
          "limit": {
            "context": 200000,
            "output": 8192
          }
        },
        "claude-opus-4": {
          "name": "Claude Opus 4 (Vertex)",
          "limit": {
            "context": 200000,
            "output": 8192
          }
        }
      }
    }
  }
}
```

**Important:** Claude on Vertex is only available in specific regions (us-east5, europe-west1).

## Service Account Setup

### Create Service Account

```bash
# Create service account
gcloud iam service-accounts create opencode-vertex \
    --display-name="OpenCode Vertex AI Access"

# Grant Vertex AI User role
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
    --member="serviceAccount:opencode-vertex@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
    --role="roles/aiplatform.user"

# Create and download key
gcloud iam service-accounts keys create ~/opencode-vertex-key.json \
    --iam-account=opencode-vertex@YOUR_PROJECT_ID.iam.gserviceaccount.com
```

### Set Credentials

```bash
export GOOGLE_APPLICATION_CREDENTIALS="$HOME/opencode-vertex-key.json"
```

## Using Variable Substitution

OpenCode supports environment variable substitution in config:

```json
{
  "provider": {
    "google-vertex": {
      "options": {
        "project": "{env:GOOGLE_CLOUD_PROJECT}",
        "location": "{env:VERTEX_LOCATION}"
      }
    }
  }
}
```

Also supports file references:

```json
{
  "provider": {
    "google-vertex": {
      "options": {
        "credentials": "{file:~/.secrets/vertex-credentials.json}"
      }
    }
  }
}
```

## Available Vertex AI Models

### Gemini Models

| Model ID | Description |
|----------|-------------|
| `gemini-2.5-pro` | Most capable Gemini model |
| `gemini-2.5-flash` | Fast, efficient for simple tasks |
| `gemini-2.0-flash-exp` | Experimental flash model |

### Claude Models (via Vertex)

| Model ID | Description |
|----------|-------------|
| `claude-sonnet-4` | Balanced performance |
| `claude-opus-4` | Most capable Claude |
| `claude-haiku-4` | Fast, cost-effective |

## Verification

Test the configuration:

```bash
# Set environment
export GOOGLE_CLOUD_PROJECT="your-project"
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"

# Test with a simple prompt
opencode run --model google-vertex/gemini-2.5-flash "Hello, respond with OK"
```

## Troubleshooting

### Authentication Errors

```
Error: Could not load the default credentials
```

**Fix:** Ensure `GOOGLE_APPLICATION_CREDENTIALS` points to valid service account JSON, or run `gcloud auth application-default login`.

### Permission Denied

```
Error: Permission 'aiplatform.endpoints.predict' denied
```

**Fix:** Grant the service account the `roles/aiplatform.user` role.

### Region Not Available

```
Error: Model not available in region
```

**Fix:** Claude models are only in specific regions. Use `us-east5` or `europe-west1` for Claude. Gemini is available in most regions.

### Model Not Found

```
Error: Model 'xyz' not found
```

**Fix:** Verify the model ID matches Vertex AI naming. Use the `/models` command in OpenCode TUI to see available models.

## Cost Considerations

- Vertex AI pricing differs from direct API access
- Consider using `gemini-2.5-flash` for cost-sensitive operations
- Claude on Vertex may have different pricing than direct Anthropic API
- Use `small_model` config option for lightweight tasks
