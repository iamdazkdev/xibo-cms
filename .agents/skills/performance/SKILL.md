---
name: performance
description: 'Flutter performance patterns — const constructors, lazy loading, efficient rebuilds, image caching.'
---

# Performance Skill

## 1. Const Constructors

```dart
// ✅ ALWAYS use const where possible
const StoreListLoading();
const SizedBox(height: 16);
const EdgeInsets.all(16);

// ✅ Mark widgets as const
class MyWidget extends StatelessWidget {
  const MyWidget({super.key}); // const constructor
}
```

**Rule:** If a widget has no runtime parameters, make it `const`.

## 2. Efficient BLoC Rebuilds

```dart
// ✅ Use buildWhen to limit rebuilds
BlocBuilder<StoreListBloc, StoreListState>(
  buildWhen: (previous, current) => previous != current,
  builder: (context, state) => ...,
)

// ✅ Use BlocSelector for specific field
BlocSelector<StoreListBloc, StoreListState, int>(
  selector: (state) => state is StoreListLoaded ? state.total : 0,
  builder: (context, total) => Text('$total stores'),
)

// ⛔ AVOID: rebuilding entire widget tree unnecessarily
// Split large BlocBuilder into smaller focused ones
```

## 3. List Performance

```dart
// ✅ Use ListView.builder for large lists (lazy rendering)
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) => StoreCard(store: items[index]),
)

// ⛔ AVOID: Column with all items (renders everything)
Column(children: items.map((i) => StoreCard(store: i)).toList())

// ✅ Use itemExtent if items have fixed height
ListView.builder(
  itemCount: items.length,
  itemExtent: 72.0, // fixed height → faster scrolling
  itemBuilder: (context, index) => ...,
)
```

## 4. Image Optimization

```dart
// ✅ Use cacheWidth/cacheHeight to limit decode size
Image.network(
  url,
  cacheWidth: 200,  // decode at smaller size
  cacheHeight: 200,
)

// ✅ Use fadeInDuration for smooth loading
FadeInImage.memoryNetwork(
  placeholder: kTransparentImage,
  image: url,
  fadeInDuration: const Duration(milliseconds: 200),
)
```

## 5. Avoid Unnecessary Rebuilds

```dart
// ✅ Extract child widgets to avoid rebuilding
class ParentWidget extends StatelessWidget {
  const ParentWidget({super.key});

  @override
  Widget build(BuildContext context) {
    return AnimatedContainer(
      duration: const Duration(milliseconds: 300),
      child: const _ExpensiveChild(), // won't rebuild with container
    );
  }
}

// ✅ Use RepaintBoundary for isolated repaints
RepaintBoundary(
  child: CustomPaint(painter: ChartPainter()),
)
```

## 6. Debounce Search Input

```dart
// ✅ Debounce search to avoid excessive API calls
Timer? _debounce;

void _onSearchChanged(String query) {
  _debounce?.cancel();
  _debounce = Timer(const Duration(milliseconds: 500), () {
    context.read<StoreListBloc>().add(
      StoreListFetchRequested(search: query),
    );
  });
}

@override
void dispose() {
  _debounce?.cancel();
  super.dispose();
}
```

## 7. Lazy Initialization

```dart
// ✅ Use late final for expensive computations
late final _dateFormat = DateFormat('dd/MM/yyyy');

// ✅ GetIt LazySingleton — created only when first accessed
sl.registerLazySingleton(() => ExpensiveService());
```

## Performance Checklist

- [ ] All static widgets use `const`
- [ ] Lists use `ListView.builder` (not `Column`)
- [ ] BlocBuilder has `buildWhen` where applicable
- [ ] Search inputs are debounced (300-500ms)
- [ ] Images use `cacheWidth`/`cacheHeight`
- [ ] Heavy custom paint wrapped in `RepaintBoundary`
- [ ] No unnecessary `setState` calls
- [ ] Controllers disposed in `dispose()`
