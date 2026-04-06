---
description: BLoC patterns — Bloc vs Cubit rules, event/state conventions, Result pattern, no try/catch.
---

# BLoC Patterns

## Bloc vs Cubit (MANDATORY)

- **Bloc** — Use for ALL feature/business logic (auth, stores, campaigns, content)
- **Cubit** — Use ONLY for system-level concerns (theme toggle, locale, loading indicator)

⛔ NEVER use Cubit for feature logic.

## Event Convention

```dart
// Use sealed class + Equatable
sealed class StoreListEvent extends Equatable {
  const StoreListEvent();
  @override
  List<Object?> get props => [];
}

final class StoreListFetchRequested extends StoreListEvent {
  const StoreListFetchRequested({this.search, this.page = 1});
  final String? search;
  final int page;
  @override
  List<Object?> get props => [search, page];
}
```

## State Convention

```dart
sealed class StoreListState extends Equatable {
  const StoreListState();
  @override
  List<Object?> get props => [];
}

final class StoreListInitial extends StoreListState { const StoreListInitial(); }
final class StoreListLoading extends StoreListState { const StoreListLoading(); }
final class StoreListLoaded extends StoreListState {
  const StoreListLoaded({required this.stores, required this.total});
  final List<Store> stores;
  final int total;
  @override
  List<Object?> get props => [stores, total];
}
final class StoreListError extends StoreListState {
  const StoreListError(this.message);
  final String message;
  @override
  List<Object?> get props => [message];
}
```

## Result Pattern (MANDATORY)

BLoCs use `switch` pattern matching on `Result<T>`:

```dart
final result = await _listStores(params);
switch (result) {
  case Success(:final data):
    emit(StoreListLoaded(stores: data.items, total: data.total));
  case Err(:final failure):
    emit(StoreListError(failure.message));
}
```

## Rules

- ⛔ **NO `try/catch`** in BLoCs — `Result` handles errors
- ⛔ **NO `_mapFailureToState`** — use `failure.message` passthrough
- ⛔ **NO hardcoded error strings** — errors come from `Failure.message`
- ✅ Constructor injection via use cases
- ✅ `LoadingCubit` for global loading overlay (show/hide)
