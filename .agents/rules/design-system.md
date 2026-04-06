# RMN Design System — Flutter Implementation

Single source of truth. Matches the actual `lib/core/theme/` implementation.

---

## 1. COLORS

Source: `lib/core/theme/app_color_scheme.dart` — class `AppColorScheme` (ThemeExtension)

**Usage:** `final colors = AppColorScheme.of(context);` then `colors.primary`, `colors.bg`, etc.

### Brand
| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `colors.primary` | `#1E40AF` | `#3B82F6` | Primary buttons, active elements |
| `colors.primaryHover` | `#1D4ED8` | `#60A5FA` | Hover state on primary |
| `colors.primaryLight` | `#DBEAFE` | `#1E3A5F` | Light primary backgrounds |
| `colors.primaryDark` | `#1E3A8A` | `#93C5FD` | Dark primary variant |
| `colors.secondary` | `#059669` | `#34D399` | Secondary actions |
| `colors.secondaryHover` | `#047857` | `#6EE7B7` | Hover state on secondary |
| `colors.secondaryLight` | `#D1FAE5` | `#064E3B` | Light secondary backgrounds |
| `colors.accent` | `#F59E0B` | `#FBBF24` | Accent/CTA elements |
| `colors.accentHover` | `#D97706` | `#FCD34D` | Hover state on accent |
| `colors.accentLight` | `#FEF3C7` | `#78350F` | Light accent backgrounds |

### Semantic
| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `colors.success` | `#059669` | `#34D399` | Success states |
| `colors.successBg` | `#ECFDF5` | `#064E3B` | Success background |
| `colors.warning` | `#D97706` | `#FBBF24` | Warning states |
| `colors.warningBg` | `#FFFBEB` | `#78350F` | Warning background |
| `colors.error` | `#DC2626` | `#F87171` | Error, destructive |
| `colors.errorBg` | `#FEF2F2` | `#7F1D1D` | Error background |
| `colors.info` | `#2563EB` | `#60A5FA` | Info states |
| `colors.infoBg` | `#EFF6FF` | `#1E3A5F` | Info background |

### Surfaces
| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `colors.bg` | `#F8FAFC` | `#0B0F19` | Scaffold, page background |
| `colors.surface` | `#FFFFFF` | `#111827` | Card, panel background |
| `colors.surfaceHover` | `#F1F5F9` | `#1F2937` | Hover state on surfaces |
| `colors.border` | `#E2E8F0` | `#374151` | Borders, outlines |
| `colors.divider` | `#F1F5F9` | `#1F2937` | Divider lines |

### Text
| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `colors.textPrimary` | `#0F172A` | `#F1F5F9` | Primary body text |
| `colors.textSecondary` | `#64748B` | `#94A3B8` | Secondary/helper text |
| `colors.textMuted` | `#94A3B8` | `#64748B` | Placeholder, disabled text |
| `colors.textInverse` | `#FFFFFF` | `#0F172A` | Text on dark/primary backgrounds |

### Sidebar
| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `colors.sidebarBg` | `#0F172A` | `#030712` | Sidebar background |
| `colors.sidebarText` | `#CBD5E1` | `#94A3B8` | Sidebar default text |
| `colors.sidebarActive` | `rgba(59,130,246,.15)` | `rgba(59,130,246,.20)` | Active sidebar item bg |
| `colors.sidebarActiveText` | `#FFFFFF` | `#FFFFFF` | Active sidebar item text |
| `colors.sidebarHover` | `rgba(255,255,255,.06)` | `rgba(255,255,255,.08)` | Hover sidebar item bg |

### Rules
- **NEVER** hardcode hex values in widgets. Always use `AppColorScheme.of(context).xxx`
- Supports both **light** and **dark** themes via `ThemeExtension`
- Access pattern: `final colors = AppColorScheme.of(context);`
- Chart colors: `#93C5FD`, `#3B82F6`, `#1D4ED8`, `#1E40AF`, `#1E3A8A`

---

## 2. SPACING

Source: `lib/core/theme/app_spacing.dart` — class `AppSpacing`

| Constant | Value | Usage |
|----------|-------|-------|
| `AppSpacing.sp1` | 4px | Tight gaps, icon-to-text |
| `AppSpacing.sp2` | 8px | Component internal padding |
| `AppSpacing.sp3` | 12px | Intermediate spacing |
| `AppSpacing.sp4` | 16px | Standard padding |
| `AppSpacing.sp5` | 20px | Medium spacing |
| `AppSpacing.sp6` | 24px | Section spacing, card padding |
| `AppSpacing.sp8` | 32px | Large section gaps |
| `AppSpacing.sp10` | 40px | Extra large spacing |
| `AppSpacing.sp12` | 48px | Hero/page-level spacing |
| `AppSpacing.sp16` | 64px | Maximum spacing |

