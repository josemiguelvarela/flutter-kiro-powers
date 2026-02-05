---
inclusion: manual
---

# Performance Optimization Guide

## When to Use

Use this guide when:
- App feels sluggish or janky
- Profiling shows performance issues
- Building complex UIs
- Optimizing for low-end devices

## Performance Fundamentals

### Frame Budget

- Target: 60 FPS = 16.67ms per frame
- Build phase: < 8ms
- Paint phase: < 8ms
- Jank: Any frame > 16.67ms

### DevTools Profiling

```bash
# Run in profile mode
flutter run --profile

# Open DevTools
flutter pub global activate devtools
flutter pub global run devtools
```

## Build Optimization

### 1. Use const Constructors

```dart
// BAD - Creates new instance every build
Widget build(BuildContext context) {
  return Container(
    padding: EdgeInsets.all(16), // New instance
    child: Icon(Icons.star), // New instance
  );
}

// GOOD - Reuses instances
Widget build(BuildContext context) {
  return const Container(
    padding: EdgeInsets.all(16), // Compile-time constant
    child: Icon(Icons.star), // Compile-time constant
  );
}
```

### 2. Extract Widgets

```dart
// BAD - Entire tree rebuilds
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 100 lines of static content
        Text(dynamicValue), // Only this changes
        // 100 more lines of static content
      ],
    );
  }
}

// GOOD - Only dynamic part rebuilds
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        const StaticHeader(), // Never rebuilds
        DynamicContent(value: dynamicValue), // Rebuilds when needed
        const StaticFooter(), // Never rebuilds
      ],
    );
  }
}
```

### 3. Use BlocSelector

```dart
// BAD - Rebuilds on any state change
BlocBuilder<UserCubit, UserState>(
  builder: (context, state) {
    final name = state.whenOrNull(loaded: (user) => user.name);
    return Text(name ?? '');
  },
)

// GOOD - Only rebuilds when name changes
BlocSelector<UserCubit, UserState, String?>(
  selector: (state) => state.whenOrNull(loaded: (user) => user.name),
  builder: (context, name) {
    return Text(name ?? '');
  },
)
```

### 4. Avoid Expensive Operations in Build

```dart
// BAD - Sorts every build
Widget build(BuildContext context) {
  final sortedItems = items..sort((a, b) => a.name.compareTo(b.name));
  return ListView.builder(...);
}

// GOOD - Sort in Cubit
class ItemsCubit extends Cubit<ItemsState> {
  void setItems(List<Item> items) {
    final sorted = List<Item>.from(items)
      ..sort((a, b) => a.name.compareTo(b.name));
    emit(ItemsState.loaded(sorted));
  }
}
```

## Paint Optimization

### 1. RepaintBoundary

```dart
// Isolate frequently repainting widgets
RepaintBoundary(
  child: AnimatedWidget(), // Repaints often
)

// Useful for:
// - Animations
// - Scrolling content
// - Video players
// - Canvas drawings
```

### 2. Avoid Opacity Widget

```dart
// BAD - Creates separate layer
Opacity(
  opacity: 0.5,
  child: Container(color: Colors.red),
)

// GOOD - Use color with alpha
Container(
  color: Colors.red.withOpacity(0.5),
)

// Or use AnimatedOpacity for animations
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: const Duration(milliseconds: 300),
  child: Container(color: Colors.red),
)
```

### 3. Clip Optimization

```dart
// BAD - Expensive clipping
ClipRRect(
  borderRadius: BorderRadius.circular(8),
  child: Image.network(url),
)

// GOOD - Use decoration instead
Container(
  decoration: BoxDecoration(
    borderRadius: BorderRadius.circular(8),
    image: DecorationImage(
      image: NetworkImage(url),
      fit: BoxFit.cover,
    ),
  ),
)
```

## List Optimization

### 1. Use ListView.builder

```dart
// BAD - Builds all items
ListView(
  children: items.map((item) => ItemWidget(item: item)).toList(),
)

// GOOD - Builds only visible items
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) => ItemWidget(item: items[index]),
)
```

