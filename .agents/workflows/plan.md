---
description: Create implementation plan for a Flutter feature before coding. WAIT for user approval.
---

# Plan Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-architect.md` and strictly adopt that persona!

Create a detailed implementation plan before writing any code.

## When to Use

- Starting a new feature
- Making significant architectural changes
- Complex refactoring
- Multiple files/features will be affected

## Steps

1. **Analyze** request — Restate requirements, identify affected features
2. **Search codebase** — Find existing patterns, similar implementations
3. **Break down** into phases:
   - Entity → Model → DataSource → Repository → UseCase → BLoC → DI → UI → Tests
4. **Identify dependencies** between components
5. **Assess risks** and potential blockers

// turbo

6. **Present the plan** using this format:

```markdown
# Implementation Plan: [Feature Name]

## Overview
[2-3 sentence summary]

## Implementation Steps

### Phase 1: Domain Layer
1. **Entity** (File: domain/entities/foo.dart)
2. **Repository Interface** (File: domain/repositories/foo_repository.dart)
3. **Use Cases** (File: domain/usecases/list_foo_usecase.dart)

### Phase 2: Data Layer
1. **Model** (File: data/models/foo_model.dart)
2. **Data Source** (File: data/datasources/foo_remote_data_source.dart)
3. **Repository Impl** (File: data/repositories/foo_repository.dart)

### Phase 3: Presentation Layer
1. **BLoC** (events + states + bloc)
2. **DI Module**
3. **Page** + **Widgets**

### Phase 4: Tests
1. BLoC tests

## Verification
- flutter analyze → 0 issues
- flutter test → all pass
```

7. **WAIT FOR USER CONFIRMATION** before proceeding to code

## Skills Reference

Read before planning:
- `flutter-bloc` — BLoC creation patterns
- `flutter-api` — HTTP client, data source patterns
- `flutter-di` — DI module and wiring
- `flutter-testing` — test patterns
- `search-first` — research existing code first

⚠️ NEVER code before user confirms the plan.
