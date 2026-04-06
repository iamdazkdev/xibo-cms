# Flutter AI Skills

Skills for deterministic, repeatable code generation in Flutter projects following Clean Architecture with BLoC.

---

## Skill: generate_feature

### When to Use

- Starting a new feature from scratch
- Adding a new domain to the application
- User says "Generate feature: <name>"

### Activation Trigger

```
Generate feature: <feature_name>
```

### What It Generates

```
lib/features/<feature_name>/
├── data/
│   ├── datasources/
│   │   ├── <feature>_remote_data_source.dart
│   │   └── <feature>_local_data_source.dart
│   ├── models/
│   │   └── <feature>_model.dart
│   └── repositories/
│       └── <feature>_repository_impl.dart
├── domain/
│   ├── entities/
│   │   └── <entity>.dart
│   ├── repositories/
│   │   └── <feature>_repository.dart
│   └── usecases/
│       └── <primary_usecase>.dart
└── presentation/
    ├── bloc/
    │   ├── <feature>_bloc.dart
    │   ├── <feature>_event.dart
    │   └── <feature>_state.dart
    ├── pages/
    │   └── <feature>_page.dart
    └── widgets/
        ├── <feature>_list_view.dart
        └── <feature>_error_view.dart
```

Plus tests:
```
test/features/<feature_name>/
├── data/
│   ├── models/<feature>_model_test.dart
│   └── repositories/<feature>_repository_impl_test.dart
├── domain/
│   └── usecases/<primary_usecase>_test.dart
└── presentation/
    └── bloc/<feature>_bloc_test.dart
```

### Generation Order

1. Domain entity
2. Domain repository interface
3. Domain use case
4. Data model (with `fromJson`, `toJson`, `toEntity`, `fromEntity`)
5. Data source interfaces + implementations (remote + local)
6. Data repository implementation
7. BLoC event
8. BLoC state
9. BLoC
10. Page + widgets
11. DI registration in `di_container.dart`
12. Route registration in `app_router.dart`
13. BLoC tests
14. Use case tests
15. Repository implementation tests
16. Model tests

### Template: Entity

```dart
// lib/features/{feature}/domain/entities/{entity}.dart
import 'package:equatable/equatable.dart';

class {Entity} extends Equatable {
  const {Entity}({
    required this.id,
    // required fields
  });

  final String id;
  // final typed fields

  @override
  List<Object?> get props => [id];
}
```

### Template: Repository Interface

```dart
// lib/features/{feature}/domain/repositories/{feature}_repository.dart
import 'package:dartz/dartz.dart';

import '../../../../core/error/failures.dart';
import '../entities/{entity}.dart';

abstract class {Feature}Repository {
  Future<Either<Failure, List<{Entity}>>> getAll();
  Future<Either<Failure, {Entity}>> getById({required String id});
  Future<Either<Failure, {Entity}>> create({required {Entity} entity});
  Future<Either<Failure, {Entity}>> update({required {Entity} entity});
  Future<Either<Failure, void>> delete({required String id});
}
```

### Template: Use Case

```dart
// lib/features/{feature}/domain/usecases/get_{feature}_list.dart
import 'package:dartz/dartz.dart';

import '../../../../core/error/failures.dart';
import '../../../../core/usecases/usecase.dart';
import '../entities/{entity}.dart';
import '../repositories/{feature}_repository.dart';

class Get{Feature}List implements UseCase<List<{Entity}>, NoParams> {
  const Get{Feature}List({required {Feature}Repository repository})
      : _repository = repository;

  final {Feature}Repository _repository;

  @override
  Future<Either<Failure, List<{Entity}>>> call(NoParams params) {
    return _repository.getAll();
  }
}
```

### Template: Model

```dart
// lib/features/{feature}/data/models/{feature}_model.dart
import 'package:equatable/equatable.dart';

import '../../domain/entities/{entity}.dart';

class {Entity}Model extends Equatable {
  const {Entity}Model({
    required this.id,
    // required fields
  });

  final String id;
  // final typed fields

  factory {Entity}Model.fromJson(Map<String, dynamic> json) {
    return {Entity}Model(
      id: json['id'] as String,
      // parse fields
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'id': id,
      // serialize fields
    };
  }

  {Entity} toEntity() {
    return {Entity}(
      id: id,
      // map fields
    );
  }

  factory {Entity}Model.fromEntity({Entity} entity) {
    return {Entity}Model(
      id: entity.id,
      // map fields
    );
  }

  @override
  List<Object?> get props => [id];
}
```