### 2. Provide Item Extent

```dart
// GOOD - Helps with scroll calculations
ListView.builder(
  itemCount: items.length,
  itemExtent: 72, // Fixed height
  itemBuilder: (context, index) => ItemWidget(item: items[index]),
)

// Or use prototypeItem
ListView.builder(
  itemCount: items.length,
  prototypeItem: const ItemWidget(item: Item.placeholder()),
  itemBuilder: (context, index) => ItemWidget(item: items[index]),
)
```

### 3. Use Keys Properly

```dart
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ItemWidget(
      key: ValueKey(items[index].id), // Stable key
      item: items[index],
    );
  },
)
```

### 4. Pagination

```dart
class PaginatedList extends StatelessWidget {
  const PaginatedList({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocBuilder<ItemsCubit, ItemsState>(
      builder: (context, state) {
        final items = state.whenOrNull(loaded: (items) => items) ?? [];

        return NotificationListener<ScrollNotification>(
          onNotification: (notification) {
            if (notification is ScrollEndNotification) {
              final metrics = notification.metrics;
              if (metrics.pixels >= metrics.maxScrollExtent - 200) {
                context.read<ItemsCubit>().loadMore();
              }
            }
            return false;
          },
          child: ListView.builder(
            itemCount: items.length + 1,
            itemBuilder: (context, index) {
              if (index == items.length) {
                return const LoadingIndicator();
              }
              return ItemWidget(item: items[index]);
            },
          ),
        );
      },
    );
  }
}
```

## Image Optimization

### 1. Caching

```dart
// Use cached_network_image
CachedNetworkImage(
  imageUrl: url,
  placeholder: (context, url) => const Shimmer(),
  errorWidget: (context, url, error) => const Icon(Icons.error),
)
```

### 2. Resize Images

```dart
// Specify cache dimensions
Image.network(
  url,
  cacheWidth: 200, // Decode at smaller size
  cacheHeight: 200,
)
```

### 3. Precache Images

```dart
@override
void didChangeDependencies() {
  super.didChangeDependencies();
  precacheImage(NetworkImage(imageUrl), context);
}
```

## Async Optimization

### 1. Compute for Heavy Work

```dart
// BAD - Blocks main thread
final result = heavyComputation(data);

// GOOD - Runs in isolate
final result = await compute(heavyComputation, data);
```

### 2. Debounce Search

```dart
class SearchNotifier extends StateNotifier<SearchState> {
  Timer? _debounce;

  void search(String query) {
    _debounce?.cancel();
    _debounce = Timer(const Duration(milliseconds: 300), () {
      _performSearch(query);
    });
  }

  @override
  void dispose() {
    _debounce?.cancel();
    super.dispose();
  }
}
```

## Memory Optimization

### 1. Dispose Controllers

```dart
class MyWidget extends StatefulWidget {
  @override
  State<MyWidget> createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  late final TextEditingController _controller;
  late final ScrollController _scrollController;

  @override
  void initState() {
    super.initState();
    _controller = TextEditingController();
    _scrollController = ScrollController();
  }

  @override
  void dispose() {
    _controller.dispose();
    _scrollController.dispose();
    super.dispose();
  }
}
```

### 2. Cancel Subscriptions

```dart
class _MyWidgetState extends State<MyWidget> {
  StreamSubscription? _subscription;

  @override
  void initState() {
    super.initState();
    _subscription = stream.listen((data) {
      // Handle data
    });
  }

  @override
  void dispose() {
    _subscription?.cancel();
    super.dispose();
  }
}
```

## Profiling Checklist

1. [ ] Run in profile mode, not debug
2. [ ] Check Timeline for jank frames
3. [ ] Look for excessive rebuilds
4. [ ] Check memory for leaks
5. [ ] Profile on real devices
6. [ ] Test on low-end devices

## Quick Reference

**DO**: Measure first | Profile on device | Use const | Lazy load | Cache results

**DON'T**: Optimize prematurely | Ignore dispose | Block main thread | Rebuild entire trees | Skip keys in lists
