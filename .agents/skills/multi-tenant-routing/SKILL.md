---
description: Patterns for setting up GoRouter in a multi-tenant application (Advertiser, Supplier, Admin).
---
# Flutter Multi-Tenant Routing Pattern

## When to Use This Skill
Use this skill when modifying or creating routes for specific portals (Advertiser vs Supplier vs Admin) or when fixing GoRouter `ShellRoute` synchronization issues in the app.

## Concept
The application serves multiple business boundaries from a single app. Each tenant uses a different portal, identified by role or sub-path. 

## Best Practices
1. **Isolated ShellRoutes**: Each portal MUST have its own `ShellRoute` to maintain its distinct AppDrawer, AppBar, and BottomNavigationBar. Do not share a shell across portals that require different layouts.
2. **Path Prefixing**: Use path prefixes (e.g., `/advertiser/dashboard`, `/supplier/dashboard`) to explicitly separate domains and prevent GoRouter path conflicts.
3. **Role Validation Guard**: Use the `.redirect` property of the top-level route to verify `AuthBloc.state.role` matches the portal before granting access. Reject unauthorized portals.
4. **Shared Components, Different Themes**: When sharing UI components between portals, pass configurations or use `Theme.of(context)` rather than hardcoding portal-specific colors.
