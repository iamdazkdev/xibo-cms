---
name: flutter-bloc
description: 'BLoC creation patterns for RMN Flutter app. Use when: creating new BLoCs, modifying events/states, wiring use cases.'
---

# Flutter BLoC Skill

## Creating a New BLoC

### Step 1: Create Event File (`{name}_event.dart`)

```dart
part of '{name}_bloc.dart';

sealed class FooEvent extends Equatable {
  const FooEvent();
  @override
  List<Object?> get props => [];
}

final class FooFetchRequested extends FooEvent {
  const FooFetchRequested({this.search, this.page = 1, this.pageSize = 20});
  final String? search;
  final int page;
  final int pageSize;
  @override
  List<Object?> get props => [search, page, pageSize];
}

final class FooRefreshRequested extends FooEvent {
  const FooRefreshRequested();
}
```

### Step 2: Create State File (`{name}_state.dart`)

```dart
part of '{name}_bloc.dart';

sealed class FooState extends Equatable {
  const FooState();
  @override
  List<Object?> get props => [];
}

final class FooInitial extends FooState { const FooInitial(); }
final class FooLoading extends FooState { const FooLoading(); }
final class FooLoaded extends FooState {
  const FooLoaded({required this.items, required this.total});
  final List<FooEntity> items;
  final int total;
  @override
  List<Object?> get props => [items, total];
}
final class FooError extends FooState {
  const FooError(this.message);
  final String message;
  @override
  List<Object?> get props => [message];
}
```

### Step 3: Create BLoC File (`{name}_bloc.dart`)

```dart
import 'package:bloc/bloc.dart';
import 'package:equatable/equatable.dart';

import '../../../../core/infra/error/result.dart';
import '../../../../core/ui/loading/loading_cubit.dart';
import '../../domain/entities/foo.dart';
import '../../domain/usecases/list_foo_usecase.dart';

part '{name}_event.dart';
part '{name}_state.dart';

class FooBloc extends Bloc<FooEvent, FooState> {
  FooBloc({
    required ListFooUseCase listFooUseCase,
    required LoadingCubit loadingCubit,
  })  : _listFoo = listFooUseCase,
        _loading = loadingCubit,
        super(const FooInitial()) {
    on<FooFetchRequested>(_onFetchRequested);
    on<FooRefreshRequested>(_onRefreshRequested);
  }

  final ListFooUseCase _listFoo;
  final LoadingCubit _loading;

  Future<void> _onFetchRequested(
    FooFetchRequested event,
    Emitter<FooState> emit,
  ) async {
    emit(const FooLoading());
    final result = await _listFoo(ListFooParams(
      search: event.search,
      page: event.page,
      pageSize: event.pageSize,
    ));

    switch (result) {
      case Success(:final data):
        emit(FooLoaded(items: data.items, total: data.total));
      case Err(:final failure):
        emit(FooError(failure.message));
    }
  }

  Future<void> _onRefreshRequested(
    FooRefreshRequested event,
    Emitter<FooState> emit,
  ) async {
    _loading.show();
    final result = await _listFoo(const ListFooParams());

    switch (result) {
      case Success(:final data):
        emit(FooLoaded(items: data.items, total: data.total));
      case Err(:final failure):
        emit(FooError(failure.message));
    }
    _loading.hide();
  }
}
```

### Step 4: Register in DI Module

```dart
// In bootstrap/modules/foo_module.dart
sl.registerFactory(() => FooBloc(
  listFooUseCase: sl(),
  loadingCubit: sl(),
));
```

### Step 5: Write Tests

See `flutter-testing` skill for BLoC test patterns.

## Checklist

- [ ] Events: sealed class + Equatable + const constructors
- [ ] States: sealed class + Equatable + const constructors
- [ ] BLoC: inject use cases (not repos), use Result switch
- [ ] No try/catch, no hardcoded error strings
- [ ] DI: register as Factory (not singleton — BLoCs are disposable)
- [ ] Test: `bloc_test` + `mocktail`
