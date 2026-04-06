---
name: search-first
description: 'Research-before-coding workflow. Search for existing tools, libraries, and patterns before writing custom code.'
---

# Search First Skill

## Principle

Before writing new code, ALWAYS search the existing codebase first.

## Checklist

1. **Search for existing implementations**
   - Grep for similar class/function names
   - Check if a widget/utility already exists in `core/`
   - Look for similar patterns in other features

2. **Check existing packages**
   - Is there already a dependency in `pubspec.yaml` that does this?
   - Don't add new packages for things Flutter SDK already handles

3. **Follow existing patterns**
   - How do similar features implement this? (e.g. how does supplier do it?)
   - Copy the pattern, don't invent a new one

4. **Check documentation**
   - `CLAUDE.md` — project conventions
   - `DEV_WIKI.md` — architecture decisions
   - `docs/API_DOCS/` — available API endpoints

## Common Searches

```bash
# Find existing widgets
grep -r "class.*Widget" lib/core/ui/widgets/ --include="*.dart"

# Find use case patterns
grep -r "class.*UseCase" lib/ --include="*.dart"

# Find existing BLoC patterns
grep -r "class.*Bloc extends" lib/ --include="*.dart"

# Find API endpoints
grep -r "static.*String" lib/core/infra/network/api_endpoints.dart
```