### Pre-built EdgeInsets
| Constant | Value |
|----------|-------|
| `AppSpacing.paddingSp2` | `EdgeInsets.all(8)` |
| `AppSpacing.paddingSp3` | `EdgeInsets.all(12)` |
| `AppSpacing.paddingSp4` | `EdgeInsets.all(16)` |
| `AppSpacing.paddingSp6` | `EdgeInsets.all(24)` |
| `AppSpacing.paddingSp8` | `EdgeInsets.all(32)` |
| `AppSpacing.paddingHSp4` | `EdgeInsets.symmetric(horizontal: 16)` |
| `AppSpacing.paddingHSp6` | `EdgeInsets.symmetric(horizontal: 24)` |

### Rules
- **NEVER** use arbitrary values (e.g., `EdgeInsets.all(13)`)
- Use `SizedBox(height: AppSpacing.sp2)` or `SizedBox(width: AppSpacing.sp3)` for gaps
- Use pre-built `AppSpacing.paddingSp4` etc. for common padding

---

## 3. BORDER RADIUS

Source: `lib/core/theme/app_radius.dart` — class `AppRadius`

| Constant | Value | Usage |
|----------|-------|-------|
| `AppRadius.sm` | 4px | Subtle corners |
| `AppRadius.md` | 6px | **Default** — buttons, inputs |
| `AppRadius.lg` | 10px | Cards, popovers |
| `AppRadius.xl` | 16px | Modals, large cards |
| `AppRadius.full` | 9999px | Pills, circles, badges |

### Pre-built BorderRadius
| Constant | Usage |
|----------|-------|
| `AppRadius.smRadius` | `BorderRadius.all(Radius.circular(4))` |
| `AppRadius.mdRadius` | `BorderRadius.all(Radius.circular(6))` |
| `AppRadius.lgRadius` | `BorderRadius.all(Radius.circular(10))` |
| `AppRadius.xlRadius` | `BorderRadius.all(Radius.circular(16))` |
| `AppRadius.fullRadius` | `BorderRadius.all(Radius.circular(9999))` |

---

## 4. TYPOGRAPHY

Source: `lib/core/theme/app_text_styles.dart` — class `AppTextStyles`

Fonts: **Fira Sans** (body) + **Fira Code** (mono)

| TextTheme | Size | Weight | Height | Usage |
|-----------|------|--------|--------|-------|
| `displayLarge` | 36px | 700 | 1.2 | Large display (rarely used) |
| `displayMedium` | 30px | 700 | 1.25 | Medium display (rarely used) |
| `displaySmall` | 28px | 700 | 1.3 | Small display (rarely used) |
| `headlineLarge` | 24px | 700 | 1.3 | Page title (h1), stat values |
| `headlineMedium` | 20px | 600 | 1.35 | Section title (h2) |
| `headlineSmall` | 18px | 600 | 1.35 | Sub-section (h3) |
| `titleLarge` | 16px | 600 | 1.4 | Card titles, subheadings |
| `titleMedium` | 14px | 600 | 1.4 | Labels, bold small text |
| `titleSmall` | 12px | 600 | 1.4 | Small labels |
| `bodyLarge` | 16px | 400 | 1.5 | Large body text |
| `bodyMedium` | 14px | 400 | 1.5 | **Default body text** |
| `bodySmall` | 12px | 400 | 1.5 | Captions, helper text |
| `labelLarge` | 14px | 600 | 1.4 | Button labels |
| `labelMedium` | 12px | 500 | 1.4 | Medium labels |
| `labelSmall` | 11px | 500 | 1.4 | Tags, badges, table headers |

### Rules
- Always use `Theme.of(context).textTheme.bodyMedium` etc.
- **NEVER** hardcode `fontSize` or `fontWeight` in widgets
- Font family constants: `AppTextStyles.fontSans`, `AppTextStyles.fontMono`

---

## 5. SHADOWS

Source: `lib/core/theme/app_shadows.dart` — class `AppShadows`

| Level | Definition | Usage |
|-------|------------|-------|
| `AppShadows.xs` | `Offset(0,1) blur:2 opacity:5%` | Stat cards, subtle elevation |
| `AppShadows.sm` | Two layers: `Offset(0,1) blur:3` + `Offset(0,1) blur:2` | Cards |
| `AppShadows.md` | Two layers: `Offset(0,4) blur:6` + `Offset(0,2) blur:4` | Popovers |
| `AppShadows.lg` | Two layers: `Offset(0,10) blur:15` + `Offset(0,4) blur:6` | Modals, dropdowns |
| `AppShadows.xl` | Two layers: `Offset(0,20) blur:25` + `Offset(0,8) blur:10` | Tooltips, overlays |