### Template: Remote Data Source

```dart
// lib/features/{feature}/data/datasources/{feature}_remote_data_source.dart
import 'package:dio/dio.dart';

import '../../../../core/error/exceptions.dart';
import '../models/{feature}_model.dart';

abstract class {Feature}RemoteDataSource {
  Future<List<{Entity}Model>> getAll();
  Future<{Entity}Model> getById({required String id});
  Future<{Entity}Model> create({required Map<String, dynamic> data});
  Future<{Entity}Model> update({required String id, required Map<String, dynamic> data});
  Future<void> delete({required String id});
}

class {Feature}RemoteDataSourceImpl implements {Feature}RemoteDataSource {
  const {Feature}RemoteDataSourceImpl({required Dio dio}) : _dio = dio;

  final Dio _dio;

  @override
  Future<List<{Entity}Model>> getAll() async {
    try {
      final response = await _dio.get('/{feature}');
      final list = response.data['data'] as List<dynamic>;
      return list
          .map((e) => {Entity}Model.fromJson(e as Map<String, dynamic>))
          .toList();
    } on DioException catch (e) {
      throw ServerException(
        message: e.response?.data['message'] as String? ?? 'Failed to fetch {feature}',
        statusCode: e.response?.statusCode,
      );
    }
  }

  @override
  Future<{Entity}Model> getById({required String id}) async {
    try {
      final response = await _dio.get('/{feature}/$id');
      return {Entity}Model.fromJson(
        response.data['data'] as Map<String, dynamic>,
      );
    } on DioException catch (e) {
      throw ServerException(
        message: e.response?.data['message'] as String? ?? 'Failed to fetch {feature}',
        statusCode: e.response?.statusCode,
      );
    }
  }

  @override
  Future<{Entity}Model> create({required Map<String, dynamic> data}) async {
    try {
      final response = await _dio.post('/{feature}', data: data);
      return {Entity}Model.fromJson(
        response.data['data'] as Map<String, dynamic>,
      );
    } on DioException catch (e) {
      throw ServerException(
        message: e.response?.data['message'] as String? ?? 'Failed to create {feature}',
        statusCode: e.response?.statusCode,
      );
    }
  }

  @override
  Future<{Entity}Model> update({
    required String id,
    required Map<String, dynamic> data,
  }) async {
    try {
      final response = await _dio.put('/{feature}/$id', data: data);
      return {Entity}Model.fromJson(
        response.data['data'] as Map<String, dynamic>,
      );
    } on DioException catch (e) {
      throw ServerException(
        message: e.response?.data['message'] as String? ?? 'Failed to update {feature}',
        statusCode: e.response?.statusCode,
      );
    }
  }

  @override
  Future<void> delete({required String id}) async {
    try {
      await _dio.delete('/{feature}/$id');
    } on DioException catch (e) {
      throw ServerException(
        message: e.response?.data['message'] as String? ?? 'Failed to delete {feature}',
        statusCode: e.response?.statusCode,
      );
    }
  }
}
```

### Template: Local Data Source

```dart
// lib/features/{feature}/data/datasources/{feature}_local_data_source.dart
import 'dart:convert';

import 'package:shared_preferences/shared_preferences.dart';

import '../../../../core/error/exceptions.dart';
import '../models/{feature}_model.dart';

abstract class {Feature}LocalDataSource {
  Future<List<{Entity}Model>> getCached();
  Future<void> cache(List<{Entity}Model> models);
  Future<void> clearCache();
}

class {Feature}LocalDataSourceImpl implements {Feature}LocalDataSource {
  const {Feature}LocalDataSourceImpl({
    required SharedPreferences sharedPreferences,
  }) : _sharedPreferences = sharedPreferences;

  final SharedPreferences _sharedPreferences;

  static const _cacheKey = 'cached_{feature}';

  @override
  Future<List<{Entity}Model>> getCached() async {
    final jsonString = _sharedPreferences.getString(_cacheKey);
    if (jsonString == null) {
      throw const CacheException(message: 'No cached data found');
    }
    final list = json.decode(jsonString) as List<dynamic>;
    return list
        .map((e) => {Entity}Model.fromJson(e as Map<String, dynamic>))
        .toList();
  }

  @override
  Future<void> cache(List<{Entity}Model> models) async {
    final jsonString = json.encode(models.map((m) => m.toJson()).toList());
    await _sharedPreferences.setString(_cacheKey, jsonString);
  }

  @override
  Future<void> clearCache() async {
    await _sharedPreferences.remove(_cacheKey);
  }
}
```

