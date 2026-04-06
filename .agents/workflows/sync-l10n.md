---
description: Sync ARB translation files after adding/modifying l10n keys. Ensure en, vi, ko are in sync.
---

# Sync L10n Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-core-engineer.md` and strictly adopt that persona!

Sync ARB translation files after adding or modifying l10n keys.

## Steps

1. **Find untranslated keys**:

// turbo

```bash
cd app/frontend/rmn_app && flutter gen-l10n 2>&1 | grep -i "untranslated"
```

2. **Compare ARB files**:

// turbo

```bash
cd app/frontend/rmn_app && python3 -c "
import json
en = json.load(open('lib/core/ui/l10n/app_en.arb'))
vi = json.load(open('lib/core/ui/l10n/app_vi.arb'))
ko = json.load(open('lib/core/ui/l10n/app_ko.arb'))
en_keys = {k for k in en if not k.startswith('@') and not k.startswith('@@')}
vi_keys = {k for k in vi if not k.startswith('@') and not k.startswith('@@')}
ko_keys = {k for k in ko if not k.startswith('@') and not k.startswith('@@')}
missing_vi = en_keys - vi_keys
missing_ko = en_keys - ko_keys
if missing_vi: print(f'Missing in vi: {missing_vi}')
if missing_ko: print(f'Missing in ko: {missing_ko}')
if not missing_vi and not missing_ko: print('All synced!')
"
```

3. **Add missing translations** to `app_vi.arb` and `app_ko.arb`
   - Translate from English source
   - Keep key names identical across all files
   - Include `@key` metadata only in `app_en.arb` (source)

4. **Regenerate**:

// turbo

```bash
cd app/frontend/rmn_app && flutter gen-l10n
```

5. **Verify no warnings**:

// turbo

```bash
cd app/frontend/rmn_app && flutter analyze
```

## Supported Languages

| Code | Language |
|---|---|
| `en` | English (source) |
| `vi` | Vietnamese |
| `ko` | Korean |
