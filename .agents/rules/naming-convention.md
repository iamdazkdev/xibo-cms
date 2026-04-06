---
description: Dart/Flutter naming conventions for files, classes, variables, and packages in RMN project.
---

# Naming Conventions

## Files

- `snake_case.dart` — always lowercase with underscores
- Suffix by type: `_bloc.dart`, `_event.dart`, `_state.dart`, `_page.dart`, `_model.dart`
- Test files: `{name}_test.dart` mirroring source path

| Type | Example |
|---|---|
| BLoC | `store_list_bloc.dart` |
| Event | `store_list_event.dart` (part of bloc) |
| State | `store_list_state.dart` (part of bloc) |
| Entity | `store.dart` |
| Model | `store_model.dart` |
| Data Source | `store_remote_data_source.dart` |
| Repository Interface | `store_repository.dart` (in `domain/`) |
| Repository Impl | `store_repository.dart` (in `data/`) |
| Use Case | `list_stores_usecase.dart` |
| Page | `supplier_store_list_page.dart` |
| Widget | `store_list_card.dart` |
| DI Module | `store_module.dart` |

## Classes

- `PascalCase` for all classes
- BLoC: `StoreListBloc`, not `StoreListCubit` (for features)
- Events: `StoreListFetchRequested`, `StoreListRefreshRequested`
- States: `StoreListInitial`, `StoreListLoading`, `StoreListLoaded`, `StoreListError`
- Use Cases: `ListStoresUseCase`, `GetStoreUseCase`
- Models: `StoreModel` (extends `Store` entity)

## Variables & Methods

- `camelCase` for all
- Private: prefix with `_`
- BLoC dependencies: `_listStores`, `_loading`
- Event handlers: `_onFetchRequested`, `_onRefreshRequested`

## API Endpoints

Centralized in `core/infra/network/api_endpoints.dart`:

```dart
class ApiEndpoints {
  ApiEndpoints._();
  static const String stores = '$_v1/stores';
  static String storeById(String id) => '$_v1/stores/$id';
}
```