Usage: `decoration: BoxDecoration(boxShadow: AppShadows.sm)`

---

## 6. THEME

Source: `lib/core/theme/app_theme.dart` — class `AppTheme`
Source: `lib/core/theme/bloc/theme_cubit.dart` — class `ThemeCubit`

- `AppTheme.light()` — Full light theme with Material 3
- `AppTheme.dark()` — Full dark theme with Material 3
- `AppColorScheme` registered as `ThemeExtension` on both themes
- `ThemeCubit` manages theme toggle (light/dark/system), persists to secure storage
- Theme toggle UI in sidebar footer
- Access: `final colors = AppColorScheme.of(context);`

### Theme files
| File | Purpose |
|------|---------|
| `lib/core/theme/app_color_scheme.dart` | `AppColorScheme` ThemeExtension with light + dark palettes |
| `lib/core/theme/app_theme.dart` | `AppTheme.light()` + `AppTheme.dark()` ThemeData builders |
| `lib/core/theme/bloc/theme_cubit.dart` | `ThemeCubit` with `toggleTheme()` |
| `lib/core/theme/bloc/theme_state.dart` | `ThemeState` with `isDark`, `isLight`, `isSystem` |

---

## 7. COMPONENT PATTERNS

### AppButton (`lib/core/widgets/app_button.dart`)
```
Props: label (String), onPressed (VoidCallback?), isLoading (bool)
Width: full width (double.infinity)
Style: ElevatedButton with AppColorScheme.of(context).primary bg, textInverse text
Radius: 8px (BorderRadius.circular(8))
Padding: 16px vertical
Loading: 20x20 CircularProgressIndicator, disables button
Dark mode: Fully supported via AppColorScheme
```

### AppTextField (`lib/core/widgets/app_text_field.dart`)
```
Props: label, hint, controller, obscureText, keyboardType, validator, onChanged
Widget: TextFormField with InputDecoration
Radius: 8px (BorderRadius.circular(8))
Padding: 16px horizontal, 14px vertical
```

### StatCard (`lib/core/widgets/shared_components.dart`)
```
Props: label, value, change, changePositive, icon, iconColor
Background: colors.surface
Border: colors.border, AppRadius.lgRadius
Shadow: AppShadows.xs
Padding: AppSpacing.paddingSp4
```

### PageHeader (`lib/core/widgets/shared_components.dart`)
```
Props: title, subtitle, actions
Layout: Row with Expanded Column (title + subtitle) + actions Row
Bottom padding: AppSpacing.sp6
```

### AppBadge (`lib/core/widgets/shared_components.dart`)
```
Props: label, type (AppBadgeType enum)
Types: active, success, pending, warning, error, info, draft
Shape: pill (AppRadius.fullRadius)
Structure: dot (6px circle) + text (11px, w500)
```

### AppFilterChip (`lib/core/widgets/shared_components.dart`)
```
Props: label, active, onTap
Active: colors.primaryLight bg, colors.primary border
Inactive: transparent bg, colors.border border
Shape: pill (AppRadius.fullRadius)
```

### SearchInput (`lib/core/widgets/shared_components.dart`)
```
Props: hintText, onChanged
Width: min 240, max 320
Border: colors.border, AppRadius.mdRadius
Icon: search icon, 16px, colors.textMuted
```

### AppDataTable (`lib/core/widgets/shared_components.dart`)
```
Props: columns (List<String>), rows (List<List<Widget>>)
Border: colors.border, AppRadius.lgRadius
Header: colors.bg background, uppercase labelSmall
Rows: hover effect (colors.surfaceHover), divider lines
```

### Other Shared Widgets
| Widget | File |
|--------|------|
| `AppHeader` | `lib/core/widgets/app_header.dart` |
| `AppSidebar` | `lib/core/widgets/app_sidebar.dart` |
| `AppShell` | `lib/core/widgets/app_shell.dart` |
| `AppLoadingOverlay` | `lib/core/widgets/app_loading_overlay.dart` |
| `AppLockWrapper` | `lib/core/widgets/app_lock_wrapper.dart` |
| `AuthLayout` | `lib/core/widgets/auth_layout.dart` |
| `AuthFormPanel` | `lib/core/widgets/auth_form_panel.dart` |
| `ErrorLayout` | `lib/core/widgets/error_layout.dart` |
| `EmptyStateWidget` | `lib/core/widgets/empty_state_widget.dart` |
| `GenericListPage` | `lib/core/widgets/generic_list_page.dart` |
| `InfoBox` | `lib/core/widgets/info_box.dart` |
| `LoadingIndicator` | `lib/core/widgets/loading_indicator.dart` |
| `NetworkAwareWrapper` | `lib/core/widgets/network_aware_wrapper.dart` |
| `RetryWidget` | `lib/core/widgets/retry_widget.dart` |
| `FilterBar` | `lib/core/widgets/shared_components.dart` |
| `SidebarModel` | `lib/core/widgets/sidebar_model.dart` |

