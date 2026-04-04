# Orchex Releases

Public release notes for [@wundam/orchex](https://www.npmjs.com/package/@wundam/orchex).

For full documentation, visit [orchex.dev](https://orchex.dev).

---

## [1.0.0-rc.21] — 2026-04-04

### Highlights
- Dynamic model registry — Orchex now auto-discovers available models from all 6 LLM providers daily, so you never hit "model not found" errors at runtime
- Key-aware smart routing — streams are only routed to providers where you have valid API keys configured
- Plan preview now shows the model and estimated cost per stream before you approve execution
- Smarter error recovery — infrastructure errors no longer waste tokens on futile fix retries
- Failed streams automatically roll back file changes, keeping your repo clean

### Changes
- **feat**: Dynamic model registry with server-side daily provider discovery and client-side cache (24h TTL). Models validated before execution
- **feat**: Plan preview enhanced with per-stream provider, model, and cost estimation
- **feat**: Key-aware routing ensures streams only go to providers with valid API keys
- **fix**: Execution reports now include full error messages and remediation suggestions instead of just error category names
- **fix**: Self-healing no longer triggers for infrastructure/transport errors — only code-level failures spawn fix streams
- **fix**: Verify failures now roll back file changes before generating fix streams
- **fix**: Dependent streams can proceed when a failed parent's output files exist on disk
- **fix**: Cloud sync reliability improved with better error handling

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

---

## [1.0.0-rc.20] — 2026-04-02

### Highlights
- Durable job queue — cloud orchestrations now survive server deploys and restarts
- Graceful shutdown — in-flight LLM calls complete before the server stops
- All provider limits removed — use as many LLM providers as you want on any tier

### Changes
- **feat**: Durable job queue for cloud mode — jobs persist across deploys and crashes with automatic recovery on startup
- **feat**: Graceful worker shutdown — in-flight LLM calls complete before process exits (30s drain timeout)
- **feat**: All tiers now support unlimited providers — the per-tier provider limit has been removed
- **fix**: Plan generation now respects tier stream limits during planning, not just post-generation
- **fix**: Cloud sync error responses now include actionable error details

### Upgrade
```bash
npm install -g @wundam/orchex@latest
```

---

## [1.0.0-rc.19] — 2026-04-02

### Highlights
- Fixed critical bug where `--provider` flag sent wrong model IDs to non-default providers
- Natural language intent now detects file paths (e.g., "Execute the plan in docs/plan.md")

### Changes
- **fix**: `--provider` flag now correctly routes model IDs to the specified provider — previously using `--provider openai` or `--provider gemini` caused 100% stream failure
- **fix**: Smart router model defaults synced across all providers
- **feat**: File paths in natural language intents are now auto-detected (e.g., `orchex run "implement docs/my-plan.md"`)

### Upgrade
```bash
npm install -g @wundam/orchex@latest
```

---

## [1.0.0-rc.18] — 2026-04-01

### Highlights
- Fixed premature timeouts in cloud mode

### Changes
- **fix**: Cloud mode streams now default to 10-minute timeout (was 2 minutes) — aligns with server-side limits. Local mode retains 2-minute default.

### Upgrade
```bash
npm install -g @wundam/orchex@latest
```

---

## [1.0.0-rc.17] — 2026-03-31

### Highlights
- Cloud mode now validates provider support before execution with clear error messages
- Better error reporting when cloud execution encounters issues

### Changes
- **feat**: Cloud provider validation — non-supported providers are rejected with an actionable error before execution starts, not during
- **feat**: CLI warns when configuring a provider that isn't supported in cloud mode
- **fix**: Cloud execution errors now show the actual model name and details instead of cryptic status codes

### Upgrade
```bash
npm install -g @wundam/orchex@latest
```

---

## [1.0.0-rc.16] — 2026-03-31

### Highlights
- npm package security hardened — reduced attack surface, sealed type boundaries, 4 automated CI guards
- Intelligence modules bundled for IP protection and smaller package size
- LLM auto-discovery — your AI assistant automatically learns how to use Orchex on connect
- `orchex setup` command auto-configures your IDE (Cursor, Windsurf, Claude Code)
- Execution resilience — smarter error categorization, wave-level circuit breaker, fix stream deduplication

### Changes
- **feat**: `orchex setup` — auto-detects your IDE and writes MCP configuration. Supports Cursor, Windsurf, Claude Code, and VS Code
- **feat**: MCP auto-context — your AI assistant receives usage instructions automatically on connect, no prompt engineering needed
- **feat**: 8 on-demand documentation resources via `orchex://` URIs for deep guides
- **feat**: 3 new error categories (model not found, auth error, server error) — infrastructure errors no longer trigger self-healing
- **feat**: Wave-level circuit breaker — halts execution when most streams fail with the same error, with diagnostic guidance
- **feat**: Fix stream deduplication — only one fix chain per failed stream
- **fix**: Cloud executor packaging — cloud API client correctly included in npm package
- **fix**: Stripe SDK compatibility updated for latest API version

### Upgrade
```bash
npm install -g @wundam/orchex@latest
```
