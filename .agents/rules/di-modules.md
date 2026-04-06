---
description: GetIt DI module pattern — module registration, LazySingleton, use case wiring.
---

# DI Modules

## Module Location

`lib/bootstrap/modules/` — one module per feature domain.

## Module Pattern

```dart
import 'package:get_it/get_it.dart';

class StoreModule {
  StoreModule._();

  static void register(GetIt sl) {
    // Data sources
    sl.registerLazySingleton<StoreRemoteDataSource>(
      () => StoreRemoteDataSourceImpl(httpClient: sl()),
    );

    // Repository
    sl.registerLazySingleton<StoreRepository>(
      () => StoreRepositoryImpl(remoteDataSource: sl()),
    );

    // Use cases
    sl.registerLazySingleton(() => ListStoresUseCase(sl()));
    sl.registerLazySingleton(() => GetStoreUseCase(sl()));
    sl.registerLazySingleton(() => CreateStoreUseCase(sl()));
  }
}
```

## Rules

- Private constructor: `ModuleName._()` — prevents instantiation
- Static `register(GetIt sl)` method
- Registration order: DataSource → Repository → UseCases
- Use `registerLazySingleton` for all (created on first use)
- Use `sl()` to resolve dependencies (GetIt auto-resolves by type)

## Adding a New Module

1. Create `lib/bootstrap/modules/{name}_module.dart`
2. Register in `lib/bootstrap/di_container.dart`:

```dart
void setupDI() {
  // ...
  StoreModule.register(sl);
  NewFeatureModule.register(sl); // ← add here
}
```
