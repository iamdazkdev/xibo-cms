---
description: Run comprehensive code review before committing. Analysis + patterns + quality check.
---
# Code Review Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-qa-reviewer.md` and strictly adopt that persona!

Review Flutter code before committing — focus on Flutter specific pitfalls, state management rules, and quality.

## Steps

1. **Get changes**:
```bash
git diff --staged -- '*.dart'
```
If no staged changes: `git diff -- '*.dart'`

// turbo
2. **Run diagnostics**:
```bash
flutter analyze
flutter test
```

3. **Review against strict Flutter checklist**:
   - [ ] **BuildContext across async gaps**: Check for `if (!context.mounted) return;` after any `await` if `context` is used.
   - [ ] **State Leaks**: Ensure all `TextEditingController`, `FocusNode`, `ScrollController`, and `StreamSubscription` are correctly disposed in `dispose()`.
   - [ ] **Clean Architecture**: presentation → domain ← data (no cross-layer imports).
   - [ ] **BLoC Rules**: Ensure no navigation, dialogs, or `BuildContext` inside the BLoC class. It must be completely pure dart.
   - [ ] **DI & Routing**: Ensure `di_container.dart` is updated and `app_router.dart` uses `GoRoute` safely.

4. **Report findings** grouped by severity:
```
## Review Summary

| Severity | Count | Status |
|----------|-------|--------|
| CRITICAL | 0     | ✅     |
| HIGH     | 0     | ⚠️     |
| MEDIUM   | 0     | ℹ️     |

Verdict: [APPROVE / WARNING / BLOCK]
```
