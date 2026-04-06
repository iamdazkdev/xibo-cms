# Feature Generation Prompt

Generate a complete Flutter feature following Clean Architecture with BLoC.

## Input

Feature name: {feature_name}
Entity name: {entity_name}
Entity fields: {field_definitions}
API endpoint: {api_base_path}
Description: {feature_description}

## Required Output

Generate ALL of the following files in this exact order. Every file must be complete and compilable. No placeholders. No TODOs.

### Phase 1: Domain Layer
1. `lib/features/{feature}/domain/entities/{entity}.dart` — Equatable entity with const constructor, final fields
2. `lib/features/{feature}/domain/repositories/{feature}_repository.dart` — Abstract class with Either<Failure, T> returns
3. `lib/features/{feature}/domain/usecases/get_{feature}_list.dart` — UseCase<List<Entity>, NoParams>
4. Additional use cases as needed (create, update, delete, get by id)

### Phase 2: Data Layer
5. `lib/features/{feature}/data/models/{entity}_model.dart` — Equatable model with fromJson, toJson, toEntity, fromEntity
6. `lib/features/{feature}/data/datasources/{feature}_remote_data_source.dart` — Interface + Impl using Dio
7. `lib/features/{feature}/data/datasources/{feature}_local_data_source.dart` — Interface + Impl using SharedPreferences
8. `lib/features/{feature}/data/repositories/{feature}_repository_impl.dart` — Implements domain interface, catches exceptions, returns Either

### Phase 3: Presentation Layer
9. `lib/features/{feature}/presentation/bloc/{feature}_event.dart` — Sealed class, Equatable, const constructors
10. `lib/features/{feature}/presentation/bloc/{feature}_state.dart` — Final class, Equatable, copyWith, status enum
11. `lib/features/{feature}/presentation/bloc/{feature}_bloc.dart` — Constructor injection, on<Event>, result.fold
12. `lib/features/{feature}/presentation/pages/{feature}_page.dart` — BlocProvider + private _View with BlocConsumer
13. `lib/features/{feature}/presentation/widgets/{feature}_list_view.dart` — ListView.builder with RefreshIndicator
14. `lib/features/{feature}/presentation/widgets/{feature}_error_view.dart` — Error display with retry button

### Phase 4: Integration
15. DI registration block for `bootstrap/di_container.dart`
16. Route registration block for `core/routing/app_router.dart`

### Phase 5: Tests
17. `test/features/{feature}/presentation/bloc/{feature}_bloc_test.dart` — blocTest for each event (success + failure)
18. `test/features/{feature}/domain/usecases/get_{feature}_list_test.dart` — Success + failure paths
19. `test/features/{feature}/data/repositories/{feature}_repository_impl_test.dart` — Online, offline, cache, server error
20. `test/features/{feature}/data/models/{entity}_model_test.dart` — fromJson, toJson, toEntity, fromEntity

## Constraints

- Events: sealed class extending Equatable
- States: final class extending Equatable with copyWith
- BLoC: constructor injection of use cases, on<Event> registration, result.fold for Either
- No BuildContext in BLoC
- No business logic in widgets
- Error flow: DataSource throws Exception → Repository returns Left(Failure) → BLoC folds → Widget renders
- All constructors const where possible
- All fields final
- Naming: snake_case files, PascalCase classes, camelCase variables
