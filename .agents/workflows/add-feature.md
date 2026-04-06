---
description: Full feature creation flow — domain → data → BLoC → UI → DI → tests.
---
# Add Feature Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-core-engineer.md` and strictly adopt that persona!

Create a full feature from scratch following Clean Architecture strictly.

## Prerequisites
- Read `flutter-architecture`, `flutter-bloc-state`, `wizard-form-pattern` rules/skills.

## Execution Order
```
Entity → Repo Interface → Use Cases → Model → build_runner (if needed) → DataSource → Repo Impl → BLoC → BLoC Tests (TDD) → UI Widgets → Pages → DI Registration → Routes → Analyze → Widget Tests
```

## Steps

### Phase 1: Domain
1. **Create Entity** → `lib/features/{feature}/domain/entities/{name}.dart` (Pure Dart, Equatable)
2. **Create Repo Interface** → `lib/features/{feature}/domain/repositories/{name}_repository.dart`
3. **Create Use Cases** → `lib/features/{feature}/domain/usecases/{action}_{name}_usecase.dart`

### Phase 2: Data
4. **Create Model** → `lib/features/{feature}/data/models/{name}_model.dart` (extends Entity, handles JSON)
5. **Create Data Source** → `lib/features/{feature}/data/datasources/{name}_remote_data_source.dart` (Uses Dio)
6. **Create Repo Impl** → `lib/features/{feature}/data/repositories/{name}_repository_impl.dart` (Catches Exceptions, returns Failures)

### Phase 3: BLoC & TDD (CRITICAL)
// turbo
7. **Generate BLoC** (States must be immutable, Events sealed)
8. **Write BLoC Tests** (Red -> Green -> Refactor before touching UI)
   ```bash
   flutter test test/features/{feature}/presentation/bloc/{name}_bloc_test.dart
   ```

### Phase 4: Presentation & UI
9. **Create Widgets** (No logic in BuildContext)
10. **Create Page** (Wraps BlocProvider and BlocListener)

### Phase 5: Wiring & Verification
11. **Inject dependencies** in `di_container.dart`
12. **Register route** in `app_router.dart`

// turbo
13. **Analyze & Test**:
```bash
flutter pub run build_runner build --delete-conflicting-outputs
flutter analyze
flutter test --coverage
```
