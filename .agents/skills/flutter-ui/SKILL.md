---
name: flutter-ui
description: 'UI patterns for RMN Flutter app. Use when: creating pages, responsive layouts, theming, shared widgets.'
---

# Flutter UI Skill

## Theme System

Located in `lib/core/ui/theme/`. Supports light + dark mode.

## Page Structure

```dart
class SupplierStoreListPage extends StatelessWidget {
  const SupplierStoreListPage({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (_) => sl<StoreListBloc>()
        ..add(const StoreListFetchRequested()),
      child: const _StoreListView(),
    );
  }
}

class _StoreListView extends StatelessWidget {
  const _StoreListView();

  @override
  Widget build(BuildContext context) {
    return BlocBuilder<StoreListBloc, StoreListState>(
      builder: (context, state) => switch (state) {
        StoreListInitial() || StoreListLoading() => const LoadingWidget(),
        StoreListLoaded(:final stores) => _buildList(stores),
        StoreListError(:final message) => ErrorWidget(message: message),
      },
    );
  }
}
```

## Widget Organization

```
presentation/
├── pages/                    # Full page (route target)
│   └── supplier_store_list_page.dart
└── widgets/                  # Extracted components
    ├── store_list_card.dart
    ├── store_slide_over.dart
    └── store_create_widgets.dart
```

## File Size Rule

- Target: < 500 lines per file
- Warning: > 600 lines — consider splitting
- Extract: logical sections into `widgets/` files

## Responsive Patterns

- Use `LayoutBuilder` or `MediaQuery` for responsive layouts
- Breakpoints: mobile < 768, tablet < 1200, desktop ≥ 1200
- Sidebar visible on desktop, drawer on mobile

## Shared Widgets

Located in `lib/core/ui/widgets/`:
- `loading/` — LoadingCubit, LoadingOverlay
- `sidebar/` — App sidebar + user footer
- Common buttons, dialogs, cards
