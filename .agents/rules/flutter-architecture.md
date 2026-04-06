---
description: Clean Architecture, Layer Boundaries, Dependency Injection, and Folder Structure for RMN Flutter App
---
# Flutter AI Rules - Architecture

## Core Principles

1. **Structure Before Code** — Every feature follows Clean Architecture. No code is written without knowing its layer, folder, and responsibility.
2. **Immutability Is Non-Negotiable** — All BLoC states, entities, and models are immutable. Use `copyWith`, `Equatable`, and `const` constructors.
3. **BLoC Is the Primary State Management** — No Provider, no Riverpod, no GetX. BLoC with explicit Events and States for all features. Cubit is allowed only in `core/` for simple state holders (e.g., loading, network, splash, biometric).
4. **Repository Pattern Is Mandatory** — Domain defines abstract repositories. Data implements them. No layer skipping.
5. **No Business Logic in UI** — Widgets dispatch events. BLoCs process logic. Widgets render states. No exceptions.
6. **No BuildContext Inside BLoC** — BLoC never imports `flutter/material.dart`. Navigation, dialogs, and theming belong to the presentation layer.
7. **Fail Fast, Fail Loud** — Validate inputs at boundaries. Return typed failures. Never silently swallow errors.
8. **Many Small Files Over Few Large Files** — One class per file. 200-400 lines typical. 600 max.
9. **Plan Before Execute** — Complex features require an implementation plan before code is written.
10. **Test-Driven Development** — Write tests first. Red-Green-Refactor. 80%+ coverage mandatory.
11. **Security-First** — Never hardcode secrets. Validate all inputs. Sanitize all outputs. Audit before commit.
12. **Agent-First Delegation** — Delegate to specialized agents for planning, review, testing, security, and architecture decisions.

## Architecture Rules

### Layer Boundaries

```
┌─────────────────────────────────────────────┐
│  Presentation (UI)                          │
│  - Widgets dispatch Events to BLoC          │
│  - BlocBuilder / BlocListener render States │
│  - No business logic                        │
├─────────────────────────────────────────────┤
│  Domain (Business Logic)                    │
│  - Entities (pure Dart, no dependencies)    │
│  - Use Cases (single responsibility)        │
│  - Repository interfaces (abstract)         │
├─────────────────────────────────────────────┤
│  Data (Implementation)                      │
│  - Repository implementations               │
│  - Data sources (remote + local)            │
│  - Models (fromJson / toJson / toEntity)    │
└─────────────────────────────────────────────┘
```

### Dependency Direction

```
Presentation → Domain ← Data
```

- Presentation depends on Domain.
- Data depends on Domain.
- Domain depends on NOTHING.
- Data never imports Presentation. Presentation never imports Data directly.

### Allowed and Forbidden Dependencies

| From | To Domain | To Data | To Presentation | To Core |
|------|-----------|---------|-----------------|---------|
| **Domain** | Self only | FORBIDDEN | FORBIDDEN | ALLOWED (error, usecases) |
| **Data** | ALLOWED | Self only | FORBIDDEN | ALLOWED (error, network) |
| **Presentation** | ALLOWED | FORBIDDEN | Self only | ALLOWED (routing, widgets, theme) |
| **Core** | FORBIDDEN | FORBIDDEN | FORBIDDEN | Self only |

### Dependency Injection

- All dependencies registered in `bootstrap/di_container.dart` using **GetIt**.
- Register in order: core services → data sources → repositories → use cases → BLoCs.
- Use `sl<T>()` (service locator) for retrieval. Never instantiate dependencies manually in widgets or BLoCs.
- BLoCs registered as `Factory` (new instance per screen). Everything else as `LazySingleton`.

## Folder Structure (ABSOLUTE)

```
lib/
├── main.dart
├── app.dart
├── bootstrap/
│   ├── app_config.dart
│   └── di_container.dart
├── core/
│   ├── ... (core services, widgets, theme, routing, error handling)
└── features/
    └── <feature_name>/
        ├── data/
        │   ├── datasources/
        │   │   ├── <feature>_remote_data_source.dart
        │   │   └── <feature>_local_data_source.dart
        │   ├── models/
        │   │   └── <entity>_model.dart
        │   └── repositories/
        │       └── <feature>_repository_impl.dart
        ├── domain/
        │   ├── entities/
        │   │   └── <entity>.dart
        │   ├── repositories/
        │   │   └── <feature>_repository.dart
        │   └── usecases/
        │       └── <usecase_name>.dart
        └── presentation/
            ├── bloc/
            │   ├── <feature>_bloc.dart
            │   ├── <feature>_event.dart
            │   └── <feature>_state.dart
            ├── pages/
            │   └── <feature>_page.dart
            └── widgets/
                └── <widget_name>.dart
```

## Checklists
Before marking any feature complete:
- [ ] All layers present (data/domain/presentation)
- [ ] Repository interface in domain, implementation in data
- [ ] Use case has single responsibility
- [ ] DI registered in `di_container.dart`
- [ ] Each file < 600 lines, each function < 50 lines
