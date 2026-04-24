# Claude Code Setup Guide

> Use ToAPIs as the API backend for Claude Code

## Prerequisites

Before getting started, make sure you have:

1. **Claude Code installed**

   Install the Claude Code CLI via npm or pnpm:

   ```bash theme={null}
   # npm
   npm install -g @anthropic-ai/claude-code

   # pnpm
   pnpm install -g @anthropic-ai/claude-code
   ```

   Or visit the [Claude Code official docs](https://docs.anthropic.com/claude-code) for the latest installation instructions.

2. **A ToAPIs API key**

   Sign in to the [ToAPIs Console](https://toapis.com/console/token) to obtain your API key (starts with `sk-`).

**Note:** If you don't have a ToAPIs account yet, register at [ToAPIs](https://toapis.com) first.

## Configuration

Claude Code supports multiple ways to configure a custom API endpoint. You can point it to ToAPIs as a drop-in replacement for the Anthropic API.

Claude Code uses two separate JSON files: **onboarding state** and **environment variables** must go in the correct file to take effect.

### Ensure onboarding is complete

In your home directory, set `~/.claude.json` (create the file if it does not exist):

```json theme={null}
{
  "hasCompletedOnboarding": true
}
```

This file is only for CLI state. Put your API token and base URL in `settings.json` below.

### Option 1: `settings.json` (Recommended)

Configure environment variables in `~/.claude/settings.json` (global) or `.claude/settings.json` (project-level). No shell config changes required:

```json theme={null}
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-xxxxxxxxxxxx",
    "ANTHROPIC_BASE_URL": "https://toapis.com"
  }
}
```

**Global config** (applies to all projects — recommended):

```bash theme={null}
mkdir -p ~/.claude
```

Then write the JSON above into `~/.claude/settings.json`.

**Project-level config** (applies only to the current project):

Create `.claude/settings.json` in your project root. Do not put your API key in project-level config; keep `ANTHROPIC_AUTH_TOKEN` in the global config and only set `ANTHROPIC_BASE_URL` at the project level:

```json theme={null}
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://toapis.com"
  }
}
```

> **Note:** Put `hasCompletedOnboarding` in `~/.claude.json` and `env` in `~/.claude/settings.json` or `.claude/settings.json`. Do not mix them in the same file.

### Option 2: Environment Variables

Best for temporary use or CI/CD pipelines.

#### macOS / Linux

Set temporarily for the current session:

```bash theme={null}
export ANTHROPIC_AUTH_TOKEN="sk-xxxxxxxxxxxx"
export ANTHROPIC_BASE_URL="https://toapis.com"
claude
```

Set permanently by adding the following to `~/.bashrc` or `~/.zshrc`:

```bash theme={null}
export ANTHROPIC_AUTH_TOKEN="sk-xxxxxxxxxxxx"
export ANTHROPIC_BASE_URL="https://toapis.com"
```

Then reload your shell:

```bash theme={null}
source ~/.zshrc  # or source ~/.bashrc
```

#### Windows

Set temporarily in PowerShell:

```powershell theme={null}
$env:ANTHROPIC_AUTH_TOKEN = "sk-xxxxxxxxxxxx"
$env:ANTHROPIC_BASE_URL = "https://toapis.com"
claude
```

Set permanently (user-level):

```powershell theme={null}
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "sk-xxxxxxxxxxxx", "User")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://toapis.com", "User")
```

### Option 3: `.env` File

Create a `.env` file in your project root:

```bash theme={null}
ANTHROPIC_AUTH_TOKEN=sk-xxxxxxxxxxxx
ANTHROPIC_BASE_URL=https://toapis.com
```

**Important:** Add `.env` to your `.gitignore` to avoid leaking your API key.

### Configuration Reference

| Variable                   | Value                                   |
| -------------------------- | --------------------------------------- |
| **ANTHROPIC\_AUTH\_TOKEN** | Your ToAPIs API key (`sk-xxxxxxxxxxxx`) |
| **ANTHROPIC\_BASE\_URL**   | `https://toapis.com`                    |

## Verify the Setup

Run the following to check your installation:

```bash theme={null}
claude --version
```

Then start Claude Code:

```bash theme={null}
claude
```

If configured correctly, Claude Code will connect through ToAPIs and respond normally.

## Recommended Models

The following Claude models are available through ToAPIs:

| Model Name        | Model ID                    | Description                                                |
| ----------------- | --------------------------- | ---------------------------------------------------------- |
| Claude Opus 4.6   | `claude-opus-4-6`           | Most capable, best for complex tasks                       |
| Claude Sonnet 4.6 | `claude-sonnet-4-6`         | Balanced performance and speed — recommended for daily use |
| Claude Haiku 4.5  | `claude-haiku-4-5-20251001` | Fastest, best for lightweight tasks                        |

To switch models, use the `/model` command inside Claude Code, or pass `--model` at startup:

```bash theme={null}
claude --model claude-sonnet-4-6
```

## Troubleshooting

### `Authentication error` or `401 Unauthorized`?

1. Check that `ANTHROPIC_AUTH_TOKEN` is set correctly and starts with `sk-`
2. Verify the key is active in the [ToAPIs Console](https://toapis.com/console/token)
3. Ensure your account has sufficient balance

### `Connection refused` or unable to connect?

1. Confirm `ANTHROPIC_BASE_URL` is set to `https://toapis.com`
2. Check your network connection and ensure `https://toapis.com` is reachable

### Check current environment variables

```bash theme={null}
echo $ANTHROPIC_AUTH_TOKEN
echo $ANTHROPIC_BASE_URL
```

### Common error codes

| Error                       | Cause                        | Fix                     |
| --------------------------- | ---------------------------- | ----------------------- |
| `401 Unauthorized`          | Invalid or expired API token | Generate a new API key  |
| `429 Too Many Requests`     | Rate limit exceeded          | Wait a moment and retry |
| `500 Internal Server Error` | Temporary server issue       | Retry in a few minutes  |
| `insufficient_quota`        | Insufficient account balance | Top up in the console   |

## Tips

### 1. Launch in a project directory

Navigate to your project folder and run `claude` — it will automatically pick up the project context:

```bash theme={null}
cd /your/project
claude
```

### 2. Useful commands

| Command             | Description                    |
| ------------------- | ------------------------------ |
| `/help`             | Show help                      |
| `/model`            | Switch model                   |
| `/clear`            | Clear the current conversation |
| `/exit` or `Ctrl+C` | Exit Claude Code               |

### 3. Working with code

Claude Code can read and modify project files. Common use cases:

* **Code review** — Ask Claude Code to review your code and suggest improvements
* **Code generation** — Describe what you need; Claude Code writes it directly to files
* **Debugging** — Paste error messages and get targeted fixes
* **Refactoring** — Let Claude Code restructure and clean up your code

## Support

If you encounter any issues:

* 📚 [ToAPIs Documentation](https://docs.toapis.com)
* 📚 [Claude Code Official Docs](https://docs.anthropic.com/claude-code)
* 💬 [Discord Community](https://discord.gg/hvnszCrJ73)
* 🐦 [Twitter @toapisai](https://x.com/toapisai)

***

\[## Get Started with ToAPIs

Sign up for ToAPIs, get your API key, and supercharge your coding with Claude Code!]\([https://toapis.com](https://toapis.com))
