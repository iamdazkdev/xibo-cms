---
description: State management rules for Flutter BLoC, Event patterns, and Immutable States
---
# Flutter AI Rules - BLoC & State Management

## BLoC Rules

### Structure Per Feature

```
features/<feature>/presentation/bloc/
├── <feature>_bloc.dart
├── <feature>_event.dart
└── <feature>_state.dart
```

### Event Rules

```dart
// REQUIRED: sealed class, Equatable, const constructors
sealed class LoginEvent extends Equatable {
  const LoginEvent();

  @override
  List<Object?> get props => [];
}

final class LoginSubmitted extends LoginEvent {
  const LoginSubmitted({required this.email, required this.password});

  final String email;
  final String password;

  @override
  List<Object?> get props => [email, password];
}
```

### State Rules

```dart
// REQUIRED: final class, Equatable, immutable, copyWith
enum LoginStatus { initial, loading, success, failure }

final class LoginState extends Equatable {
  const LoginState({
    this.status = LoginStatus.initial,
    this.email = '',
    this.password = '',
    this.errorMessage,
  });

  final LoginStatus status;
  final String email;
  final String password;
  final String? errorMessage;

  LoginState copyWith({
    LoginStatus? status,
    String? email,
    String? password,
    String? errorMessage,
  }) {
    return LoginState(
      status: status ?? this.status,
      email: email ?? this.email,
      password: password ?? this.password,
      errorMessage: errorMessage ?? this.errorMessage,
    );
  }

  @override
  List<Object?> get props => [status, email, password, errorMessage];
}
```

### BLoC Prohibitions

| Forbidden | Reason |
|-----------|--------|
| `BuildContext` inside BLoC | BLoC is framework-agnostic business logic |
| Direct API/DB calls in BLoC | Must go through Use Case → Repository |
| `emit` after `await` without checking `isClosed` | Causes state emission on disposed BLoC |
| Mutable state fields | All state must be immutable with `copyWith` |
| `Cubit` in `features/` | Features use BLoC with explicit events (Cubit allowed only in `core/`) |
| Navigation inside BLoC | Use `BlocListener` in presentation layer |
| Showing dialogs/snackbars from BLoC | Use `BlocListener` in presentation layer |
| Multiple BLoCs sharing state directly | Use separate BLoCs with use cases, communicate via events |

### Cubit vs BLoC Decision Rule

| Location | State Management | Reason |
|----------|-----------------|--------|
| `core/` | Cubit allowed | Simple state holders, no complex event flows |
| `features/` | BLoC required | Business logic with explicit events and states |
