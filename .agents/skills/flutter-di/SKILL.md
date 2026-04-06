---
name: flutter-di
description: 'GetIt DI patterns for RMN Flutter app. Use when: adding DI modules, wiring use cases, registering new services.'
---

# Flutter DI Skill

## Adding a New Feature Module

### Step 1: Create Module File

`lib/bootstrap/modules/{feature}_module.dart`:

```dart
import 'package:get_it/get_it.dart';
import '../../features/{feature}/data/datasources/{name}_remote_data_source.dart';
import '../../features/{feature}/data/repositories/{name}_repository.dart';
import '../../features/{feature}/domain/repositories/{name}_repository.dart';
import '../../features/{feature}/domain/usecases/list_{name}_usecase.dart';
import '../../features/{feature}/domain/usecases/get_{name}_usecase.dart';

class FeatureModule {
  FeatureModule._();

  static void register(GetIt sl) {
    // Data sources
    sl.registerLazySingleton<FooRemoteDataSource>(
      () => FooRemoteDataSourceImpl(httpClient: sl()),
    );

    // Repository
    sl.registerLazySingleton<FooRepository>(
      () => FooRepositoryImpl(remoteDataSource: sl()),
    );

    // Use cases
    sl.registerLazySingleton(() => ListFooUseCase(sl()));
    sl.registerLazySingleton(() => GetFooUseCase(sl()));
  }
}
```

### Step 2: Register in DI Container

`lib/bootstrap/di_container.dart`:

```dart
void setupDI() {
  CoreModule.register(sl);
  AuthModule.register(sl);
  StoreModule.register(sl);
  DeviceModule.register(sl);
  AdvertiserModule.register(sl);
  BusinessModule.register(sl);
  FeatureModule.register(sl);  // ← add here
}
```

## Registration Types

| Type | When | Example |
|---|---|---|
| `registerLazySingleton` | DataSources, Repos, UseCases | Created once on first use |
| `registerFactory` | BLoCs | New instance every time (disposable) |
| `registerSingleton` | Core services (HTTP client) | Created immediately |

## Resolving Dependencies

```dart
// In module
sl.registerLazySingleton(() => ListFooUseCase(sl()));
// sl() auto-resolves FooRepository by type

// In widget (for BLoC)
BlocProvider(
  create: (_) => sl<FooBloc>(),
  child: FooPage(),
)
```
