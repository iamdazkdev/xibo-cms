---
description: Error handling — Result sealed class, Failure types, failure.message passthrough, l10n in UI.
---

# Error Handling

## Result Pattern

Located at `core/infra/error/result.dart`:

```dart
sealed class Result<T> {
  const Result();
}

final class Success<T> extends Result<T> {
  const Success(this.data);
  final T data;
}

final class Err<T> extends Result<T> {
  const Err(this.failure);
  final Failure failure;
}
```

## Failure Types

```dart
sealed class Failure {
  const Failure({this.message = 'An unexpected error occurred'});
  final String message;
}

class ServerFailure extends Failure { ... }
class NetworkFailure extends Failure { ... }
class TimeoutFailure extends Failure { ... }
class UnauthorizedFailure extends Failure { ... }
class ForbiddenFailure extends Failure { ... }
class RateLimitFailure extends Failure { ... }
class MaintenanceFailure extends Failure { ... }
```

## Rules

### In Repositories (data layer)
- Catch exceptions → return `Err(FailureType())`
- Never throw — always return `Result<T>`

### In BLoCs (presentation layer)
- ⛔ **NO `try/catch`** — Result already handles errors
- ⛔ **NO `_mapFailureToState`** — use `failure.message` directly
- ✅ Pass `failure.message` to error states

```dart
case Err(:final failure):
  emit(StoreListError(failure.message));
```

### In UI (pages/widgets)
- Map error states to l10n strings via `BlocListener`
- Use `context.l10n.errorXxx` for user-facing messages

## L10n Error Keys

Existing keys in ARB files:
- `errorUnexpected`, `errorTimeout`, `errorNoInternet`
- `errorSessionTitle`, `errorSessionMessage`
