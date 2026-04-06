---
name: flutter-l10n
description: 'L10n and internationalization for RMN Flutter app. Use when: adding/modifying l10n keys, managing ARB files, translating UI strings.'
---

# Flutter L10n Skill

## ARB File Location

```
lib/core/ui/l10n/
├── app_en.arb          # English (source)
├── app_vi.arb          # Vietnamese
├── app_ko.arb          # Korean
└── generated/          # Auto-generated (do NOT edit)
    ├── app_localizations.dart
    ├── app_localizations_en.dart
    └── app_localizations_vi.dart
```

## Adding a New Key

### 1. Add to `app_en.arb` (source of truth)

```json
{
  "storeListTitle": "Store List",
  "@storeListTitle": { "description": "Title for the store list page" },

  "storeCount": "{count} stores",
  "@storeCount": {
    "description": "Number of stores",
    "placeholders": { "count": { "type": "int" } }
  }
}
```

### 2. Add to `app_vi.arb` and `app_ko.arb`

```json
{
  "storeListTitle": "Danh sách cửa hàng",
  "storeCount": "{count} cửa hàng"
}
```

### 3. Regenerate

```bash
flutter gen-l10n
```

### 4. Use in Code

```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';

// Extension (if available)
context.l10n.storeListTitle

// Direct
AppLocalizations.of(context)!.storeListTitle
AppLocalizations.of(context)!.storeCount(42)
```

## Key Naming Convention

- `camelCase` for all keys
- Page titles: `{feature}{page}Title` → `storeListTitle`
- Actions: `{action}Button` → `saveButton`, `cancelButton`
- Errors: `error{Type}` → `errorUnexpected`, `errorTimeout`
- Labels: `{field}Label` → `emailLabel`, `passwordLabel`
- Messages: `{action}Success` / `{action}Failed` → `loginSuccess`

## Existing Error Keys

```
errorUnexpected, errorTimeout, errorNoInternet
errorSessionTitle, errorSessionMessage
```
