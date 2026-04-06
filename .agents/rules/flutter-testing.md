---
description: Unit testing, Widget testing, BLoC tests, and Test-Driven Development workflow
---
# Flutter AI Rules - Testing

## Testing Rules

### Minimum Coverage: 80%

| Layer | Test Type | What to Test |
|-------|-----------|-------------|
| Domain | Unit | Use cases, entities |
| Data | Unit | Models (fromJson/toJson/toEntity), repository impl |
| Data | Unit | Data sources (mock Dio) |
| Presentation | Unit | BLoC (events → states) |
| Presentation | Widget | Pages and widgets (pump with mocked BLoC) |
| Full | Integration | Critical flows end-to-end |

### TDD Workflow (MANDATORY)

1. Write test first (RED) — test should FAIL
2. Write minimal implementation (GREEN) — test should PASS
3. Refactor (IMPROVE) — verify coverage 80%+

### BLoC Test Pattern

```dart
blocTest<LoginBloc, LoginState>(
  'emits [loading, success] when login succeeds',
  setUp: () {
    when(() => mockLoginUseCase(any()))
        .thenAnswer((_) async => const Right(testUser));
  },
  build: () => LoginBloc(loginUseCase: mockLoginUseCase),
  act: (bloc) => bloc.add(
    const LoginSubmitted(email: 'test@test.com', password: 'password'),
  ),
  expect: () => [
    const LoginState(status: LoginStatus.loading),
    const LoginState(status: LoginStatus.success),
  ],
);
```

### Required Test Packages

```yaml
dev_dependencies:
  bloc_test: ^9.0.0
  mocktail: ^1.0.0
  flutter_test:
    sdk: flutter
```

### Test Anti-Patterns

| Anti-Pattern | Correct Approach |
|-------------|-----------------|
| Testing implementation details | Test observable behavior (emitted states) |
| Tests depending on each other | Independent setup per test |
| Asserting too little | Verify specific state fields |
| Not mocking external dependencies | Mock all data sources, repositories, use cases |
| Testing widget internals | Test through BLoC state emissions |
| Skipping error path tests | Always test both success and failure |
