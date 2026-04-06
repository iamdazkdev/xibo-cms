---
name: flutter-navigation
description: 'GoRouter navigation patterns for RMN Flutter app. Use when: adding routes, guards, deep links, portal routing.'
---

# Flutter Navigation Skill

## Router Location

`lib/app.dart` — contains `GoRouter` configuration.

## Multi-Portal Routing

Each portal has its own route tree:

```dart
GoRouter(
  routes: [
    GoRoute(path: '/', builder: (_, __) => const LandingPage()),
    // Auth routes
    GoRoute(path: '/login', builder: (_, __) => const LoginPage()),
    GoRoute(path: '/register', builder: (_, __) => const RegisterPage()),
    // Portal routes
    ShellRoute(
      builder: (_, __, child) => SupplierShell(child: child),
      routes: [
        GoRoute(path: '/supplier/stores', ...),
        GoRoute(path: '/supplier/stores/:id', ...),
        GoRoute(path: '/supplier/devices', ...),
      ],
    ),
    ShellRoute(
      builder: (_, __, child) => AdvertiserShell(child: child),
      routes: [
        GoRoute(path: '/advertiser/content', ...),
        GoRoute(path: '/advertiser/campaigns', ...),
      ],
    ),
  ],
)
```

## Route Parameters

```dart
// Path params
GoRoute(
  path: '/supplier/stores/:id',
  builder: (context, state) {
    final storeId = state.pathParameters['id']!;
    return StoreDetailPage(storeId: storeId);
  },
),

// Query params
final search = state.uri.queryParameters['search'];
```

## Navigation

```dart
// Push
context.push('/supplier/stores/$storeId');

// Go (replace)
context.go('/login');

// Pop
context.pop();
```

## Auth Guards

Route guards check auth state before allowing navigation.
