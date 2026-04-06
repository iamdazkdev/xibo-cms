# Workspace Boundary — rmn_app ≠ rmn-pm

## Rule

You are working in `app/frontend/rmn_app/` — the **Flutter application**.
Do NOT read from, write to, or modify any file inside `app/frontend/rmn-pm/`.

## Forbidden Paths

- `app/frontend/rmn-pm/artifacts/` — PM planning artifacts
- `app/frontend/rmn-pm/docs/` — PM documentation
- `app/frontend/rmn-pm/scripts/` — PM scripts
- `app/frontend/rmn-pm/.env` — PM environment config
- Any path containing `rmn-pm`

## ClickUp CLI

The ClickUp CLI is self-contained in `app/frontend/rmn_app/clickup/` with its own `.clickup_env`.
It defaults to **remote-only mode** — no local file modifications.

```bash
# ✅ CORRECT — uses rmn_app's own script + config
./app/frontend/rmn_app/clickup/clickup_dev.sh <command>

# ❌ WRONG — never use rmn-pm paths
../rmn-pm/docs/team-processes/dev.sh <command>
```

## Why

`rmn-pm` is a separate Project Management repo that lives next to `rmn_app` under `app/frontend/`.
The script reads story files from `rmn-pm` for ID resolution (read-only) but never writes to them.
