# Refactor Prompt

Audit and refactor an existing Flutter feature to comply with Clean Architecture + BLoC rules.

## Input

Feature path: lib/features/{feature_name}/
Rules document: flutter_ai_rules.md

## Process

### Step 1: Full Audit

Read every file in the feature directory. For each file, check:

**CRITICAL violations (must fix):**
- [ ] BuildContext used inside BLoC
- [ ] Mutable state fields (non-final, no copyWith)
- [ ] Business logic in widget build methods
- [ ] Direct API/DB calls from BLoC (bypassing UseCase/Repository)
- [ ] Missing layers (no domain layer, no repository interface)
- [ ] Hardcoded secrets or API URLs

**HIGH violations (should fix):**
- [ ] Missing use case layer (BLoC calls repository directly)
- [ ] Events not sealed or not Equatable
- [ ] States not Equatable or missing copyWith
- [ ] No error handling (missing try/catch in data sources, missing Either in repositories)
- [ ] Data layer imported from presentation layer
- [ ] Navigation logic inside BLoC

**MEDIUM violations (improve):**
- [ ] Missing tests
- [ ] God widgets (>300 lines)
- [ ] Deep nesting (>4 levels)
- [ ] Hardcoded strings/colors in UI
- [ ] Missing DI registration
- [ ] print() statements
- [ ] dynamic types

### Step 2: Violation Report

Output a structured report:

```
REFACTOR AUDIT: {feature_name}
==============================

CRITICAL: X issues
- [{file}:{line}] {description}

HIGH: X issues
- [{file}:{line}] {description}

MEDIUM: X issues
- [{file}:{line}] {description}

Plan:
- Files to create: X (list them)
- Files to modify: X (list them)
- Files to delete: X (list them)
```

### Step 3: Generate Corrected Files

For each violation, generate the corrected file. Output complete files, not patches.

Order of generation:
1. Domain entities (if missing or incorrect)
2. Domain repository interfaces (if missing)
3. Domain use cases (if missing)
4. Data models (if missing fromJson/toJson/toEntity)
5. Data sources (if missing interface/impl split)
6. Data repository implementations (if missing or not using Either)
7. BLoC events (fix sealed, Equatable, const)
8. BLoC states (fix Equatable, copyWith, immutability)
9. BLoC (fix constructor injection, remove BuildContext, use fold)
10. Pages/widgets (extract logic to BLoC, fix widget structure)
11. DI registration updates
12. Route registration updates
13. Tests for new/modified classes

### Step 4: Verification

After generating all files, verify:
- [ ] All dependency arrows correct (Presentation → Domain ← Data)
- [ ] No cross-layer imports
- [ ] All states immutable
- [ ] All events sealed + Equatable
- [ ] Error flow: Exception → Failure → State
- [ ] DI complete
- [ ] Tests cover success + failure

## Constraints

- Never delete functionality — restructure it into the correct layer
- Preserve all existing behavior
- Generate complete files, not diffs
- Every generated file must compile
- Maintain existing naming where possible
- If a class needs to move layers, update all imports
