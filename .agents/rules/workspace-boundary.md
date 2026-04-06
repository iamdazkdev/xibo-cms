# Workspace Boundary Rule

## CRITICAL: This is `rmn_app` (Flutter App) — NOT `rmn-pm` (Project Management)

You are working in `app/frontend/rmn_app/` — the **Flutter application** codebase.

### ❌ FORBIDDEN — Do NOT touch these paths

- `app/frontend/rmn-pm/` — This is the Project Management repo
- `app/frontend/rmn-pm/artifacts/` — PM planning artifacts
- `app/frontend/rmn-pm/.env` — PM environment config
- `app/frontend/rmn-pm/docs/` — PM documentation

### ✅ ALLOWED — Your workspace

- `app/frontend/rmn_app/lib/` — Flutter source code
- `app/frontend/rmn_app/test/` — Flutter tests
- `app/frontend/rmn_app/clickup/` — ClickUp CLI (self-contained)
- `app/frontend/rmn_app/.agents/` — Agent config
- `app/frontend/rmn_app/.claude/` — Claude config

### ClickUp CLI

The CLI is self-contained in `app/frontend/rmn_app/clickup/` with its own API key config.
It defaults to **REMOTE_ONLY=1** — only ClickUp API calls, no local file modifications.

```bash
# ✅ CORRECT
./app/frontend/rmn_app/clickup/clickup_dev.sh start STORY-1.4
./app/frontend/rmn_app/clickup/clickup_dev.sh update STORY-1.4 "message"
./app/frontend/rmn_app/clickup/clickup_dev.sh my sprint-02

# ❌ WRONG — never use rmn-pm paths
../rmn-pm/docs/team-processes/dev.sh start STORY-1.4
```

### What REMOTE_ONLY=1 means (default behavior)

| Operation | Behavior |
|---|---|
| ClickUp API (status, comment) | ✅ Works |
| Read local files (ID lookup) | ✅ Works |
| `sed -i` local frontmatter | ❌ Skipped |
| `_add_local_update` | ❌ Skipped |
| `push` / `pull` commands | ❌ Disabled |
