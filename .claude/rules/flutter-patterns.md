---
paths: ['app/frontend/**/*.dart']
description: Flutter/Dart architecture patterns and conventions for the RMN frontend app
---

# Flutter / Dart Patterns

## Clean Architecture Layers (MANDATORY)

```
presentation (pages, widgets, blocs)
    ↓
domain (entities, repositories interfaces, usecases)
    ↓
data (models, data sources, repository implementations)
```

## State Management Convention (MANDATORY)

- **Bloc** — Use for ALL feature/business logic (e.g. profile, campaigns, auth, stores).
  Events give explicit traceability; always prefer `Bloc` for feature flows.
- **Cubit** — Use ONLY for system-level concerns (e.g. dark/light theme, locale switching).
  These are simple toggle/value holders with no complex event streams.

⛔ NEVER use Cubit for feature logic — always use Bloc with proper Events and States.

## Model & Entity Convention (MANDATORY)

Every API model **MUST** follow this 2-layer pattern:

### 1. Domain Entity (`domain/entities/`)
- Pure Dart class, no serialization logic
- Extend `Equatable` with `props` for value equality
- No dependency on any package except `equatable`

```dart
import 'package:equatable/equatable.dart';

class Foo extends Equatable {
  const Foo({required this.id, required this.name, this.status});

  final String id;
  final String name;
  final String? status;

  @override
  List<Object?> get props => [id, name, status];
}
```

### 2. Data Model (`data/models/`)
- Extend the domain entity
- Use `@JsonSerializable(fieldRename: FieldRename.snake)` for JSON mapping
- Include `part 'xxx_model.g.dart';` for code generation
- Provide `fromJson` factory and `toJson` method via generated code
- Pass fields to super constructor, do NOT re-declare fields

```dart
import 'package:json_annotation/json_annotation.dart';
import '../../domain/entities/foo.dart';

part 'foo_model.g.dart';

@JsonSerializable(fieldRename: FieldRename.snake)
class FooModel extends Foo {
  const FooModel({
    required super.id,
    required super.name,
    super.status,
  });

  factory FooModel.fromJson(Map<String, dynamic> json) =>
      _$FooModelFromJson(json);

  Map<String, dynamic> toJson() => _$FooModelToJson(this);
}
```

⛔ NEVER:
- Write manual `fromJson` with `json['field']` casts — always use `json_serializable`
- Put serialization logic in the domain entity
- Skip the entity and use the model directly in domain/presentation layers
- Re-declare entity fields in the model — use `super.fieldName` instead

After creating/modifying models, always run:
```bash
dart run build_runner build --delete-conflicting-outputs
```

## Data Source Convention

```dart
abstract class FooRemoteDataSource {
  Future<FooModel> getFoo(String id);
}

class FooRemoteDataSourceImpl implements FooRemoteDataSource {
  FooRemoteDataSourceImpl({required AppHttpClient httpClient})
      : _httpClient = httpClient;

  final AppHttpClient _httpClient;
  static const _timeout = Duration(seconds: 10);
}
```

## DI Registration (GetIt)

Register data sources and repositories in module files under `bootstrap/modules/`:

```dart
class FooModule {
  FooModule._();
  static void register(GetIt sl) {
    sl.registerLazySingleton<FooRemoteDataSource>(
      () => FooRemoteDataSourceImpl(httpClient: sl()),
    );
    sl.registerLazySingleton<FooRepository>(
      () => FooRepositoryImpl(remoteDataSource: sl()),
    );
  }
}
```

## API Endpoints

All endpoint paths are centralized in `core/infra/network/api_endpoints.dart`:

```dart
class ApiEndpoints {
  ApiEndpoints._();
  static const String foos = '$_v1/foos';
  static String fooById(String id) => '$_v1/foos/$id';
}
```

## UI & Rendering (MANDATORY)

- **Remote Images:** ALL images loaded from a network URL MUST use `CachedNetworkImage` from the `cached_network_image` package.
  - ⛔ NEVER use Flutter's native `Image.network()`.
  - ✅ ALWAYS specify `memCacheHeight` or `memCacheWidth` to prevent out-of-memory errors and scrolling lag.
