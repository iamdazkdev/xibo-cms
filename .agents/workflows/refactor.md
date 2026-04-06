---
description: Safe refactoring with verification at each step. No behavior changes.
---

# Refactor Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-qa-reviewer.md` and strictly adopt that persona!

Refactor Flutter code safely with verification at each step.

## When to Use

- Extracting domain layers
- Splitting large files
- Removing dead code
- Cleaning up patterns (try/catch → Result)

## Steps

1. **Identify scope** — What files/classes will be refactored?

2. **Verify baseline** — Ensure clean state before starting:

// turbo

```bash
flutter analyze && flutter test
```

3. **Make changes** — One logical group at a time:
   - Extract entity → verify
   - Update imports → verify
   - Remove old code → verify

4. **Verify after each change**:

// turbo

```bash
flutter analyze
```

5. **Run tests** after all changes:

// turbo

```bash
flutter test
```

6. **Commit** with descriptive message:

```bash
git add <changed files>
git commit --no-gpg-sign -m "refactor(fe): <description>"
```

## Safe Refactoring Checklist

- [ ] No behavior changes — only structure
- [ ] All imports updated after moves
- [ ] No unused imports remain
- [ ] Tests still pass (53+ tests)
- [ ] `flutter analyze` → 0 issues
- [ ] File sizes reduced (target < 500 lines)

## Common Refactoring Patterns

| Pattern | Before | After |
|---|---|---|
| Extract domain | Repo returns model | Repo returns entity via Result |
| Add use case | BLoC calls repo | BLoC calls use case |
| Split file | 800+ lines | 2-3 files < 500 lines |
| Remove try/catch | try/catch in BLoC | Result switch |
| l10n strings | Hardcoded "Error" | failure.message passthrough |
