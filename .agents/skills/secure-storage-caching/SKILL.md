---
description: Patterns for using flutter_secure_storage and local caching.
---
# Secure Storage & Caching Pattern

## When to Use This Skill
Use this skill when reading/writing sensitive data (tokens, PII) or configuring offline data caching via Hive or SharedPreferences.

## Concept
Flutter applications must carefully distinguish between non-sensitive fast retrieval data (e.g., theme preferences, app launch history) and sensitive data requiring OS-level encryption (e.g., JWT access tokens, refresh tokens).

## Best Practices
1. **Strict Segregation**: 
   - Non-Sensitive -> `SharedPreferences` or `Hive`
   - Sensitive -> `flutter_secure_storage`
2. **Initialization Guard**: `flutter_secure_storage` on Android requires careful handling during app reinstalls due to Android Keystore behaviors. Always catch `PlatformException` on read, and if it fails (corrupted keystore), execute `storage.deleteAll()` and force absolute logout.
3. **Service Layer Abstraction**: Do NOT import `flutter_secure_storage` inside Repositories or BLoCs directly. Abstract it behind an `AuthLocalDataSource` interface or `SecureStorageService`.
4. **Caching Entities (Hive/SharedPreferences)**: When caching models (e.g., User Profile), store them strictly in the `Data` layer and map back to `Entities` upon retrieval to satisfy Domain purity.
