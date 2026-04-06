---
description: Failure hierarchy, Exceptions, and Error flow mapping
---
# Flutter AI Rules - Error Handling

## Error Handling Rules

### Failure Hierarchy

```dart
// core/error/failures.dart
sealed class Failure extends Equatable {
  const Failure(this.message);

  final String message;

  @override
  List<Object?> get props => [message];
}

final class ServerFailure extends Failure {
  const ServerFailure(super.message);
}

final class NetworkFailure extends Failure {
  const NetworkFailure(super.message);
}

final class CacheFailure extends Failure {
  const CacheFailure(super.message);
}

final class ValidationFailure extends Failure {
  const ValidationFailure(super.message);
}

final class UnauthorizedFailure extends Failure {
  const UnauthorizedFailure(super.message);
}
```

### Exception Hierarchy

```dart
// core/error/exceptions.dart
class ServerException implements Exception {
  const ServerException({required this.message, this.statusCode});

  final String message;
  final int? statusCode;
}

class CacheException implements Exception {
  const CacheException({required this.message});

  final String message;
}

class UnauthorizedException implements Exception {
  const UnauthorizedException({required this.message});

  final String message;
}
```

### Error Flow

```
DataSource throws Exception
  → Repository catches Exception, returns Left(Failure)
    → UseCase passes Either through
      → BLoC folds Either into State
        → Widget renders error from State
```

- DataSources throw **Exceptions**.
- Repositories catch Exceptions and return **Failures** via `Either`.
- BLoCs never try/catch. They fold `Either`.
- UI renders `state.errorMessage`. No raw exceptions shown to user.
