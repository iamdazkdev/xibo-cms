---
description: Match HTML/CSS prototype to Flutter widget code. Input prototype file, output matching Flutter UI.
---

# Match UI Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-ui-ux-designer.md` and strictly adopt that persona!

Convert HTML/CSS prototype files into matching Flutter widget code.

// turbo-all

## When to Use

- Designer provides HTML prototype
- Need to match landing page / nav bar / dashboard UI
- Visual 1:1 matching from web prototype to Flutter

## Steps

1. **Read the HTML/CSS prototype file**

2. **Analyze the prototype**:
   - Layout structure (flex, grid → Row, Column, Wrap)
   - Colors → map to `AppColorScheme.of(context).xxx`
   - Spacing → map to `AppSpacing.spX`
   - Radius → map to `AppRadius.xxx`
   - Typography → map to `Theme.of(context).textTheme.xxx`
   - Animations → `AnimatedContainer`, `AnimatedOpacity`
   - Responsive breakpoints → `LayoutBuilder` / `MediaQuery`

3. **Map CSS to Flutter**:

| CSS | Flutter |
|---|---|
| `display: flex; flex-direction: column` | `Column` |
| `display: flex; flex-direction: row` | `Row` |
| `gap: 16px` | `SizedBox(width/height: AppSpacing.sp4)` |
| `padding: 24px` | `Padding(padding: AppSpacing.paddingSp6)` |
| `border-radius: 10px` | `AppRadius.lgRadius` |
| `background: #xxx` | `AppColorScheme.of(context).surface` |
| `color: #xxx` | `AppColorScheme.of(context).textPrimary` |
| `font-size: 14px; font-weight: 600` | `Theme.of(context).textTheme.titleMedium` |
| `box-shadow` | `AppShadows.sm` |
| `hover:` | `MouseRegion` + `setState` |
| `@media (max-width: 768px)` | `LayoutBuilder` or `MediaQuery` |
| `position: fixed` | `Stack` + `Positioned` |
| `overflow: auto` | `SingleChildScrollView` or `ListView` |
| `grid-template-columns` | `GridView` or `Wrap` |

4. **Create Flutter widget** following UI skill patterns:
   - Page wrapper with BlocProvider
   - Extract sub-components into separate widgets
   - Use design system tokens — NO hardcoded values

5. **Verify visual match**:
   - Run app and compare side-by-side
   - Check light + dark mode
   - Check responsive behavior

6. **Verify code**:

```bash
flutter analyze
```

## Rules

- ⛔ NEVER hardcode colors, spacing, fonts — use design system
- ⛔ NEVER use pixel values directly — map to AppSpacing/AppRadius
- ✅ Support both light and dark themes
- ✅ Extract reusable widgets
- ✅ File < 500 lines, split if larger