### Template: Repository Implementation

```dart
// lib/features/{feature}/data/repositories/{feature}_repository_impl.dart
import 'package:dartz/dartz.dart';

import '../../../../core/error/exceptions.dart';
import '../../../../core/error/failures.dart';
import '../../../../core/network/network_info.dart';
import '../../domain/entities/{entity}.dart';
import '../../domain/repositories/{feature}_repository.dart';
import '../datasources/{feature}_remote_data_source.dart';
import '../datasources/{feature}_local_data_source.dart';
import '../models/{feature}_model.dart';

class {Feature}RepositoryImpl implements {Feature}Repository {
  const {Feature}RepositoryImpl({
    required {Feature}RemoteDataSource remoteDataSource,
    required {Feature}LocalDataSource localDataSource,
    required NetworkInfo networkInfo,
  })  : _remoteDataSource = remoteDataSource,
        _localDataSource = localDataSource,
        _networkInfo = networkInfo;

  final {Feature}RemoteDataSource _remoteDataSource;
  final {Feature}LocalDataSource _localDataSource;
  final NetworkInfo _networkInfo;

  @override
  Future<Either<Failure, List<{Entity}>>> getAll() async {
    if (!await _networkInfo.isConnected) {
      try {
        final cached = await _localDataSource.getCached();
        return Right(cached.map((m) => m.toEntity()).toList());
      } on CacheException {
        return const Left(NetworkFailure('No internet connection'));
      }
    }
    try {
      final models = await _remoteDataSource.getAll();
      await _localDataSource.cache(models);
      return Right(models.map((m) => m.toEntity()).toList());
    } on ServerException catch (e) {
      return Left(ServerFailure(e.message));
    }
  }

  @override
  Future<Either<Failure, {Entity}>> getById({required String id}) async {
    if (!await _networkInfo.isConnected) {
      return const Left(NetworkFailure('No internet connection'));
    }
    try {
      final model = await _remoteDataSource.getById(id: id);
      return Right(model.toEntity());
    } on ServerException catch (e) {
      return Left(ServerFailure(e.message));
    }
  }

  @override
  Future<Either<Failure, {Entity}>> create({required {Entity} entity}) async {
    if (!await _networkInfo.isConnected) {
      return const Left(NetworkFailure('No internet connection'));
    }
    try {
      final model = await _remoteDataSource.create(
        data: {Entity}Model.fromEntity(entity).toJson(),
      );
      return Right(model.toEntity());
    } on ServerException catch (e) {
      return Left(ServerFailure(e.message));
    }
  }

  @override
  Future<Either<Failure, {Entity}>> update({required {Entity} entity}) async {
    if (!await _networkInfo.isConnected) {
      return const Left(NetworkFailure('No internet connection'));
    }
    try {
      final model = await _remoteDataSource.update(
        id: entity.id,
        data: {Entity}Model.fromEntity(entity).toJson(),
      );
      return Right(model.toEntity());
    } on ServerException catch (e) {
      return Left(ServerFailure(e.message));
    }
  }

  @override
  Future<Either<Failure, void>> delete({required String id}) async {
    if (!await _networkInfo.isConnected) {
      return const Left(NetworkFailure('No internet connection'));
    }
    try {
      await _remoteDataSource.delete(id: id);
      return const Right(null);
    } on ServerException catch (e) {
      return Left(ServerFailure(e.message));
    }
  }
}
```

### Template: DI Registration

