---
description: GoRouter configuration, App Shell routing, and Navigation patterns
---
# Flutter AI Rules - Routing & Navigation

## Routing Rules
- All routes defined in `core/routing/`.
- Use named routes. Never hardcode path strings in widgets.
- Navigation from BLoC response: use `BlocListener` → `context.goNamed(...)`.
- Route guards use `redirect` parameter on `GoRouter`, not middleware in BLoC.

```dart
// core/routing/app_router.dart
final GoRouter appRouter = GoRouter(
  initialLocation: '/',
  routes: [
    GoRoute(
      path: '/',
      name: 'splash',
      builder: (context, state) => const SplashPage(),
    ),
    GoRoute(
      path: '/login',
      name: 'login',
      builder: (context, state) => const LoginPage(),
    ),
    ShellRoute(
      builder: (context, state, child) => MainShell(child: child),
      routes: [
        GoRoute(
          path: '/home',
          name: 'home',
          builder: (context, state) => const HomePage(),
        ),
      ],
    ),
  ],
);
```
