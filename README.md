# Orchex Community

> Describe what you want. Orchex plans, parallelizes, and executes — safely.

Community hub and Cursor plugin for [Orchex](https://orchex.dev) — autopilot AI orchestration for multi-file coding tasks.

## What is Orchex?

Orchex is an MCP server that orchestrates AI coding agents in parallel. Give it a task, and it auto-plans streams of work, executes them across waves with ownership enforcement (no file conflicts), and self-heals on failure. Supports 6 LLM providers with BYOK (Bring Your Own Key) and dynamic model discovery.

## Cursor Plugin

### Install from Marketplace

Search for **"Orchex"** in the Cursor Marketplace, or visit [cursor.com/marketplace](https://cursor.com/marketplace).

### Auto Setup

```bash
npx @wundam/orchex setup
```

Auto-detects your IDE and writes the MCP config. Works with Cursor, Windsurf, Claude Code, and VS Code.

### Manual Setup

Add to your Cursor MCP config (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "orchex": {
      "command": "npx",
      "args": ["-y", "@wundam/orchex"]
    }
  }
}
```

Orchex auto-detects API keys from your shell environment (`ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `GEMINI_API_KEY`, `DEEPSEEK_API_KEY`). No `env` block needed.

### Usage

1. Open Cursor and ask: *"Build a REST API with authentication and tests"*
2. Orchex generates a plan, shows you the streams, and executes on approval
3. Streams run in parallel waves — each stream owns its files, no conflicts

## What's New

**rc.21** — Dynamic model registry & error diagnostics:
- Auto-discovers available models from all 6 LLM providers — no more "model not found" errors
- Key-aware smart routing — streams only go to providers where you have valid API keys
- Plan preview shows model and estimated cost per stream before execution
- Smarter self-healing — infrastructure errors no longer waste tokens on futile fix retries
- Failed streams roll back file changes automatically, keeping your repo clean

See the [full release notes](RELEASES.md) or visit the [changelog](https://orchex.dev/docs/changelog).

## Community

Use [GitHub Discussions](https://github.com/wundam/orchex-community/discussions) for:

| Category | Purpose |
|----------|---------|
| **Announcements** | Release notes, breaking changes |
| **Bug Reports** | Issues you encounter during beta |
| **Feedback** | Feature requests, UX suggestions |
| **Q&A** | Setup help, troubleshooting |

## Links

- [orchex.dev](https://orchex.dev) — Website & docs
- [@wundam/orchex on npm](https://www.npmjs.com/package/@wundam/orchex) — Package
- [Integration guides](https://orchex.dev/docs/integrations) — Claude Code, Cursor, Windsurf, and more
