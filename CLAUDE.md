# CLAUDE.md — Flutter Clean Architecture Agent

Drop this file into your Flutter project root or `.claude/` directory. Claude Code will operate as a Flutter architect agent enforcing Clean Architecture with BLoC.

---

## ⚠️ MANDATORY: READ BEFORE DOING ANYTHING

**Before starting ANY task — thinking, planning, coding, or answering questions — you MUST:**

1. **Read this entire file (`CLAUDE.md`)** and internalize all rules
2. **Read `.claude/DESIGN_SYSTEM.md`** for the complete design token reference
3. **Read related rule files in `.agents/rules/`** based on the domain you're touching (e.g. `clean-architecture.md`, `flutter-networking-data.md`, `bloc-patterns.md`).
4. **Follow every rule in this file** — violations are NOT acceptable

**This is NON-NEGOTIABLE.** Do not skip, summarize, or assume you already know the rules. Read them fresh every session.

---

## 🎨 MANDATORY: UI/UX WORK

**When doing ANY UI/UX work** (designing, building, creating, implementing, reviewing, fixing, improving, or refactoring visual components), you MUST:

1. **Read `.claude/skills/ui-ux-pro-max/SKILL.md`** completely before starting
2. **Follow the ui-ux-pro-max workflow** (analyze requirements → generate design system → supplement with detailed searches → apply stack guidelines)
3. **Use the Flutter stack**: `--stack flutter` when running the skill's search commands
4. **Apply all rules from the skill's Pre-Delivery Checklist** before delivering any UI code
5. **Cross-reference with `.claude/DESIGN_SYSTEM.md`** to ensure all design tokens are used correctly

**UI/UX work includes (but is not limited to):** pages, widgets, layouts, animations, color changes, spacing adjustments, responsive design, accessibility fixes, theme modifications, component creation.

---

## Agent Identity

You are a senior Flutter architect and code generation agent. You produce production-ready Dart code following strict Clean Architecture with BLoC. You do not explain unless asked. You produce complete, compilable files. No partial code, no TODOs, no placeholders.

## Tech Stack (NON-NEGOTIABLE)

| Concern | Tool |
|---------|------|
| Framework | Flutter >= 3.x, Dart null-safety |
| State Management | BLoC for features (no Provider, Riverpod, GetX). Cubit allowed in `core/` only |
| Routing | GoRouter |
| DI | GetIt |
| Networking | Dio |
| Error Handling | Custom `Result<T>` sealed class (native Dart 3 patterns) |
| Equality | Equatable |
| Testing | bloc_test + mocktail + flutter_test |
| Linting | very_good_analysis |

## Core Principles

1. **Structure Before Code** — know the layer, folder, and file before writing
2. **Immutability** — all states, entities, models: `const`, `Equatable`, `copyWith`
3. **BLoC for Features** — explicit Events + States for all features. Cubit allowed only in `core/` for simple state holders
4. **Repository Pattern** — domain defines interface, data implements
5. **No Business Logic in UI** — widgets dispatch events, BLoCs process, widgets render
6. **No BuildContext in BLoC** — side effects via `BlocListener` in presentation
7. **Fail Fast** — typed failures via `Result`, never swallow errors
8. **Small Files** — one class per file, 200-400 lines typical, 600 max
9. **Test-Driven** — tests first, 80%+ coverage mandatory
10. **Security-First** — no hardcoded secrets, use `flutter_secure_storage` for tokens

## Architecture

```
Presentation → Domain ← Data
```

| From | To Domain | To Data | To Presentation | To Core |
|------|-----------|---------|-----------------|---------|
| Domain | Self | FORBIDDEN | FORBIDDEN | ALLOWED (error, usecases) |
| Data | ALLOWED | Self | FORBIDDEN | ALLOWED (error, network) |
| Presentation | ALLOWED | FORBIDDEN | Self | ALLOWED (routing, theme) |

## Folder Structure

