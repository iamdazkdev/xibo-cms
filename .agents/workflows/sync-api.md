---
description: Sync Flutter data layer when backend API changes. Read API docs → update entity/model/datasource/endpoint.
---

# Sync API Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-core-engineer.md` and strictly adopt that persona!

Update Flutter data layer when backend API changes or new endpoints are added.

// turbo-all

## When to Use

- Backend adds new API endpoint
- API response schema changes
- New service deployed
- After reading updated Swagger/API docs

## Steps

1. **Read API documentation**:

```bash
# Check available API docs
ls app/frontend/rmn_app/docs/API_DOCS/
```

2. **Identify changes**:
   - New endpoints → new entity + model + datasource methods
   - Schema changes → update entity fields + model fields
   - Removed fields → remove from entity + model

3. **Update domain entity** (`domain/entities/`):
   - Add/remove/modify fields
   - Update `props` list

4. **Update data model** (`data/models/`):
   - Update `super.` fields to match entity
   - Regenerate JSON:

```bash
cd app/frontend/rmn_app && dart run build_runner build --delete-conflicting-outputs
```

5. **Update API endpoints** (`core/infra/network/api_endpoints.dart`):
   - Add new endpoint constants/methods

6. **Update data source** (`data/datasources/`):
   - Add new methods for new endpoints
   - Update existing methods for schema changes

7. **Update repository** (`data/repositories/`):
   - Add new methods matching domain interface changes

8. **Update use cases** if new operations added

9. **Verify**:

```bash
flutter analyze
flutter test
```

## Example

Backend adds `PATCH /api/v1/stores/:id/status`:

1. Add endpoint: `static String storeStatus(String id) => '$_v1/stores/$id/status';`
2. Add datasource method: `Future<StoreModel> updateStoreStatus(String id, String status)`
3. Add repo method: `Future<Result<Store>> updateStoreStatus(String id, String status)`
4. Add use case: `UpdateStoreStatusUseCase`
5. Wire into BLoC + DI
