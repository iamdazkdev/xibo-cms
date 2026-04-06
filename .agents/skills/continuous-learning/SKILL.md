---
name: continuous-learning
description: 'Extract reusable patterns from coding sessions and save as learned skills for future reference.'
---

# Continuous Learning Skill

## Purpose

After completing a significant task, extract reusable patterns and save them for future sessions.

## When to Use

- After completing a complex feature
- After fixing a tricky bug
- After discovering a new pattern
- After refactoring that revealed insights

## What to Capture

### 1. Code Patterns
- API response parsing patterns (e.g. nested `data.items`)
- Widget composition patterns
- State management patterns
- Error handling edge cases

### 2. Gotchas & Pitfalls
- Constructor parameter order (positional vs named)
- Field name mismatches (API snake_case → Dart camelCase)
- Generated code quirks (`build_runner` issues)
- Platform-specific behavior (web vs mobile)

### 3. Project-Specific Knowledge
- Which entities use which field names
- Which modules register which dependencies
- API response structure per service
- Test data patterns that work

## How to Save

After a session, create or update a learned pattern file:

```markdown
## Pattern: [Name]
**Context:** When [situation]
**Problem:** [what went wrong or was tricky]
**Solution:** [correct approach]
**Example:**
\`\`\`dart
// correct code
\`\`\`
```

## Storage Location

Save learned patterns to:
- `.agents/skills/*/SKILL.md` — update existing skills with new examples
- `.agents/rules/*.md` — update rules with new edge cases
- `DEV_WIKI.md` — project-level knowledge

## Integration with Backend

When learning involves both frontend and backend:
- Document the full-stack pattern
- Note API contract expectations
- Reference backend error codes