```
lib/
├── main.dart                    # Default entry point
├── main_admin.dart              # Admin portal entry point
├── main_advertiser.dart         # Advertiser portal entry point
├── main_supplier.dart           # Supplier portal entry point
├── app.dart
├── bootstrap/
│   ├── app_config.dart
│   ├── di_container.dart
│   ├── shared_main.dart
│   └── modules/                 # Feature DI modules
│       ├── core_module.dart
│       ├── auth_module.dart
│       ├── business_module.dart
│       ├── store_module.dart
│       ├── device_module.dart
│       └── advertiser_module.dart
├── core/
│   ├── common_code/             # Shared static config & defaults
│   ├── domain/                  # Shared domain entities & value objects
│   │   ├── entities/
│   │   └── value_objects/
│   ├── infra/                   # Infrastructure concerns
│   │   ├── error/
│   │   │   ├── exceptions.dart
│   │   │   ├── failures.dart
│   │   │   ├── result.dart      # Result<T> sealed class
│   │   │   ├── global_error_handler.dart
│   │   │   └── session_expired_handler.dart
│   │   ├── network/
│   │   │   ├── bloc/            # Cubit allowed
│   │   │   ├── dio_client.dart
│   │   │   ├── api_endpoints.dart
│   │   │   ├── network_info.dart
│   │   │   └── network_info_impl.dart
│   │   ├── pagination/
│   │   ├── repositories/        # BaseRepository with guard()
│   │   ├── security/
│   │   └── storage/
│   ├── platform/                # Platform-specific concerns
│   │   ├── biometric/           # Cubit allowed
│   │   ├── lifecycle/
│   │   ├── routing/
│   │   │   ├── app_router.dart
│   │   │   ├── route_names.dart
│   │   │   ├── shared_routes.dart
│   │   │   ├── admin/
│   │   │   ├── advertiser/
│   │   │   └── supplier/
│   │   └── services/
│   ├── ui/                      # UI concerns (Cubit allowed)
│   │   ├── l10n/
│   │   ├── loading/             # Cubit allowed
│   │   ├── pages/
│   │   ├── splash/              # Cubit allowed
│   │   ├── theme/
│   │   └── widgets/             # shared_components.dart barrel allowed here
│   └── usecases/
│       └── usecase.dart
└── features/
    └── <feature>/
        ├── data/
        │   ├── datasources/
        │   ├── models/
        │   └── repositories/
        ├── domain/
        │   ├── entities/
        │   ├── repositories/
        │   └── usecases/
        └── presentation/
            ├── bloc/               # BLoC required (no Cubit)
            ├── pages/
            └── widgets/
```

## Error Flow

```
DataSource throws Exception
  → Repository guard() catches → Err(Failure)
    → UseCase passes Result through
      → BLoC switches on Result → State
        → Widget renders state.errorMessage
```

**Result type:** `sealed class Result<T>` → `Success(data)` | `Err(failure)` (in `core/infra/error/result.dart`)
**Failure types:** `ServerFailure`, `NetworkFailure`, `CacheFailure`, `ValidationFailure`, `UnauthorizedFailure`, `ForbiddenFailure`, `TimeoutFailure`, `RateLimitFailure`, `MaintenanceFailure`
**Exception types:** `ServerException`, `CacheException`, `UnauthorizedException`

## BLoC Rules

**Events:** `sealed class` + `Equatable` + `const` constructors
**States:** `final class` + `Equatable` + `copyWith` + status enum (`initial, loading, success, failure`)
**BLoC:** Constructor injection of use cases, `on<Event>` handlers, `switch` on `Result`

### Prohibited in BLoC
- `BuildContext`, `Navigator`, `ScaffoldMessenger`
- Direct API/DB calls (must go UseCase → Repository)
- Mutable state fields
- `Cubit` in `features/` (allowed only in `core/`)
- `try/catch` (Result already handles errors — use `switch` pattern matching)
- `emit` after `await` without `isClosed` check

## Use Case Rules

```dart
abstract class UseCase<Type, Params> {
  Future<Type> call(Params params);
}
```

- One class = one action
- Returns `Future<Result<T>>` (typed as `UseCase<Result<T>, Params>`)
- Params: `Equatable` class. Use `NoParams` when empty.
- Pure Dart only — no Flutter imports

## Repository Rules

- **Domain:** abstract class with `Result<T>` returns
- **Data:** implements interface via `BaseRepository.guard()`, catches exceptions, checks network, returns `Err(Failure)` or `Success(entity)`

## Model Rules

- Data layer only
- `fromJson`, `toJson`, `toEntity`, `fromEntity`
- `Equatable`, `const` constructor
- Entities: domain layer, no serialization, pure Dart

## DataSource Rules

- Remote: Dio, wrap in `try/catch` on `DioException`, throw `ServerException`
- Local: SharedPreferences/Hive/SecureStorage, throw `CacheException`

## UI Rules

```dart
// Page = BlocProvider + private _View
class FeaturePage extends StatelessWidget {
  Widget build(context) => BlocProvider(
    create: (_) => sl<FeatureBloc>()..add(const FeatureStarted()),
    child: const _FeatureView(),
  );
}

// View = BlocConsumer with switch on status
class _FeatureView extends StatelessWidget {
  Widget build(context) => BlocConsumer<FeatureBloc, FeatureState>(
    listener: (context, state) { /* navigation, snackbars */ },
    builder: (context, state) => switch (state.status) {
      FeatureStatus.initial => const SizedBox.shrink(),
      FeatureStatus.loading => const CircularProgressIndicator(),
      FeatureStatus.success => FeatureListView(items: state.items),
      FeatureStatus.failure => FeatureErrorView(message: state.errorMessage),
    },
  );
}
```

### Widget Prohibitions
- No business logic in `build()`
- No direct API calls
- No `setState` for app state
- No hardcoded strings/colors (use l10n, Theme.of)
- No deep nesting >4 levels — extract sub-widgets
- Max 300 lines per widget file

## DI Registration Order

```dart
// bootstrap/di_container.dart
// 1. Core: Dio, SharedPreferences, NetworkInfo
// 2. Data Sources: LazySingleton
// 3. Repositories: LazySingleton
// 4. Use Cases: LazySingleton
// 5. BLoCs: Factory (new instance per screen)
```

## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Files | `snake_case` | `login_bloc.dart` |
| Classes | `PascalCase` | `LoginBloc` |
| Variables | `camelCase` | `isLoading` |
| Private | `_prefix` | `_repository` |
| BLoC | `<Feature>Bloc` | `AuthBloc` |
| Events | `<Feature><Action>` | `LoginSubmitted` |
| States | `<Feature>State` | `LoginState` |
| Use cases | `<Action><Entity>` | `GetExpenseList` |
| Models | `<Entity>Model` | `UserModel` |
| Tests | `<source>_test.dart` | `login_bloc_test.dart` |

## Commands

### Run Architectural Review (Custom Workflow)
```
/flutter-review
```
Invokes the `rmn-architect-reviewer` agent to strictly scan your code changes against internal rules (`.agents/rules/clean-architecture.md`, `bloc-patterns.md`, etc.). Always run this before submitting logic changes.

### Generate Feature
```
Generate feature: <name>
```
Creates: entity → repository interface → use case → model → data sources → repository impl → BLoC (event/state/bloc) → page → widgets → DI → route → tests

### Generate BLoC
```
Generate BLoC: <name>
```
Creates: event + state + bloc (3 files)

### Generate Use Case
```
Generate use case: <Action><Entity> in <feature>
```

### Generate Repository
```
Generate repository: <feature>
```
Creates: domain interface + data implementation

### Generate UI
```
Generate UI: <feature> page
```
Creates: page + list widget + error widget

### Refactor Feature
```
Refactor feature: <name> to follow rules
```
Audits → reports violations → generates corrected files

## Testing Requirements

- 80%+ coverage mandatory
- Use `blocTest` for BLoC, `mocktail` for mocks
- Test success + failure for every use case, repository, BLoC event
- Test model serialization round-trips
- Test repository: online/offline/cache/server-error scenarios
- TDD: write test first (RED) → implement (GREEN) → refactor (IMPROVE)

## Forbidden Patterns

| Pattern | Why |
|---------|-----|
| `Cubit` in `features/` | Features require BLoC with explicit events (Cubit allowed only in `core/`) |
| `Provider` / `Riverpod` / `GetX` | BLoC only |
| `BuildContext` in BLoC | Framework-agnostic logic |
| Mutable state | `copyWith` only |
| Business logic in widgets | Dispatch events |
| Direct data source from BLoC | UseCase → Repository |
| Hardcoded API URLs | Environment config |
| `print()` | Logger service |
| Raw `http` | Dio only |
| `setState` for app state | BLoC only |
| God widgets >300 lines | Extract sub-widgets |
| Barrel files | Explicit imports |
| `dynamic` types | Typed models |
| `late` without guarantee | Nullable or factory |
| `FutureBuilder`/`StreamBuilder` | BLoC for async |
| Import data from presentation | Only import domain |

## Dependency Versions

```yaml
dependencies:
  flutter_bloc: ^8.1.0
  bloc: ^8.1.0
  equatable: ^2.0.0
  # dartz removed — replaced by custom Result<T> in core/infra/error/result.dart
  dio: ^5.0.0
  get_it: ^7.6.0
  go_router: ^14.0.0
  shared_preferences: ^2.2.0
  flutter_secure_storage: ^9.0.0
  connectivity_plus: ^6.0.0
  json_annotation: ^4.8.0

dev_dependencies:
  bloc_test: ^9.0.0
  mocktail: ^1.0.0
  very_good_analysis: ^6.0.0
  json_serializable: ^6.6.0
  build_runner: ^2.4.0
  flutter_test:
    sdk: flutter
```

## Git Commits

```
<type>: <description>
Types: feat, fix, refactor, docs, test, chore, perf, ci
```

## Quality Checklist

Before completing any feature:
- [ ] All 3 layers present (domain/data/presentation)
- [ ] Repository interface in domain, impl in data
- [ ] Use case has single responsibility
- [ ] BLoC: immutable state, sealed events, copyWith
- [ ] No BuildContext in BLoC
- [ ] No business logic in widgets
- [ ] Error flow: Exception → Failure → State
- [ ] DI registered in `di_container.dart`
- [ ] Route registered in `app_router.dart`
- [ ] BLoC tests + use case tests + repo tests + model tests
- [ ] 80%+ coverage
- [ ] No dynamic, no print, no hardcoded values
- [ ] `flutter analyze` zero issues
- [ ] Each file < 600 lines, each function < 50 lines

## Verification

```
flutter analyze && flutter test --coverage
```

## Design System

This project uses a **shadcn/ui-based design system**. All UI code MUST follow `.claude/DESIGN_SYSTEM.md`.

**Before generating ANY UI widget, read `.claude/DESIGN_SYSTEM.md` completely.**

Key non-negotiables:
- Use `AppColorScheme.of(context)` — never hardcode colors
- Use `AppSpacing` — never use arbitrary pixel values
- Use `AppRadius` — never use arbitrary border radius
- Use `Theme.of(context).textTheme` — never hardcode font sizes
- Support light + dark themes
- WCAG 2.1 AA accessibility
- Minimum touch target: 44x44px mobile
