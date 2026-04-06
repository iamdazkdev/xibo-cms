---
description: Verify Flutter Clean Architecture integrity — layer dependencies, naming conventions, patterns. Run after any code change to ensure no architectural violations.
---

# Check Flutter Architecture

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-architect.md` and strictly adopt that persona!

Validate that the Flutter project (`app/frontend/rmn_app`) conforms to the established Clean Architecture rules.

// turbo-all

## When to Use

- After **any** automated code generation or modification
- Before committing feature code
- As a post-step in the `/auto` pipeline
- When reviewing PRs that touch `lib/features/`

## Phase 1: Layer Dependency Check

The dependency rule is: `presentation → domain ← data`. Domain depends on NOTHING.

1. **Domain must NOT import data or presentation**:

```bash
cd app/frontend/rmn_app && grep -rn "import.*data/" lib/features/*/domain/ lib/core/domain/ 2>/dev/null || echo "✅ Domain → Data: CLEAN"
```

```bash
cd app/frontend/rmn_app && grep -rn "import.*presentation/" lib/features/*/domain/ lib/core/domain/ 2>/dev/null || echo "✅ Domain → Presentation: CLEAN"
```

2. **Presentation must NOT import data layer directly**:

```bash
cd app/frontend/rmn_app && grep -rn "import.*data/" lib/features/*/presentation/ 2>/dev/null || echo "✅ Presentation → Data: CLEAN"
```

3. **Domain must NOT import any Flutter/package dependencies** (except `equatable`, `meta`):

```bash
cd app/frontend/rmn_app && grep -rn "import 'package:" lib/features/*/domain/ lib/core/domain/ 2>/dev/null | grep -v "equatable" | grep -v "meta" | grep -v "rmn_app" || echo "✅ Domain purity: CLEAN"
```

4. **Data layer must NOT import presentation**:

```bash
cd app/frontend/rmn_app && grep -rn "import.*presentation/" lib/features/*/data/ 2>/dev/null || echo "✅ Data → Presentation: CLEAN"
```

> If ANY grep returns results → it's a violation. Fix the import before proceeding.

## Phase 2: Model / Entity Separation

5. **Entities must NOT have `fromJson` / `toJson`**:

```bash
cd app/frontend/rmn_app && grep -rn "fromJson\|toJson\|@JsonSerializable\|json_serializable" lib/features/*/domain/entities/ lib/core/domain/ 2>/dev/null || echo "✅ Entity purity: CLEAN"
```

6. **Models must have `toEntity()` or extend an entity**:

```bash
cd app/frontend/rmn_app && for f in $(find lib/features/*/data/models -name '*.dart' ! -name '*.g.dart' 2>/dev/null); do grep -qE 'toEntity|extends [A-Z]' "$f" || echo "⚠️  Missing toEntity/extends in: $f"; done; echo "✅ Model check done"
```

## Phase 3: BLoC Pattern Check

7. **BLoCs must NOT use try/catch**:

```bash
cd app/frontend/rmn_app && grep -rn "try {" lib/features/*/presentation/bloc/ 2>/dev/null || echo "✅ BLoC no try/catch: CLEAN"
```

8. **BLoCs must NOT import models (data layer) directly**:

```bash
cd app/frontend/rmn_app && grep -rn "import.*data/models" lib/features/*/presentation/bloc/ 2>/dev/null || echo "✅ BLoC no model import: CLEAN"
```

9. **BLoCs must NOT import repositories directly (must use use cases)**:

```bash
cd app/frontend/rmn_app && grep -rn "import.*repositories/" lib/features/*/presentation/bloc/ 2>/dev/null || echo "✅ BLoC uses UseCases only: CLEAN"
```

## Phase 4: Naming Convention Check

10. **File naming — must be snake_case.dart**:

```bash
cd app/frontend/rmn_app && find lib/features/ -name '*.dart' | grep -E '[A-Z]' || echo "✅ File naming: CLEAN"
```

11. **Feature structure — each feature must have domain/, data/, presentation/**:

```bash
cd app/frontend/rmn_app && for d in lib/features/*/; do feature=$(basename "$d"); for layer in domain data presentation; do [ -d "$d$layer" ] || echo "⚠️  Missing $layer/ in features/$feature"; done; done; echo "✅ Feature structure check done"
```

## Phase 5: DI Module Check

12. **Every feature with a data layer should have a DI module**:

```bash
cd app/frontend/rmn_app && for d in lib/features/*/data/; do feature=$(basename "$(dirname "$d")"); ls lib/bootstrap/modules/${feature}_module.dart 2>/dev/null || echo "⚠️  Missing DI module for: $feature"; done; echo "✅ DI module check done"
```

## Phase 6: Static Analysis & Tests

13. Run `flutter analyze`:

```bash
cd app/frontend/rmn_app && flutter analyze
```

14. Run `flutter test`:

```bash
cd app/frontend/rmn_app && flutter test
```

15. Check formatting:

```bash
cd app/frontend/rmn_app && dart format --set-exit-if-changed lib/ test/
```

## Output

Report results as a table:

| Check | Status |
|---|---|
| Domain → Data imports | ✅ or ❌ |
| Domain → Presentation imports | ✅ or ❌ |
| Presentation → Data imports | ✅ or ❌ |
| Data → Presentation imports | ✅ or ❌ |
| Domain purity (no packages) | ✅ or ❌ |
| Entity purity (no JSON) | ✅ or ❌ |
| Model toEntity/extends | ✅ or ⚠️ |
| BLoC no try/catch | ✅ or ❌ |
| BLoC no model imports | ✅ or ❌ |
| BLoC uses UseCases only | ✅ or ❌ |
| File naming (snake_case) | ✅ or ❌ |
| Feature layer structure | ✅ or ⚠️ |
| DI modules exist | ✅ or ⚠️ |
| flutter analyze | ✅ or ❌ |
| flutter test | ✅ or ❌ |
| dart format | ✅ or ❌ |

## Severity Levels

- **❌ BLOCKER**: Layer dependency violations, BLoC anti-patterns → MUST fix
- **⚠️ WARNING**: Missing layer dirs, missing DI module → should fix (some features like `landing`, `portal`, `home` are UI-only and may not have all 3 layers)
- **✅ CLEAN**: No issues found

## Fallback

If blockers are found:
1. List all violations with file:line
2. Suggest concrete fixes
3. Do NOT proceed with commit until all ❌ are resolved
