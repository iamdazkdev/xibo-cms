# BLoC Generation Prompt

Generate a complete BLoC (event + state + bloc) for an existing Flutter feature.

## Input

Feature name: {feature_name}
Entity name: {entity_name}
Use cases available: {list_of_use_cases}
Events needed: {list_of_events_with_params}

## Required Output

Generate exactly three files:

### 1. Event File
`lib/features/{feature}/presentation/bloc/{feature}_event.dart`

Rules:
- `part of '{feature}_bloc.dart';`
- Sealed base class extending Equatable with const constructor
- Each event is a `final class` extending the base
- All fields are final with const constructors
- Override `props` for every event with fields

### 2. State File
`lib/features/{feature}/presentation/bloc/{feature}_state.dart`

Rules:
- `part of '{feature}_bloc.dart';`
- Status enum: `{ initial, loading, success, failure }`
- `final class` extending Equatable
- All fields final with defaults in const constructor
- `copyWith` method returning new instance
- `errorMessage` nullable, cleared on success via `copyWith(errorMessage: null)` pattern
- Override `props` with all fields

### 3. BLoC File
`lib/features/{feature}/presentation/bloc/{feature}_bloc.dart`

Rules:
- Import bloc, equatable, use case, entity
- `part` directives for event and state files
- Constructor receives use cases via named required parameters
- Store use cases as private final fields
- Register all event handlers in constructor body via `on<Event>`
- Each handler is a private async method
- Emit loading state first
- Call use case, then `result.fold` to emit success or failure
- NEVER use BuildContext
- NEVER try/catch — use Either.fold only
- NEVER access data sources directly

## Constraints

- No Cubit — explicit events only
- No mutable fields
- No print statements
- No dynamic types
- File must be < 600 lines
- Each handler method < 50 lines