```dart
// Append to bootstrap/di_container.dart

// -- {Feature} --
// Data Sources
sl.registerLazySingleton<{Feature}RemoteDataSource>(
  () => {Feature}RemoteDataSourceImpl(dio: sl<Dio>()),
);
sl.registerLazySingleton<{Feature}LocalDataSource>(
  () => {Feature}LocalDataSourceImpl(sharedPreferences: sl<SharedPreferences>()),
);

// Repositories
sl.registerLazySingleton<{Feature}Repository>(
  () => {Feature}RepositoryImpl(
    remoteDataSource: sl<{Feature}RemoteDataSource>(),
    localDataSource: sl<{Feature}LocalDataSource>(),
    networkInfo: sl<NetworkInfo>(),
  ),
);

// Use Cases
sl.registerLazySingleton(() => Get{Feature}List(repository: sl<{Feature}Repository>()));

// BLoCs
sl.registerFactory(() => {Feature}Bloc(get{Feature}List: sl<Get{Feature}List>()));
```

---

## Skill: generate_bloc

### When to Use

- Adding state management to a feature
- Creating a new interaction flow

### Activation Trigger

```
Generate BLoC: <feature_name>
```

### What It Generates

```
features/<feature>/presentation/bloc/
├── <feature>_bloc.dart
├── <feature>_event.dart
└── <feature>_state.dart
```

### Template: Event

```dart
part of '{feature}_bloc.dart';

sealed class {Feature}Event extends Equatable {
  const {Feature}Event();

  @override
  List<Object?> get props => [];
}

final class {Feature}Started extends {Feature}Event {
  const {Feature}Started();
}

final class {Feature}Refreshed extends {Feature}Event {
  const {Feature}Refreshed();
}
```

### Template: State

```dart
part of '{feature}_bloc.dart';

enum {Feature}Status { initial, loading, success, failure }

final class {Feature}State extends Equatable {
  const {Feature}State({
    this.status = {Feature}Status.initial,
    this.items = const [],
    this.errorMessage,
  });

  final {Feature}Status status;
  final List<{Entity}> items;
  final String? errorMessage;

  {Feature}State copyWith({
    {Feature}Status? status,
    List<{Entity}>? items,
    String? errorMessage,
  }) {
    return {Feature}State(
      status: status ?? this.status,
      items: items ?? this.items,
      errorMessage: errorMessage,
    );
  }

  @override
  List<Object?> get props => [status, items, errorMessage];
}
```

### Template: BLoC

```dart
import 'package:bloc/bloc.dart';
import 'package:equatable/equatable.dart';

import '../../../../core/usecases/usecase.dart';
import '../../domain/entities/{entity}.dart';
import '../../domain/usecases/get_{feature}_list.dart';

part '{feature}_event.dart';
part '{feature}_state.dart';

class {Feature}Bloc extends Bloc<{Feature}Event, {Feature}State> {
  {Feature}Bloc({required Get{Feature}List get{Feature}List})
      : _get{Feature}List = get{Feature}List,
        super(const {Feature}State()) {
    on<{Feature}Started>(_onStarted);
    on<{Feature}Refreshed>(_onRefreshed);
  }

  final Get{Feature}List _get{Feature}List;

  Future<void> _onStarted(
    {Feature}Started event,
    Emitter<{Feature}State> emit,
  ) async {
    emit(state.copyWith(status: {Feature}Status.loading));

    final result = await _get{Feature}List(const NoParams());

    result.fold(
      (failure) => emit(state.copyWith(
        status: {Feature}Status.failure,
        errorMessage: failure.message,
      )),
      (items) => emit(state.copyWith(
        status: {Feature}Status.success,
        items: items,
      )),
    );
  }

  Future<void> _onRefreshed(
    {Feature}Refreshed event,
    Emitter<{Feature}State> emit,
  ) async {
    final result = await _get{Feature}List(const NoParams());

    result.fold(
      (failure) => emit(state.copyWith(
        status: {Feature}Status.failure,
        errorMessage: failure.message,
      )),
      (items) => emit(state.copyWith(
        status: {Feature}Status.success,
        items: items,
      )),
    );
  }
}
```

---

## Skill: generate_usecase

### When to Use

- Adding a new business action to an existing feature
- Decomposing a complex BLoC into single-responsibility use cases

### Activation Trigger

```
Generate use case: <Action><Entity> in <feature>
```

### Template

