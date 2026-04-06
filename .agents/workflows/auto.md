---
description: Auto-complete full feature pipeline — plan + domain + data + BLoC + DI + UI + arch check + tests + verify. One command, zero interruptions.
---

# Auto Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-architect.md` and strictly adopt that persona!

Run the ENTIRE feature creation pipeline automatically. No step-by-step confirmation needed.

// turbo-all

## When to Use

- You trust the AI to handle the full flow
- Feature is well-defined (clear entities, clear API)
- You want speed over control

## Pipeline (executed in order)

### Phase 1: Research (search-first)

1. Search codebase for similar features
2. Read relevant skills: `flutter-bloc`, `flutter-api`, `flutter-di`
3. Identify API endpoints from `docs/API_DOCS/`

### Phase 2: Domain Layer

4. Create entity → `domain/entities/{name}.dart`
5. Create repository interface → `domain/repositories/{name}_repository.dart`
6. Create use cases → `domain/usecases/{action}_{name}_usecase.dart`

### Phase 3: Data Layer

7. Create model (@JsonSerializable) → `data/models/{name}_model.dart`
8. Run code gen:

```bash
cd app/frontend/rmn_app && dart run build_runner build --delete-conflicting-outputs
```

9. Create data source → `data/datasources/{name}_remote_data_source.dart`
10. Create repository impl → `data/repositories/{name}_repository.dart`
11. Add API endpoint → `core/infra/network/api_endpoints.dart`

### Phase 4: Presentation Layer

12. Create BLoC (events + states + bloc) — Result pattern, no try/catch
13. Create/update DI module → `bootstrap/modules/{feature}_module.dart`
14. Create page → `presentation/pages/{portal}_{name}_page.dart`
15. Add route → `app.dart`
16. Extract widgets if page > 500 lines

### Phase 5: Architecture Guard ⛔ (MANDATORY)

Run the `/check-flutter-arch` workflow to verify no architectural violations were introduced.

17. Execute all checks from `check-flutter-arch.md`:
    - Layer dependency direction (domain → nothing, presentation → domain, data → domain)
    - Entity purity (no JSON in domain)
    - Model has `toEntity()` or `extends`
    - BLoC patterns (no try/catch, no model imports, uses UseCases only)
    - File naming (snake_case)
    - Feature layer structure
    - DI module exists

18. If **any ❌ BLOCKER** found → **STOP and FIX** before proceeding. Loop until all blockers are resolved.

> ⚠️ This phase is NON-NEGOTIABLE. Do NOT skip it. The entire purpose is to prevent automated runs from silently degrading the architecture.

### Phase 6: Tests

19. Write BLoC tests (bloc_test + mocktail)
20. Cover: initial state, fetch success, fetch failure, refresh

### Phase 7: Verify

21. Run analysis:

```bash
cd app/frontend/rmn_app && flutter analyze
```

22. Run tests:

```bash
cd app/frontend/rmn_app && flutter test
```

23. Fix any issues (loop until clean)

## Output

At the end, report:
- Files created (count + list)
- Tests written (count)
- `flutter analyze` result
- `flutter test` result

## Fallback

If any phase fails critically:
- Stop and report the error
- Suggest manual fix or alternative approach

## Skills Reference

All skills auto-loaded:
- `search-first`, `flutter-bloc`, `flutter-api`, `flutter-di`, `flutter-testing`, `flutter-ui`, `flutter-l10n`

## Example Usage

```
/auto
Thêm feature Campaign cho advertiser portal.
API: GET /campaigns, GET /campaigns/:id, POST /campaigns
Entity: Campaign (id, name, status, startDate, endDate, budget)
```
