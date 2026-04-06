---
description: Patterns for Dio interceptors, JWT refresh loops, and handling 401 Unauthorized API responses.
---
# Dio Interceptors & Auth Refresh Pattern

## When to Use This Skill
Use this skill when configuring Dio instances, handling secure API requests, creating token refresh functionality, or managing session expiration workflows.

## Concept
All outbound API requests must be uniformly authenticated, logged, and gracefully handled when tokens expire via a centralized interceptor instead of ad-hoc error catching in Repositories.

## Best Practices
1. **AuthInterceptor**: Attach the Access Token to every request's `Authorization: Bearer <token>` header dynamically. Never cache it in `DioClient` construction.
2. **Token Refresh (401 Handling)**:
   - When a 401 is received, interceptor must pause all outgoing requests (using a lock or queuing).
   - Attempt to call the `/auth/refresh` endpoint using the securely stored Refresh Token.
   - If refresh succeeds, update secure storage, retry queued requests, and proceed.
   - If refresh fails, clear all storage and dispatch an `UnauthenticatedEvent` to global AppBloc to force logout.
3. **Logging Interceptor**: Always use `LogInterceptor(requestBody: true, responseBody: true)` in development only. 
4. **Environment Configurations**: Validate `baseUrl` dynamically through `AppConfig`. Do NOT hardcode endpoints in the Dio setup.
