---
description: RMN Flutter app project context — entry points, features, tech stack, folder structure.
---

# RMN Flutter Project

## Overview

Multi-portal Flutter web app for Retail Media Network (RMN) platform.

## Entry Points

| File | Portal | Description |
|---|---|---|
| `main.dart` | Default | Landing page, auth |
| `main_admin.dart` | Admin | Platform management |
| `main_supplier.dart` | Supplier | Stores, devices |
| `main_advertiser.dart` | Advertiser | Content, campaigns |

## Tech Stack

- **State**: `flutter_bloc` (Bloc for features, Cubit for system-level only)
- **DI**: `get_it` (service locator) — modules in `bootstrap/modules/`
- **Routing**: `go_router`
- **HTTP**: Custom `AppHttpClient` wrapping `dio`
- **JSON**: `json_serializable` + `json_annotation`
- **L10n**: Flutter's built-in `intl` — ARB files in `lib/core/ui/l10n/`
- **Error**: `Result<T>` sealed class (not dartz Either)
- **Testing**: `bloc_test`, `mocktail`, `flutter_test`

## Folder Structure

```
lib/
├── app.dart                    # Root widget, GoRouter
├── main*.dart                  # 4 entry points
├── bootstrap/
│   ├── di_container.dart       # GetIt setup
│   ├── app_config.dart         # Env config
│   └── modules/                # Feature DI modules
├── core/
│   ├── domain/                 # Shared domain (base classes)
│   ├── infra/                  # HTTP, error/Result, pagination
│   ├── platform/               # Routing, device detection
│   ├── ui/                     # Theme, l10n, shared widgets
│   └── usecases/               # UseCase base class
└── features/
    ├── auth/                   # Authentication
    ├── admin/                  # Admin portal
    ├── supplier/               # Supplier portal
    ├── advertiser/             # Advertiser portal
    ├── landing/                # Landing pages (UI only)
    ├── portal/                 # Portal selector (UI only)
    └── home/                   # Home page (UI only)
```

## Feature Module Structure

Each feature follows clean architecture:

```
features/{name}/
├── data/
│   ├── datasources/            # Remote data source implementations
│   ├── models/                 # @JsonSerializable models (extend entities)
│   └── repositories/           # Repository implementations
├── domain/
│   ├── entities/               # Pure domain entities (no JSON)
│   ├── repositories/           # Repository interfaces (abstract)
│   └── usecases/               # Use cases (single responsibility)
└── presentation/
    ├── bloc/                   # BLoC + events + states
    ├── pages/                  # Page widgets (route targets)
    └── widgets/                # Reusable UI components
```
