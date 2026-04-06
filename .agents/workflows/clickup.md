---
description: Manage ClickUp tasks via clickup_dev.sh CLI. View, start, update, review, complete tasks with natural language.
---

# ClickUp Task Management Workflow (rmn_app)

Interact with ClickUp tasks using the `clickup_dev.sh` CLI — self-contained in `rmn_app/clickup/`.

// turbo-all

## ⚠️ CRITICAL: This script is REMOTE-ONLY by default

The script defaults to `CLICKUP_REMOTE_ONLY=1`, meaning:

- ✅ ClickUp API calls are made (status changes, comments, subtask creation)
- ✅ Local story files are READ to resolve STORY IDs → ClickUp task IDs
- ❌ No `sed -i` modifications to any local files
- ❌ No `_add_local_update` writes
- ❌ `push` and `pull` commands are disabled

## Script & Config Location

```
app/frontend/rmn_app/clickup/
├── clickup_dev.sh      # CLI script
├── .clickup_env         # API key + DEV_NAME (gitignored)
├── README.md            # Usage docs
└── build_serve_flutter.sh
```

## When to Use

- User asks about their tasks or sprint progress
- User wants to start, update, or complete a task
- User wants to request a review or create subtasks

## Steps

1. **Parse user request** — Map natural language to the correct command:

| Intent | Command |
|---|---|
| View my tasks | `clickup_dev.sh my` or `clickup_dev.sh my sprint-02` |
| Start a task | `clickup_dev.sh start STORY-X.X` |
| Update progress | `clickup_dev.sh update STORY-X.X "message" [--commit]` |
| Complete a task | `clickup_dev.sh done STORY-X.X "message" [--commit] [--pr N]` |
| Request review | `clickup_dev.sh review STORY-X.X @person "message"` |
| Change status | `clickup_dev.sh status STORY-X.X <status>` |
| Create subtask | `clickup_dev.sh subtask STORY-X.X "[BE] name" @person` |

1. **Run the command** from the monorepo root:

```bash
./app/frontend/rmn_app/clickup/clickup_dev.sh <command> <args>
```

1. **Report result** — Summarize what happened on ClickUp.

## Valid Status Values

| CLI value | ClickUp status |
|---|---|
| `to-do` | TO DO |
| `in-design` | IN DESIGN |
| `in-progress` | IN DEVELOPMENT |
| `ready-for-dev` | READY FOR DEV |
| `in-test` | TESTING |
| `done` | SHIPPED |
| `cancelled` | CANCELLED |

## Examples

### "Xem task sprint 2 của tôi"

```bash
./app/frontend/rmn_app/clickup/clickup_dev.sh my sprint-02
```

### "Bắt đầu task 1.4"

```bash
./app/frontend/rmn_app/clickup/clickup_dev.sh start STORY-1.4
```

### "Tạo subtask BE cho 1.4, assign Leo, 4h"

```bash
./app/frontend/rmn_app/clickup/clickup_dev.sh subtask STORY-1.4 "[BE] Implementation" @leo --prio normal --time 4h
```

## Integration

After task management:

- @[/plan] — Plan the implementation for a started task
- @[/tdd] — Start coding with TDD
- @[/code-review] — Review before marking done
