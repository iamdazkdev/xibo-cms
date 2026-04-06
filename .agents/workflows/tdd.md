---
description: Start TDD workflow — write tests first, then implement. RED → GREEN → REFACTOR.
---

# TDD Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-core-engineer.md` and strictly adopt that persona!

Implement Test-Driven Development for Flutter features.

## Steps

### 1. Identify Test Cases

```dart
group('FooBloc', () {
  // Happy paths
  'emits [Loading, Loaded] when fetch succeeds'
  'emits [Loaded] when refresh succeeds'
  // Error paths
  'emits [Loading, Error] when fetch fails'
  // Edge cases
  'emits [Loading, Loaded] with empty list when no results'
});
```

### 2. RED — Write Failing Tests

- Write test file first using `bloc_test` + `mocktail`
- Cover: success, failure, loading states, edge cases

// turbo

```bash
flutter test test/features/{feature}/presentation/bloc/{name}_bloc_test.dart
# FAIL ✗ (bloc file doesn't exist yet)
```

### 3. GREEN — Make Tests Pass

- Write minimum code to pass each test
- Follow BLoC pattern exactly (events, states, Result switch)
- Don't over-engineer

// turbo

```bash
flutter test test/features/{feature}/presentation/bloc/{name}_bloc_test.dart
# PASS ✓
```

### 4. REFACTOR — Clean Up

- Extract duplicated logic
- Improve naming
- All tests still pass

// turbo

```bash
flutter test
# All PASS ✓
```

### 5. Verify

// turbo

```bash
flutter analyze
# No issues found!
```

## Test Types for RMN Flutter

| Layer | Test Type | What to Mock |
|---|---|---|
| BLoC | Unit | Use Cases (mock) |
| Repository | Unit | DataSource (mock) |
| Widget | Widget | BLoC (mock with mocktail) |

## Skills Reference

Read before starting TDD:
- `flutter-testing` — bloc_test, mocktail patterns
- `flutter-bloc` — BLoC structure, Result pattern