```dart
import 'package:dartz/dartz.dart';
import 'package:equatable/equatable.dart';

import '../../../../core/error/failures.dart';
import '../../../../core/usecases/usecase.dart';
import '../entities/{entity}.dart';
import '../repositories/{feature}_repository.dart';

class {Action}{Entity} implements UseCase<{ReturnType}, {Action}{Entity}Params> {
  const {Action}{Entity}({required {Feature}Repository repository})
      : _repository = repository;

  final {Feature}Repository _repository;

  @override
  Future<Either<Failure, {ReturnType}>> call({Action}{Entity}Params params) {
    return _repository.{repositoryMethod}(/* pass params */);
  }
}

class {Action}{Entity}Params extends Equatable {
  const {Action}{Entity}Params({
    // required fields
  });

  // final typed fields

  @override
  List<Object?> get props => [/* fields */];
}
```

---

## Skill: generate_repository

### When to Use

- Creating data access layer for a feature
- Adding a new data contract

### Activation Trigger

```
Generate repository: <feature>
```

### Generates

```
features/<feature>/domain/repositories/<feature>_repository.dart       (interface)
features/<feature>/data/repositories/<feature>_repository_impl.dart    (implementation)
```

Use templates from generate_feature skill (Repository Interface + Implementation).

---

## Skill: generate_datasource

### When to Use

- Adding remote or local data access for a feature

### Activation Trigger

```
Generate data source: <feature> [remote|local|both]
```

Use templates from generate_feature skill (Remote Data Source + Local Data Source).

---

## Skill: generate_ui_from_spec

### When to Use

- Creating the presentation layer for a feature
- Adding a new screen with BLoC integration

### Activation Trigger

```
Generate UI: <feature> page with <description>
```

### Page Template

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:go_router/go_router.dart';

import '../../../../bootstrap/di_container.dart';
import '../bloc/{feature}_bloc.dart';
import '../widgets/{feature}_list_view.dart';
import '../widgets/{feature}_error_view.dart';

class {Feature}Page extends StatelessWidget {
  const {Feature}Page({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (_) => sl<{Feature}Bloc>()..add(const {Feature}Started()),
      child: const _{Feature}View(),
    );
  }
}

class _{Feature}View extends StatelessWidget {
  const _{Feature}View();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('{Feature}')),
      body: BlocConsumer<{Feature}Bloc, {Feature}State>(
        listenWhen: (previous, current) => previous.status != current.status,
        listener: (context, state) {
          if (state.status == {Feature}Status.failure) {
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(content: Text(state.errorMessage ?? 'An error occurred')),
            );
          }
        },
        builder: (context, state) {
          return switch (state.status) {
            {Feature}Status.initial => const SizedBox.shrink(),
            {Feature}Status.loading => const Center(child: CircularProgressIndicator()),
            {Feature}Status.success => {Feature}ListView(items: state.items),
            {Feature}Status.failure => {Feature}ErrorView(
                message: state.errorMessage ?? 'Something went wrong',
                onRetry: () => context.read<{Feature}Bloc>().add(const {Feature}Refreshed()),
              ),
          };
        },
      ),
    );
  }
}
```

### Error Widget Template

```dart
import 'package:flutter/material.dart';

class {Feature}ErrorView extends StatelessWidget {
  const {Feature}ErrorView({super.key, required this.message, required this.onRetry});

  final String message;
  final VoidCallback onRetry;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(24),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(Icons.error_outline, size: 48, color: Theme.of(context).colorScheme.error),
            const SizedBox(height: 16),
            Text(message, textAlign: TextAlign.center, style: Theme.of(context).textTheme.bodyLarge),
            const SizedBox(height: 24),
            FilledButton.icon(onPressed: onRetry, icon: const Icon(Icons.refresh), label: const Text('Retry')),
          ],
        ),
      ),
    );
  }
}
```

---

## Skill: refactor_feature

### When to Use

- An existing feature violates architecture rules
- Feature needs restructuring to follow Clean Architecture

### Activation Trigger

```
Refactor feature: <feature_name> to follow rules
```

### Process

1. **Audit** — Read all files in `features/<feature>/`
2. **Detect Violations** — Check against forbidden patterns
3. **Plan Refactor** — List all violations with file paths
4. **Generate Corrected Files** — Output complete corrected files
5. **Update DI** — Ensure registration matches new structure
6. **Generate Tests** — Produce tests for new classes

### Violation Report Format

```
REFACTOR AUDIT: <feature_name>
==============================

