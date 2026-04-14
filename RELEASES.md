# Orchex Releases

Public release notes for [@wundam/orchex](https://www.npmjs.com/package/@wundam/orchex).

For full documentation, visit [orchex.dev](https://orchex.dev).

---

## [1.0.0-rc.27] — 2026-04-14

### Highlights
- Smarter self-healing: fix streams now receive explicit context about which of their files already exist on disk from prior attempts, reducing "Conflicts detected" errors on repeated fix attempts.
- Internal cleanup and stability improvements.

### Changes
- **fix**: Self-healer fix streams now include disk-state context in their prompts. When a stream is retried after a previous attempt left files partially written, the LLM is explicitly told which files exist and instructed to use `edit`/`replace` operations rather than `create`. This addresses a reported issue where grandchild fix streams (retries of retries) could fail with "Conflicts detected". **Note**: mitigation without a confirmed reproduction case — please report any recurrence so a deterministic fix can land in a future release.
- **chore**: Removed unused internal middleware that was never wired into the request pipeline.

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

---

## [1.0.0-rc.26] — 2026-04-06

### Highlights
- Fixed monorepo plan crashes — directory paths in stream reads no longer break execution
- Plans containing directory paths are now caught upfront with a clear warning
- New end-to-end regression coverage for monorepo workflows

### Changes
- **fix**: Plans no longer crash when the planner emits a directory path in a stream's `reads:` list. Directories are removed at validation time with a `path_removed` warning surfaced in the plan preview
- **fix**: Plans containing directory paths in `owns:` are now flagged with a `path_invalid` warning so you can correct the plan before running
- **fix**: The auto-plan prompt now explicitly tells the LLM that `reads:` and `owns:` must list specific files, not directory paths — reducing the upstream cause of the directory-in-reads regression introduced when monorepo conventions made the LLM more aware of package directories

### Notes
- The `workspace:*` protocol fix and plan fidelity improvements from rc.25 remain in place — rc.26 is now covered by automated regression tests

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

---

## [1.0.0-rc.25] — 2026-04-06

### Highlights
- Monorepo workspace support — pnpm, yarn, and npm workspace projects now work correctly
- Local package dependencies automatically use `workspace:*` protocol instead of `"*"`
- Plans follow your spec files more faithfully — type definitions, versions, and code copied verbatim

### Changes
- **feat**: Automatic monorepo detection — orchex detects pnpm workspaces, npm/yarn workspaces, and Bun workspaces. The planner instructs the LLM to use `workspace:*` for local packages and the correct filter/workspace commands for build and test
- **feat**: Local workspace package discovery — the planner knows which packages are local vs external, preventing the LLM from treating local packages as npm registry dependencies
- **fix**: Plan fidelity significantly improved — when you provide a spec or plan file, the LLM now copies type definitions, dependency versions, and package.json fields exactly as written instead of generating its own interpretation

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

---

## [1.0.0-rc.24] — 2026-04-06

### Highlights
- Automatic package manager detection — pnpm, yarn, and bun projects now work out of the box
- New `--plan-file` flag — bypass LLM plan generation and use your own hand-written plan
- Smarter error recovery — single-failure root-cause detection prevents wasted retries
- 4x more detail in error diagnostics

### Changes
- **feat**: Orchex now detects your package manager from lockfiles and generates correct verification commands. Previously all verify commands used `npm`, causing failures in pnpm/yarn/bun projects
- **feat**: `--plan-file` CLI flag and `plan_file` MCP parameter — feed a pre-written plan directly into the orchestration pipeline, skipping LLM plan generation entirely
- **feat**: Two-strike upstream root-cause detection — when a downstream stream fails due to bad upstream output, the first fix attempt gets enriched context. If it fails again with the same error, the system automatically fixes the upstream stream instead
- **feat**: Execution reports now include `errorRetryable` and `errorSelfHealable` fields for better failure diagnostics
- **fix**: Verify commands that reference files owned by other streams are now auto-corrected to scoped build checks instead of failing
- **fix**: Error messages in failure diagnostics now show full detail instead of being truncated
- **fix**: Blocked stream status now correctly reflects when dependency files exist on disk

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

---

## [1.0.0-rc.23] — 2026-04-05

### Highlights
- **Real-time execution progress** — see what every stream is doing as it runs, with status icons, wave progress, and elapsed time
- **Smarter self-healing** — when multiple streams fail from the same root cause, orchex fixes the upstream stream instead of retrying each one independently
- **Plan adherence** — orchex now follows your spec files verbatim instead of reinterpreting the code you provided
- **Failure correlation** — execution reports identify root-cause streams when downstream failures share the same error pattern

### Changes
- **feat**: `status` tool returns an `executionProgress` block during active execution — MCP hosts can relay formatted progress directly to users
- **feat**: `auto` and `execute` responses include polling guidance (15-30s interval) so LLM hosts automatically show progress
- **feat**: CLI prints real-time per-stream output during `orchex run` (stream started, completed, failed with timing)
- **feat**: Execution reports include failure correlation data identifying root-cause streams
- **fix**: Self-healer detects correlated downstream failures and generates a single root-cause fix stream instead of retrying each downstream independently
- **fix**: Auto-planner instructs the LLM to follow referenced plan/spec files verbatim
- **fix**: Verify commands validated — full test suite commands flagged on non-test streams

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

---

## [1.0.0-rc.22] — 2026-04-05

### Highlights
- Cloud sync fixed — run history now saves correctly (broken since rc.17)
- Ownership enforcement no longer rejects files the stream legitimately owns
- Error reports show real categories and actionable messages instead of "unknown"

### Changes
- **fix**: Cloud sync HTTP 500 resolved — run history persists to the cloud correctly. This bug persisted across 4 versions (rc.17–rc.21)
- **fix**: Ownership violations no longer trigger on files a stream explicitly owns. Path formatting differences from LLMs no longer cause false rejections
- **fix**: Ownership violation messages now include the stream's owned files list for easier debugging
- **fix**: Error reports now show real error categories (e.g., `runtime_error`, `network`, `auth_error`) instead of blanket "unknown" for most failure types
- **fix**: Failed streams retain their identity in reports — no more unnamed "unknown" entries in error output
- **fix**: When file rollback fails after a verify error, the failure is now surfaced in the error message with guidance to manually check affected files

### Upgrade
```bash
npm install -g @wundam/orchex@latest
# or
npx @wundam/orchex@latest
```

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
