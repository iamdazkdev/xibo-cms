---
description: Fix Flutter build/analyze errors incrementally. Parse, fix, verify in a loop.
---

# Build Fix Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-qa-reviewer.md` and strictly adopt that persona!

Fix `flutter analyze` or build errors incrementally until zero issues remain.

## Steps

// turbo
1. **Run analysis**:

```bash
flutter analyze 2>&1
```

2. **Parse errors** — Identify each issue:
   - File path
   - Line number
   - Error type (error, warning, info)
   - Description

3. **Fix the highest priority errors first**:
   - Errors before warnings before infos
   - Fix related errors together

4. **Fix each error** — Apply the correct fix

// turbo
5. **Re-run analysis**:

```bash
flutter analyze 2>&1
```

6. **Repeat** steps 2-5 until `No issues found!`

// turbo
7. **Run tests** to verify no regressions:

```bash
flutter test
```

## Common Fixes

| Error | Fix |
|---|---|
| Unused import | Remove the import |
| Missing override | Add `@override` annotation |
| Unused variable | Remove or use the variable |
| Type mismatch | Check entity vs model usage |
| Missing const | Add `const` keyword |
| Import cycle | Move file to correct layer |
