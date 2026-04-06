---
description: Clean Architecture 3-layer rules — dependency direction, layer responsibilities, import restrictions.
---

# Clean Architecture Rules

## Layer Dependency Direction (MANDATORY)

```
presentation → domain ← data
```

- `presentation/` depends on `domain/` only
- `data/` depends on `domain/` only
- `domain/` depends on NOTHING (pure Dart)

⛔ **NEVER**:
- Import `data/` from `presentation/`
- Import `presentation/` from `domain/` or `data/`
- Import models (JSON) from BLoCs or pages — use domain entities

## Layer Responsibilities

### Domain Layer (`domain/`)
- Pure business logic, no framework dependencies
- Entities — plain Dart classes, `Equatable` for value equality
- Repository interfaces — abstract classes defining contracts
- Use Cases — single-responsibility business operations, return `Result<T>`

### Data Layer (`data/`)
- API interaction, JSON serialization, caching
- Models — extend domain entities, add `@JsonSerializable`
- Data Sources — HTTP calls via `AppHttpClient`
- Repository implementations — implement domain interfaces

### Presentation Layer (`presentation/`)
- UI, state management, user interaction
- BLoCs — orchestrate use cases, emit states
- Pages — route targets, compose widgets
- Widgets — reusable UI components

## Use Case Pattern

```dart
class ListStoresUseCase {
  const ListStoresUseCase(this._repository);
  final StoreRepository _repository;

  Future<Result<StoreList>> call(ListStoresParams params) =>
      _repository.listStores(/* ... */);
}
```

- BLoCs inject use cases, **NOT** repositories directly
- Use cases return `Result<T>` — never throw exceptions
- One public method: `call()`
