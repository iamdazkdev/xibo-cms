---
name: form-validation
description: 'Flutter form validation patterns — TextFormField, validators, error display, BLoC form state.'
---

# Form Validation Skill

## Form Page Pattern

```dart
class CreateStorePage extends StatelessWidget {
  const CreateStorePage({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (_) => sl<CreateStoreBloc>(),
      child: const _CreateStoreForm(),
    );
  }
}

class _CreateStoreForm extends StatefulWidget {
  const _CreateStoreForm();

  @override
  State<_CreateStoreForm> createState() => _CreateStoreFormState();
}

class _CreateStoreFormState extends State<_CreateStoreForm> {
  final _formKey = GlobalKey<FormState>();
  final _nameController = TextEditingController();
  final _addressController = TextEditingController();

  @override
  void dispose() {
    _nameController.dispose();
    _addressController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return BlocListener<CreateStoreBloc, CreateStoreState>(
      listener: (context, state) {
        if (state is CreateStoreSuccess) {
          context.pop();
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text(context.l10n.createSuccess)),
          );
        }
        if (state is CreateStoreError) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text(state.message)),
          );
        }
      },
      child: Form(
        key: _formKey,
        child: Column(
          children: [
            TextFormField(
              controller: _nameController,
              decoration: InputDecoration(labelText: context.l10n.nameLabel),
              validator: (value) {
                if (value == null || value.trim().isEmpty) {
                  return context.l10n.fieldRequired;
                }
                if (value.length < 3) {
                  return context.l10n.fieldMinLength(3);
                }
                return null;
              },
            ),
            // ... more fields
            ElevatedButton(
              onPressed: _submit,
              child: Text(context.l10n.saveButton),
            ),
          ],
        ),
      ),
    );
  }

  void _submit() {
    if (_formKey.currentState!.validate()) {
      context.read<CreateStoreBloc>().add(CreateStoreSubmitted(
        name: _nameController.text.trim(),
        address: _addressController.text.trim(),
      ));
    }
  }
}
```

## Common Validators

```dart
// Required field
validator: (v) => (v == null || v.trim().isEmpty) ? l10n.fieldRequired : null

// Min length
validator: (v) => (v != null && v.length < 3) ? l10n.fieldMinLength(3) : null

// Email
validator: (v) {
  if (v == null || v.isEmpty) return l10n.fieldRequired;
  final emailRegex = RegExp(r'^[\w-.]+@([\w-]+\.)+[\w-]{2,4}$');
  if (!emailRegex.hasMatch(v)) return l10n.invalidEmail;
  return null;
}

// Phone
validator: (v) {
  if (v == null || v.isEmpty) return null; // optional
  final phoneRegex = RegExp(r'^\+?[\d\s-]{10,15}$');
  if (!phoneRegex.hasMatch(v)) return l10n.invalidPhone;
  return null;
}

// Number range
validator: (v) {
  if (v == null || v.isEmpty) return l10n.fieldRequired;
  final n = int.tryParse(v);
  if (n == null) return l10n.invalidNumber;
  if (n < 1 || n > 100) return l10n.numberRange(1, 100);
  return null;
}
```

## BLoC Form State Pattern

```dart
// For complex forms, track form state in BLoC
sealed class CreateStoreState extends Equatable { ... }
final class CreateStoreInitial extends CreateStoreState { ... }
final class CreateStoreSubmitting extends CreateStoreState { ... }
final class CreateStoreSuccess extends CreateStoreState { ... }
final class CreateStoreError extends CreateStoreState {
  final String message;
  final Map<String, String>? fieldErrors; // per-field server errors
}
```

## Rules

- ✅ Use `GlobalKey<FormState>` for form validation
- ✅ Dispose all controllers in `dispose()`
- ✅ Use l10n keys for all validation messages
- ✅ Trim input before submitting
- ✅ Disable submit button during `Submitting` state
- ⛔ NEVER put validation logic in BLoC — keep in widget validators
- ⛔ NEVER hardcode validation messages
