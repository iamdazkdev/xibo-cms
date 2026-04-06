---
description: Create a new BLoC with events, states, use case injection, DI, and tests.
---

# Add BLoC Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-core-engineer.md` and strictly adopt that persona!

Create a complete BLoC from scratch.

## Prerequisites

- Read `flutter-bloc` skill for patterns
- Use case and repository must exist first

## Steps

1. **Create event file** → `{name}_event.dart`
   - Sealed class + Equatable
   - FetchRequested, RefreshRequested events (minimum)
   - Additional events as needed (Submit, Delete, etc.)

2. **Create state file** → `{name}_state.dart`
   - Sealed class + Equatable
   - Initial, Loading, Loaded, Error states (minimum)

3. **Create BLoC file** → `{name}_bloc.dart`
   - Inject use cases via constructor
   - Register event handlers in constructor
   - Use `switch` on `Result<T>` — no try/catch
   - Use `failure.message` passthrough — no hardcoded strings

4. **Register in DI module**
   - UseCases: `registerLazySingleton`
   - BLoC: `registerFactory`

5. **Wire in page**:

```dart
BlocProvider(
  create: (_) => sl<FooBloc>()..add(const FooFetchRequested()),
  child: const FooPage(),
)
```

// turbo

6. **Verify**:

```bash
flutter analyze
```

7. **Write tests** (see `flutter-testing` skill):
   - Initial state test
   - Fetch success/failure tests
   - Refresh test

// turbo

8. **Run tests**:

```bash
flutter test test/features/{feature}/presentation/bloc/{name}_bloc_test.dart
```
