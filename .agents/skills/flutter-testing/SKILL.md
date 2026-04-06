---
name: flutter-testing
description: 'Flutter testing patterns for RMN app. Use when: writing BLoC tests, widget tests, repository tests, mocking dependencies.'
---

# Flutter Testing Skill

## BLoC Testing Pattern

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';
import 'package:rmn_app/core/infra/error/result.dart';
import 'package:rmn_app/core/ui/loading/loading_cubit.dart';

// 1. Create mocks
class MockListFooUseCase extends Mock implements ListFooUseCase {}
class MockLoadingCubit extends Mock implements LoadingCubit {}

void main() {
  late FooBloc bloc;
  late MockListFooUseCase mockListFoo;
  late MockLoadingCubit mockLoading;

  // 2. Test data
  const tFoo = Foo(id: 'foo_001', name: 'Test');
  const tFooList = FooList(items: [tFoo], total: 1, page: 1, pageSize: 20);

  // 3. Register fallback values
  setUpAll(() {
    registerFallbackValue(const ListFooParams());
  });

  // 4. Setup
  setUp(() {
    mockListFoo = MockListFooUseCase();
    mockLoading = MockLoadingCubit();
    bloc = FooBloc(listFooUseCase: mockListFoo, loadingCubit: mockLoading);
  });

  tearDown(() => bloc.close());

  // 5. Tests
  group('FooBloc', () {
    test('initial state is FooInitial', () {
      expect(bloc.state, const FooInitial());
    });

    blocTest<FooBloc, FooState>(
      'emits [Loading, Loaded] when fetch succeeds',
      build: () {
        when(() => mockListFoo(any()))
            .thenAnswer((_) async => const Success(tFooList));
        return bloc;
      },
      act: (b) => b.add(const FooFetchRequested()),
      expect: () => [const FooLoading(), isA<FooLoaded>()],
    );

    blocTest<FooBloc, FooState>(
      'emits [Loading, Error] when fetch fails',
      build: () {
        when(() => mockListFoo(any())).thenAnswer(
          (_) async => const Err(ServerFailure(message: 'Failed')),
        );
        return bloc;
      },
      act: (b) => b.add(const FooFetchRequested()),
      expect: () => [const FooLoading(), isA<FooError>()],
    );

    blocTest<FooBloc, FooState>(
      'refresh shows/hides loading',
      build: () {
        when(() => mockListFoo(any()))
            .thenAnswer((_) async => const Success(tFooList));
        return bloc;
      },
      act: (b) => b.add(const FooRefreshRequested()),
      expect: () => [isA<FooLoaded>()],
      verify: (_) {
        verify(() => mockLoading.show()).called(1);
        verify(() => mockLoading.hide()).called(1);
      },
    );
  });
}
```

## Test File Location

Mirror source path:

```
lib/features/supplier/presentation/bloc/store_list_bloc.dart
test/features/supplier/presentation/bloc/store_list_bloc_test.dart
```

## Key Rules

- ⛔ **NEVER use `thenThrow`** — use `thenAnswer((_) async => Err(...))` for failure
- ✅ Use `registerFallbackValue` for all mock params in `setUpAll`
- ✅ Use `const` for test data when possible
- ✅ Test: initial state, success, failure, loading verification
- ✅ `seed` state for tests that need pre-existing state (e.g. update)

## Running Tests

```bash
# All tests
flutter test

# Specific feature
flutter test test/features/supplier/

# With coverage
flutter test --coverage
```
