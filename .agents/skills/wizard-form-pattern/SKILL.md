---
description: Managing complex form states, multi-step wizards, and form validation using BLoC.
---
# Form Wizard Validation Pattern

## When to Use This Skill
Use this skill when building multi-step forms (e.g., Campaign Creation, Onboarding, Registration) that require complex cross-step validations, progressive saving, or wizard-like back/next navigation.

## Concept
A wizard is a state machine. The current step, intermediate validation status, and partial data payload must all explicitly reside within the BLoC state, completely decoupled from the UI.

## Best Practices
1. **Single Form BLoC**: Use one BLoC for the entire wizard flow, rather than separating BLoCs per step. This ensures partial data is preserved when moving backward.
2. **Current Step Index**: Include `int currentStep` in the state. Dispatch `NextStepRequested` and `PreviousStepRequested` events safely bounded by step limits.
3. **FormZ Package (Optional but Recommended)**: Use `formz` to construct robust `FormzInput` models for each field if validations are complex (e.g., EmailInput, PasswordInput) to abstract validation rules from the state definition itself.
4. **Validation Separation**: The BLoC decides `bool get isValid`. The UI just observes this to enable/disable the "Next" or "Submit" buttons. 
5. **Partial Saves / Drafts**: If a wizard is long, consider saving a draft object to `Hive` or `LocalDataSource` on every `NextStepRequested` to prevent data loss.
