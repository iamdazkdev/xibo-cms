---
description: Presentation rules, Widget structure, and anti-patterns for Flutter UI
---
# Flutter AI Rules - UI & Widgets

## Widget Structure

```dart
class LoginPage extends StatelessWidget {
  const LoginPage({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (_) => sl<LoginBloc>(),
      child: const _LoginView(),
    );
  }
}

class _LoginView extends StatelessWidget {
  const _LoginView();

  @override
  Widget build(BuildContext context) {
    return BlocListener<LoginBloc, LoginState>(
      listenWhen: (previous, current) => previous.status != current.status,
      listener: (context, state) {
        if (state.status == LoginStatus.success) {
          context.goNamed('home');
        }
        if (state.status == LoginStatus.failure) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text(state.errorMessage ?? 'Login failed')),
          );
        }
      },
      child: BlocBuilder<LoginBloc, LoginState>(
        builder: (context, state) {
          return Scaffold(
            body: switch (state.status) {
              LoginStatus.loading => const Center(
                  child: CircularProgressIndicator(),
                ),
              _ => const _LoginForm(),
            },
          );
        },
      ),
    );
  }
}
```

## Widget Prohibitions

| Forbidden | Required Instead |
|-----------|-----------------|
| Business logic in `build()` | Dispatch event to BLoC |
| Direct API calls from widget | Dispatch event → BLoC → UseCase → Repository |
| `setState` for app state | BLoC only |
| Accessing repository from widget | Access BLoC via `context.read<T>()` |
| Hardcoded strings in UI | Use `l10n` / constants |
| Hardcoded colors/sizes | Use `Theme.of(context)` |
| Deep widget nesting (>4 levels) | Extract sub-widgets |
| Inline style values | Use theme extensions or named constants |
| God widgets (>300 lines) | Extract into sub-widgets |
| `Image.network` for URLs | `CachedNetworkImage` with `memCacheHeight` |
