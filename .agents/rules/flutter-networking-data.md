---
description: Networking, API integration, Repositories, Data Sources, and Models rules
---
# Flutter AI Rules - Networking & Data

## Networking Rules

```dart
// core/network/dio_client.dart
class DioClient {
  DioClient({required String baseUrl, String? token})
      : _dio = Dio(
          BaseOptions(
            baseUrl: baseUrl,
            connectTimeout: const Duration(seconds: 15),
            receiveTimeout: const Duration(seconds: 15),
            headers: {
              'Content-Type': 'application/json',
              if (token != null) 'Authorization': 'Bearer $token',
            },
          ),
        ) {
    _dio.interceptors.addAll([
      LogInterceptor(requestBody: true, responseBody: true),
    ]);
  }

  final Dio _dio;

  Dio get dio => _dio;
}
```

- All HTTP via **Dio**.
- Timeouts mandatory (connect + receive).
- Interceptors for logging, token refresh, error mapping.
- Base URL from environment config, never hardcoded.
- Token refresh interceptor required for authenticated APIs.

## DataSource Rules

- Remote data sources use **Dio** exclusively.
- Local data sources use **SharedPreferences**, **Hive**, or **SecureStorage**.
- Data sources throw exceptions. Repositories catch and return `Either<Failure, T>`.
- Every Dio call is wrapped in `try/catch` on `DioException`.

## Model Rules

- Models belong to the Data layer only.
- Models handle serialization (`fromJson`, `toJson`).
- Models convert to Domain entities via `toEntity()`.
- Models construct from Domain entities via `fromEntity()`.
- Entities are pure. Models are data-layer representations.

## Entity Rules

- Entities are in the Domain layer.
- No `fromJson`, no `toJson`. No serialization logic.
- Immutable. `Equatable`. `const` constructor.
- No flutter imports. Pure Dart only.

## Repository Rules

### Domain Layer (Interface)
Interfaces live in `features/<feature>/domain/repositories/<feature>_repository.dart`. They return `Future<Either<Failure, Type>>`.

### Data Layer (Implementation)
Implementations live in `features/<feature>/data/repositories/<feature>_repository_impl.dart`. They depend on specific `RemoteDataSource` or `LocalDataSource` and map raw exceptions to `Failure`.
