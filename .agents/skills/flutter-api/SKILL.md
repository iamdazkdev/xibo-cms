---
name: flutter-api
description: 'HTTP client and data source patterns for RMN Flutter app. Use when: making API calls, creating data sources, defining endpoints.'
---

# Flutter API Skill

## API Endpoints

Centralized in `lib/core/infra/network/api_endpoints.dart`:

```dart
class ApiEndpoints {
  ApiEndpoints._();
  static const _v1 = '/api/v1';

  // Stores
  static const String stores = '$_v1/stores';
  static String storeById(String id) => '$_v1/stores/$id';

  // Devices
  static const String devices = '$_v1/devices';
  static String deviceById(String id) => '$_v1/devices/$id';
  static const String devicePair = '$_v1/devices/pair';
}
```

## Data Source Pattern

```dart
// Interface (abstract)
abstract class StoreRemoteDataSource {
  Future<List<StoreModel>> getStores({String? search, int page = 1});
  Future<StoreModel> getStoreById(String id);
  Future<StoreModel> createStore(Map<String, dynamic> body);
}

// Implementation
class StoreRemoteDataSourceImpl implements StoreRemoteDataSource {
  StoreRemoteDataSourceImpl({required AppHttpClient httpClient})
      : _httpClient = httpClient;

  final AppHttpClient _httpClient;
  static const _timeout = Duration(seconds: 10);

  @override
  Future<List<StoreModel>> getStores({String? search, int page = 1}) async {
    final response = await _httpClient.get(
      ApiEndpoints.stores,
      queryParameters: {
        if (search != null) 'search': search,
        'page': page,
      },
    ).timeout(_timeout);

    final items = (response.data['data']['items'] as List)
        .map((e) => StoreModel.fromJson(e as Map<String, dynamic>))
        .toList();
    return items;
  }
}
```

## Repository Implementation

```dart
class StoreRepositoryImpl implements StoreRepository {
  const StoreRepositoryImpl({required this.remoteDataSource});
  final StoreRemoteDataSource remoteDataSource;

  @override
  Future<Result<StoreList>> listStores({String? search, int page = 1}) async {
    try {
      final items = await remoteDataSource.getStores(search: search, page: page);
      return Success(StoreList(items: items, total: items.length, page: page, pageSize: 20));
    } on DioException catch (e) {
      return Err(_mapDioError(e));
    } catch (e) {
      return Err(ServerFailure(message: e.toString()));
    }
  }
}
```

## Rules

- ⛔ Endpoints: NO hardcoded URLs — always use `ApiEndpoints`
- ⛔ DataSource: returns **models** (JSON-aware)
- ⛔ Repository: returns **Result<Entity>** (domain-aware)
- ✅ `try/catch` only in repositories (converts exceptions to Failures)
- ✅ Use `_timeout` const for consistent timeout values
