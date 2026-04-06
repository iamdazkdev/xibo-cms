---
description: Git workflow — conventional commits, grouped commits by phase, branch strategy.
---

# Git Workflow

## Branch Strategy

- `dev` — development branch
- `dazk_dev` — personal development branch
- Feature branches as needed

## Commit Format

Conventional commits with `(fe)` scope for frontend:

```
type(fe): short description

Optional body with details
```

### Types

| Type | When |
|---|---|
| `feat(fe)` | New feature |
| `fix(fe)` | Bug fix |
| `refactor(fe)` | Code restructuring (no behavior change) |
| `test(fe)` | Adding/updating tests |
| `docs(fe)` | Documentation changes |
| `style(fe)` | Formatting, no code change |
| `chore(fe)` | Build, config, tooling |

## Grouped Commits

When making large changes, group commits by phase:

```bash
# 1. Domain layer
git add lib/features/*/domain/
git commit --no-gpg-sign -m "refactor(fe): extract domain layer"

# 2. BLoC wiring
git add lib/features/*/presentation/bloc/
git commit --no-gpg-sign -m "refactor(fe): wire use cases into BLoCs"

# 3. Tests
git add test/
git commit --no-gpg-sign -m "test(fe): add BLoC tests"
```

## Pre-commit Checks

Always run before committing:

```bash
flutter analyze    # Must be 0 issues
flutter test       # Must all pass
```
