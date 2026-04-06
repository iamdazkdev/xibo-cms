---
description: Model & Entity 2-layer pattern — domain entity (no JSON) + data model (@JsonSerializable).
---

# Model & Entity Convention

## 2-Layer Pattern (MANDATORY)

### 1. Domain Entity (`domain/entities/`)

Pure Dart class — no serialization, no JSON, no package dependencies (except `equatable`).

```dart
class Store {
  const Store({
    required this.id,
    required this.name,
    required this.status,
    this.operatingHours,
  });

  final String id;
  final String name;
  final String status;
  final StoreOperatingHours? operatingHours;
}
```

### 2. Data Model (`data/models/`)

Handles JSON serialization via `json_serializable`. Two valid patterns:

#### Pattern A: `extends` Entity (PREFERRED for simple models)

Use when model fields have the **same types** as entity fields (no nested models, no @JsonKey).

```dart
@JsonSerializable(fieldRename: FieldRename.snake)
class RoleModel extends Role {
  const RoleModel({required super.id, required super.name});

  factory RoleModel.fromJson(Map<String, dynamic> json) =>
      _$RoleModelFromJson(json);
  Map<String, dynamic> toJson() => _$RoleModelToJson(this);
}
```

#### Pattern B: `toEntity()` (REQUIRED for complex models)

Use when model has **nested model types**, **@JsonKey converters**, or **different field types** than entity.

```dart
@JsonSerializable(fieldRename: FieldRename.snake)
class StoreModel {
  const StoreModel({required this.id, required this.name, this.operatingHours});

  final String id;
  final String name;
  final OperatingHoursModel? operatingHours; // different type than entity

  @JsonKey(fromJson: _doubleFromJson) // custom converter
  final double? latitude;

  factory StoreModel.fromJson(Map<String, dynamic> json) =>
      _$StoreModelFromJson(json);

  Store toEntity() => Store(
        id: id,
        name: name,
        operatingHours: operatingHours?.toEntity(),
      );
}
```

## When to Use Which

| Condition | Pattern |
|---|---|
| All fields are simple types (String, int, bool, DateTime) | A: `extends` |
| Has nested model references (e.g., `OperatingHoursModel?`) | B: `toEntity()` |
| Has `@JsonKey` custom converters | B: `toEntity()` |
| Has list of nested models (e.g., `List<ItemModel>`) | B: `toEntity()` |

## Rules

- ⛔ NEVER put `fromJson`/`toJson` in domain entities
- ⛔ NEVER import models from BLoCs or pages — use entities
- ⛔ NEVER use manual `json['field']` casts — always use `json_serializable`
- ✅ Every model MUST have a `toEntity()` method (explicit or via extends)
- ✅ After creating/modifying models, always run:

```bash
dart run build_runner build --delete-conflicting-outputs
```
