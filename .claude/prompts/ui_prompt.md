# UI Generation Prompt

Generate Flutter presentation layer (pages + widgets) for a feature with BLoC integration.

## Input

Feature name: {feature_name}
Entity name: {entity_name}
BLoC name: {Feature}Bloc
State class: {Feature}State
Status enum: {Feature}Status { initial, loading, success, failure }
Entity display fields: {list_of_fields_to_show}
Page type: [list | detail | form | dashboard]

## Required Output

### 1. Page File
`lib/features/{feature}/presentation/pages/{feature}_page.dart`

Structure:
```
{Feature}Page (StatelessWidget)
  └── BlocProvider(create: sl<{Feature}Bloc>()..add(Started))
        └── _{Feature}View (private StatelessWidget)
              └── BlocConsumer
                    ├── listener: side effects (navigation, snackbars)
                    └── builder: switch on state.status
                          ├── initial → SizedBox.shrink
                          ├── loading → CircularProgressIndicator
                          ├── success → Content widget
                          └── failure → Error widget with retry
```

Rules:
- Page is public, creates BlocProvider and dispatches initial event
- View is private, contains BlocConsumer/BlocListener/BlocBuilder
- Use `switch` expression on status enum (exhaustive)
- Navigation via `context.goNamed()` inside BlocListener
- Error display via ScaffoldMessenger inside BlocListener
- Access BLoC via `context.read<{Feature}Bloc>()`
- No business logic in build methods
- Use theme values: `Theme.of(context)` for colors, text styles
- Use const constructors everywhere possible

### 2. List Widget (if page type is list)
`lib/features/{feature}/presentation/widgets/{feature}_list_view.dart`

Rules:
- Receives `List<{Entity}>` as final field
- Shows empty state when list is empty
- Uses `RefreshIndicator` wrapping `ListView.builder`
- Each item delegates to a card/tile widget
- Padding from theme or constants, not hardcoded

### 3. Card/Tile Widget
`lib/features/{feature}/presentation/widgets/{feature}_card.dart`

Rules:
- Receives single `{Entity}` as final field
- Uses Card or ListTile
- Displays entity fields
- Handles tap for navigation if needed

### 4. Error Widget
`lib/features/{feature}/presentation/widgets/{feature}_error_view.dart`

Rules:
- Receives `message` (String) and `onRetry` (VoidCallback)
- Centers content with error icon, message text, retry button
- Uses theme colors, not hardcoded values

### 5. Form Page (if page type is form)
`lib/features/{feature}/presentation/pages/{feature}_form_page.dart`

Rules:
- Separate BlocProvider for form BLoC
- Form fields dispatch events on change
- Submit button dispatches submit event
- BlocListener handles success (pop) and failure (snackbar)
- Disable submit during loading

## Constraints

- No business logic in any widget
- No direct API calls
- No setState for app state
- No hardcoded strings (use l10n or constants)
- No hardcoded colors (use Theme.of(context))
- No deep nesting (>4 levels) — extract sub-widgets
- Each file < 300 lines
- All constructors const where possible
