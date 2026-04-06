---
description: Flutter security, Git workflow, dependencies, terminology, and naming conventions
---
# Flutter AI Rules - Conventions & Security

## Security Rules

### Before ANY Commit
- [ ] No hardcoded secrets (API keys, passwords, tokens, base URLs)
- [ ] All user inputs validated before dispatch
- [ ] Secure storage for tokens (`flutter_secure_storage`)
- [ ] Certificate pinning for production APIs
- [ ] No sensitive data in logs or error messages
- [ ] Authentication state verified before protected operations
- [ ] Environment config separates dev/staging/prod

### Secret Management
- NEVER hardcode secrets in source code.
- ALWAYS use environment variables or a secure config.
- Validate required secrets at startup.
- Use `flutter_secure_storage` for tokens, never `SharedPreferences`.
- Never log tokens, passwords, or PII.

## Forbidden Patterns

| Pattern | Why |
|---------|-----|
| `Cubit` in `features/` | Features require BLoC with explicit events (Cubit allowed only in `core/`) |
| `Provider` / `Riverpod` / `GetX` | BLoC is the only state management |
| `BuildContext` in BLoC | Framework-agnostic business logic |
| Mutable state in BLoC | Immutable states with `copyWith` only |
| Business logic in widgets | Widgets are presentation only |
| Direct data source access from BLoC | Must go through UseCase → Repository |
| Hardcoded API URLs | Use environment config |
| `print()` for logging | Use a logger service |
| Raw `http` package | Dio only |
| `setState` for shared/app-level state | BLoC only |
| God widgets (>300 lines) | Extract into sub-widgets |
| Barrel files re-exporting everything | Explicit imports per file |
| `dynamic` types | Use typed models and generics |
| `as` casting without null check | Use pattern matching or safe casts |
| `TODO` / `FIXME` without issue reference | Link to issue tracker |
| `late` without guaranteed initialization | Use nullable or factory pattern |
| Importing `data` layer from `presentation` | Only depend on `domain` |
| Nested `FutureBuilder` / `StreamBuilder` | Use BLoC for async state |

## Development Workflow

### Feature Implementation Workflow
1. **Plan First**: Analyze requirements, break into domain entities, use cases, repos.
2. **TDD Approach**: BLoC tests first -> Use case tests -> Repos -> Implementation.
3. **Code Review**: Verify layer boundaries, immutability, error flows, no forbidden patterns.
4. **Integration**: Register DI, AppRouter.
5. **Commit**: Conventional commits format.

### Git Commit Format
```
<type>: <description>

Types: feat, fix, refactor, docs, test, chore, perf, ci
```

## Use Case Rules
```dart
abstract class UseCase<Type, Params> {
  Future<Either<Failure, Type>> call(Params params);
}
```
- One use case = one business action.
- Always returns `Future<Either<Failure, T>>`.
- No framework imports. Pure Dart only.
- Params class is `Equatable`. Use `NoParams` when no input needed.
