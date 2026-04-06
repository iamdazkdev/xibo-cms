---
description: Ensure robust widget testing with BLoC mocking and golden UI checks.
---
# Widget Test Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-qa-reviewer.md` and strictly adopt that persona!

Create and execute Widget Tests for complex layouts, mimicking the BLoC state via `mocktail`.

## Prerequisites
- Requires `mocktail` and `bloc_test` packages.

## Steps

1. **Setup Mock BLoC**:
   Create a mock class for the target BLoC.
   ```dart
   class MockLoginBloc extends MockBloc<LoginEvent, LoginState> implements LoginBloc {}
   ```

2. **Register Fallbacks** (if complex states involved):
   ```dart
   setUpAll(() {
     registerFallbackValue(const LoginState());
   });
   ```

3. **Pump the Widget**:
   Use `WidgetTester` to pump the widget inside a `MaterialApp` and `BlocProvider.value`.
   ```dart
   await tester.pumpWidget(
     MaterialApp(
       home: BlocProvider<LoginBloc>.value(
         value: mockLoginBloc,
         child: const LoginForm(),
       ),
     ),
   );
   ```

4. **Simulate States**:
   Use `when(() => mockLoginBloc.state).thenReturn(...)` to simulate loading, success, and failure states.

5. **Assert UI Elements**:
   Verify the presence of progress indicators or error snackbars.
   ```dart
   expect(find.byType(CircularProgressIndicator), findsOneWidget);
   ```

// turbo
6. **Execute Single Test**:
```bash
flutter test test/features/.../{target}_test.dart
```