CRITICAL:
- [file:line] BuildContext used in BLoC
- [file:line] Mutable state field

HIGH:
- [file:line] Business logic in widget
- [file:line] Direct API call from BLoC

MEDIUM:
- [file:line] Missing use case layer
- [file:line] No Equatable on event class

Files to Modify: X
Files to Create: Y
Files to Delete: Z
```

---

## Skill: generate_bloc_tests

### When to Use

- After generating a BLoC
- When adding events to an existing BLoC

### Activation Trigger

```
Generate BLoC tests: <feature>
```

### BLoC Test Template

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:dartz/dartz.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';

import 'package:app/core/error/failures.dart';
import 'package:app/core/usecases/usecase.dart';
import 'package:app/features/{feature}/domain/entities/{entity}.dart';
import 'package:app/features/{feature}/domain/usecases/get_{feature}_list.dart';
import 'package:app/features/{feature}/presentation/bloc/{feature}_bloc.dart';

class MockGet{Feature}List extends Mock implements Get{Feature}List {}

void main() {
  late {Feature}Bloc bloc;
  late MockGet{Feature}List mockGet{Feature}List;

  setUp(() {
    mockGet{Feature}List = MockGet{Feature}List();
    bloc = {Feature}Bloc(get{Feature}List: mockGet{Feature}List);
  });

  tearDown(() => bloc.close());

  test('initial state is {Feature}State()', () {
    expect(bloc.state, equals(const {Feature}State()));
  });

  group('{Feature}Started', () {
    final testItems = [const {Entity}(id: '1'), const {Entity}(id: '2')];

    blocTest<{Feature}Bloc, {Feature}State>(
      'emits [loading, success] when getAll succeeds',
      setUp: () {
        when(() => mockGet{Feature}List(const NoParams()))
            .thenAnswer((_) async => Right(testItems));
      },
      build: () => {Feature}Bloc(get{Feature}List: mockGet{Feature}List),
      act: (bloc) => bloc.add(const {Feature}Started()),
      expect: () => [
        const {Feature}State(status: {Feature}Status.loading),
        {Feature}State(status: {Feature}Status.success, items: testItems),
      ],
      verify: (_) {
        verify(() => mockGet{Feature}List(const NoParams())).called(1);
      },
    );

    blocTest<{Feature}Bloc, {Feature}State>(
      'emits [loading, failure] when getAll fails',
      setUp: () {
        when(() => mockGet{Feature}List(const NoParams()))
            .thenAnswer((_) async => const Left(ServerFailure('Server error')));
      },
      build: () => {Feature}Bloc(get{Feature}List: mockGet{Feature}List),
      act: (bloc) => bloc.add(const {Feature}Started()),
      expect: () => [
        const {Feature}State(status: {Feature}Status.loading),
        const {Feature}State(status: {Feature}Status.failure, errorMessage: 'Server error'),
      ],
    );
  });
}
```

### Use Case Test Template

```dart
import 'package:dartz/dartz.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';

import 'package:app/core/error/failures.dart';
import 'package:app/core/usecases/usecase.dart';
import 'package:app/features/{feature}/domain/entities/{entity}.dart';
import 'package:app/features/{feature}/domain/repositories/{feature}_repository.dart';
import 'package:app/features/{feature}/domain/usecases/get_{feature}_list.dart';

class Mock{Feature}Repository extends Mock implements {Feature}Repository {}

void main() {
  late Get{Feature}List useCase;
  late Mock{Feature}Repository mockRepository;

  setUp(() {
    mockRepository = Mock{Feature}Repository();
    useCase = Get{Feature}List(repository: mockRepository);
  });

  test('should return list from repository', () async {
    final testItems = [const {Entity}(id: '1')];
    when(() => mockRepository.getAll()).thenAnswer((_) async => Right(testItems));

    final result = await useCase(const NoParams());

    expect(result, Right(testItems));
    verify(() => mockRepository.getAll()).called(1);
  });

  test('should return failure when repository fails', () async {
    when(() => mockRepository.getAll())
        .thenAnswer((_) async => const Left(ServerFailure('error')));

    final result = await useCase(const NoParams());

    expect(result, const Left(ServerFailure('error')));
  });
}
```