---

## 8. INTERACTION STATES

Every interactive element MUST have ALL applicable states:

| State | Visual Change |
|-------|---------------|
| Default | Base appearance |
| Hover | Color shift (e.g., `colors.primaryHover`, `colors.surfaceHover`) |
| Focus | 2px solid ring, primary color |
| Active/Pressed | Deeper color than hover |
| Disabled | Muted colors, reduced opacity, `cursor: not-allowed` |
| Loading | Spinner + disabled behavior (see `AppButton.isLoading`) |
| Error | `colors.error` border + error message |

**NEVER** remove focus indicators.

---

## 9. ACCESSIBILITY (WCAG 2.1 AA)

### Mandatory
- Color contrast >= 4.5:1 (normal text), >= 3:1 (large text)
- All interactive elements keyboard navigable (Tab, Enter, Space, Escape)
- Focus indicators always visible
- Touch targets >= 44x44px (mobile), >= 32x32px (desktop)
- Wrap interactive widgets with `Semantics(button: true, label: '...')`
- Never rely on color alone — always add icon + text
- `Semantics` with `label`, `enabled`, `button`/`link`/`header` flags

### Keyboard
- Tab: move focus forward
- Shift+Tab: move focus backward
- Enter/Space: activate buttons
- Escape: close modals/dropdowns
- Arrow keys: navigate within groups

---

## 10. FILE STRUCTURE

```
lib/
├── main.dart
├── app.dart
├── bootstrap/
│   ├── app_config.dart
│   └── di_container.dart
├── core/
│   ├── theme/                        # Design tokens
│   │   ├── app_color_scheme.dart      # AppColorScheme ThemeExtension (light + dark)
│   │   ├── app_colors.dart           # [DEPRECATED] Legacy static colors
│   │   ├── app_spacing.dart          # AppSpacing class
│   │   ├── app_radius.dart           # AppRadius class
│   │   ├── app_shadows.dart          # AppShadows class
│   │   ├── app_text_styles.dart      # AppTextStyles class + TextTheme
│   │   ├── app_theme.dart             # AppTheme.light() + dark()
│   │   └── bloc/
│   │       ├── theme_cubit.dart      # ThemeCubit (toggle + persist)
│   │       └── theme_state.dart      # ThemeState
│   ├── constants/
│   │   ├── api_constants.dart
│   │   └── app_constants.dart
│   ├── widgets/                      # Shared UI components
│   │   ├── app_button.dart
│   │   ├── app_text_field.dart
│   │   ├── shared_components.dart    # StatCard, PageHeader, AppBadge, etc.
│   │   ├── app_header.dart
│   │   ├── app_sidebar.dart
│   │   ├── app_shell.dart
│   │   ├── auth_layout.dart
│   │   ├── auth_form_panel.dart
│   │   ├── error_layout.dart
│   │   └── ...
│   ├── error/
│   ├── network/
│   ├── routing/
│   ├── services/
│   ├── storage/
│   ├── l10n/
│   ├── biometric/
│   ├── lifecycle/
│   ├── loading/
│   ├── splash/
│   ├── usecases/
│   └── pages/                        # Error/status pages
└── features/                         # Clean Architecture features
    ├── auth/
    │   ├── data/
    │   ├── domain/
    │   └── presentation/
    ├── home/
    ├── admin/
    ├── advertiser/
    ├── supplier/
    └── portal/
```

---

## 11. COMPONENT CHECKLIST

Before shipping any UI component:

- [ ] Uses `AppColorScheme.of(context)` — no hardcoded colors
- [ ] Uses `AppSpacing` — no arbitrary spacing
- [ ] Uses `AppRadius` — no arbitrary radius
- [ ] Uses `Theme.of(context).textTheme` — no hardcoded fonts
- [ ] All applicable states: default, hover, focus, active, disabled, loading, error
- [ ] Focus indicator visible
- [ ] Touch target >= 44px on mobile
- [ ] `Semantics` widget for accessibility
- [ ] Keyboard navigation (Tab, Enter, Space, Escape)
- [ ] Color contrast >= 4.5:1
- [ ] `const` constructors where possible
- [ ] Placed in `lib/core/widgets/` (shared) or `lib/features/<name>/presentation/widgets/` (feature-specific)