### Repository Test Template

```dart
import 'package:dartz/dartz.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';

import 'package:app/core/error/exceptions.dart';
import 'package:app/core/error/failures.dart';
import 'package:app/core/network/network_info.dart';
import 'package:app/features/{feature}/data/datasources/{feature}_remote_data_source.dart';
import 'package:app/features/{feature}/data/datasources/{feature}_local_data_source.dart';
import 'package:app/features/{feature}/data/models/{feature}_model.dart';
import 'package:app/features/{feature}/data/repositories/{feature}_repository_impl.dart';

class Mock{Feature}RemoteDataSource extends Mock implements {Feature}RemoteDataSource {}
class Mock{Feature}LocalDataSource extends Mock implements {Feature}LocalDataSource {}
class MockNetworkInfo extends Mock implements NetworkInfo {}

void main() {
  late {Feature}RepositoryImpl repository;
  late Mock{Feature}RemoteDataSource mockRemoteDataSource;
  late Mock{Feature}LocalDataSource mockLocalDataSource;
  late MockNetworkInfo mockNetworkInfo;

  setUp(() {
    mockRemoteDataSource = Mock{Feature}RemoteDataSource();
    mockLocalDataSource = Mock{Feature}LocalDataSource();
    mockNetworkInfo = MockNetworkInfo();
    repository = {Feature}RepositoryImpl(
      remoteDataSource: mockRemoteDataSource,
      localDataSource: mockLocalDataSource,
      networkInfo: mockNetworkInfo,
    );
  });

  group('getAll', () {
    test('should return NetworkFailure when offline and no cache', () async {
      when(() => mockNetworkInfo.isConnected).thenAnswer((_) async => false);
      when(() => mockLocalDataSource.getCached())
          .thenThrow(const CacheException(message: 'No cache'));

      final result = await repository.getAll();

      expect(result, const Left(NetworkFailure('No internet connection')));
    });

    test('should return cached data when offline', () async {
      when(() => mockNetworkInfo.isConnected).thenAnswer((_) async => false);
      when(() => mockLocalDataSource.getCached())
          .thenAnswer((_) async => [const {Entity}Model(id: '1')]);

      final result = await repository.getAll();

      expect(result.isRight(), true);
    });

    test('should return remote data and cache when online', () async {
      when(() => mockNetworkInfo.isConnected).thenAnswer((_) async => true);
      when(() => mockRemoteDataSource.getAll())
          .thenAnswer((_) async => [const {Entity}Model(id: '1')]);
      when(() => mockLocalDataSource.cache(any())).thenAnswer((_) async {});

      final result = await repository.getAll();

      expect(result.isRight(), true);
      verify(() => mockLocalDataSource.cache(any())).called(1);
    });

    test('should return ServerFailure when remote throws', () async {
      when(() => mockNetworkInfo.isConnected).thenAnswer((_) async => true);
      when(() => mockRemoteDataSource.getAll())
          .thenThrow(const ServerException(message: 'error'));

      final result = await repository.getAll();

      expect(result, const Left(ServerFailure('error')));
    });
  });
}
```

### Model Test Template

```dart
import 'package:flutter_test/flutter_test.dart';

import 'package:app/features/{feature}/data/models/{feature}_model.dart';
import 'package:app/features/{feature}/domain/entities/{entity}.dart';

void main() {
  const testModel = {Entity}Model(id: '1');
  const testJson = {'id': '1'};
  const testEntity = {Entity}(id: '1');

  group('fromJson', () {
    test('should return valid model', () {
      expect({Entity}Model.fromJson(testJson), equals(testModel));
    });
  });

  group('toJson', () {
    test('should return valid JSON', () {
      expect(testModel.toJson(), equals(testJson));
    });
  });

  group('toEntity', () {
    test('should return valid entity', () {
      final result = testModel.toEntity();
      expect(result, isA<{Entity}>());
      expect(result.id, equals('1'));
    });
  });

  group('fromEntity', () {
    test('should return valid model', () {
      expect({Entity}Model.fromEntity(testEntity), equals(testModel));
    });
  });
}
```
