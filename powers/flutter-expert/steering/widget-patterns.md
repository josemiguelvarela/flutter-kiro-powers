---
inclusion: fileMatch
fileMatchPattern: "**/*widget*.dart,**/*screen*.dart,**/*page*.dart,**/widgets/**"
---

# Widget Patterns Guide

## When to Use

Use this guide when:
- Building reusable UI components
- Optimizing widget performance
- Implementing common UI patterns
- Following Flutter best practices

## Widget Fundamentals

### Const Constructors

Always use const when possible:

```dart
// GOOD
class MyWidget extends StatelessWidget {
  const MyWidget({
    super.key,
    required this.title,
  });

  final String title;

  @override
  Widget build(BuildContext context) {
    return const Padding(
      padding: EdgeInsets.all(16), // const
      child: Icon(Icons.star), // const
    );
  }
}

// Usage
const MyWidget(title: 'Hello') // Can be const
```

### Widget Keys

Use keys appropriately:

```dart
// For items in lists
ListView.builder(
  itemBuilder: (context, index) {
    return ListTile(
      key: ValueKey(items[index].id), // Unique key
      title: Text(items[index].name),
    );
  },
)

// For GlobalKey (access state/context)
final formKey = GlobalKey<FormState>();
```

## Composition Patterns

### Extract Widgets

Break large widgets into smaller ones:

```dart
// BEFORE - Monolithic widget
class UserProfile extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 50 lines of avatar code
        // 30 lines of info code
        // 40 lines of actions code
      ],
    );
  }
}

// AFTER - Composed widgets
class UserProfile extends StatelessWidget {
  const UserProfile({super.key, required this.user});

  final User user;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        UserAvatar(imageUrl: user.avatarUrl),
        UserInfo(user: user),
        UserActions(userId: user.id),
      ],
    );
  }
}
```

### Builder Pattern

For complex conditional rendering:

```dart
class ConditionalBuilder extends StatelessWidget {
  const ConditionalBuilder({
    super.key,
    required this.condition,
    required this.builder,
    this.fallback,
  });

  final bool condition;
  final WidgetBuilder builder;
  final WidgetBuilder? fallback;

  @override
  Widget build(BuildContext context) {
    if (condition) {
      return builder(context);
    }
    return fallback?.call(context) ?? const SizedBox.shrink();
  }
}
```

### Slot Pattern

For customizable layouts:

```dart
class CardWithSlots extends StatelessWidget {
  const CardWithSlots({
    super.key,
    this.leading,
    required this.title,
    this.subtitle,
    this.trailing,
  });

  final Widget? leading;
  final Widget title;
  final Widget? subtitle;
  final Widget? trailing;

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Row(
          children: [
            if (leading != null) ...[
              leading!,
              const SizedBox(width: 16),
            ],
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  title,
                  if (subtitle != null) subtitle!,
                ],
              ),
            ),
            if (trailing != null) trailing!,
          ],
        ),
      ),
    );
  }
}
```

## Responsive Patterns

### LayoutBuilder

```dart
class ResponsiveWidget extends StatelessWidget {
  const ResponsiveWidget({super.key});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth > 1200) {
          return const DesktopLayout();
        } else if (constraints.maxWidth > 600) {
          return const TabletLayout();
        }
        return const MobileLayout();
      },
    );
  }
}
```

### MediaQuery

```dart
class AdaptiveWidget extends StatelessWidget {
  const AdaptiveWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final size = MediaQuery.sizeOf(context);
    final padding = MediaQuery.paddingOf(context);
    final orientation = MediaQuery.orientationOf(context);

    return Padding(
      padding: EdgeInsets.only(top: padding.top),
      child: orientation == Orientation.portrait
          ? const PortraitLayout()
          : const LandscapeLayout(),
    );
  }
}
```

## Performance Patterns

### RepaintBoundary

Isolate expensive repaints:

```dart
RepaintBoundary(
  child: ComplexAnimatedWidget(),
)
```

### const Widgets

Extract const subtrees:

```dart
class MyScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        const _Header(), // Never rebuilds
        _DynamicContent(data: data), // Rebuilds when data changes
        const _Footer(), // Never rebuilds
      ],
    );
  }
}
```

### Selective Rebuilds with BlocSelector

```dart
BlocSelector<UserCubit, UserState, String?>(
  selector: (state) => state.whenOrNull(loaded: (user) => user.name),
  builder: (context, name) {
    return Text(name ?? '');
  },
)
```

### ListView Optimization

```dart
ListView.builder(
  itemCount: items.length,
  // Provide item extent for better performance
  itemExtent: 72,
  // Or use prototypeItem
  prototypeItem: const ListTile(title: Text('Prototype')),
  itemBuilder: (context, index) {
    return ListTile(
      key: ValueKey(items[index].id),
      title: Text(items[index].name),
    );
  },
)
```

## Common Widget Patterns

### Loading State

```dart
class AsyncContent<T> extends StatelessWidget {
  const AsyncContent({
    super.key,
    required this.asyncValue,
    required this.builder,
    this.loadingBuilder,
    this.errorBuilder,
  });

  final AsyncValue<T> asyncValue;
  final Widget Function(T data) builder;
  final WidgetBuilder? loadingBuilder;
  final Widget Function(Object error, StackTrace stack)? errorBuilder;

  @override
  Widget build(BuildContext context) {
    return asyncValue.when(
      data: builder,
      loading: () =>
          loadingBuilder?.call(context) ??
          const Center(child: CircularProgressIndicator()),
      error: (error, stack) =>
          errorBuilder?.call(error, stack) ??
          Center(child: Text('Error: $error')),
    );
  }
}
```

### Empty State

```dart
class EmptyState extends StatelessWidget {
  const EmptyState({
    super.key,
    required this.icon,
    required this.title,
    this.subtitle,
    this.action,
  });

  final IconData icon;
  final String title;
  final String? subtitle;
  final Widget? action;

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);

    return Center(
      child: Padding(
        padding: const EdgeInsets.all(32),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(icon, size: 64, color: theme.colorScheme.outline),
            const SizedBox(height: 16),
            Text(title, style: theme.textTheme.titleLarge),
            if (subtitle != null) ...[
              const SizedBox(height: 8),
              Text(
                subtitle!,
                style: theme.textTheme.bodyMedium,
                textAlign: TextAlign.center,
              ),
            ],
            if (action != null) ...[
              const SizedBox(height: 24),
              action!,
            ],
          ],
        ),
      ),
    );
  }
}
```

### Shimmer Loading

```dart
class ShimmerLoading extends StatelessWidget {
  const ShimmerLoading({
    super.key,
    required this.child,
    this.isLoading = true,
  });

  final Widget child;
  final bool isLoading;

  @override
  Widget build(BuildContext context) {
    if (!isLoading) return child;

    return Shimmer.fromColors(
      baseColor: Colors.grey[300]!,
      highlightColor: Colors.grey[100]!,
      child: child,
    );
  }
}
```

## Accessibility

### Semantics

```dart
Semantics(
  label: 'Profile picture of John Doe',
  button: true,
  onTap: () => _openProfile(),
  child: CircleAvatar(
    backgroundImage: NetworkImage(user.avatarUrl),
  ),
)
```

### ExcludeSemantics

```dart
ExcludeSemantics(
  child: DecorativeImage(), // Purely decorative
)
```

## Quick Reference

**DO**: Prefer StatelessWidget | Use const everywhere | Extract widgets (<50 lines) | Name clearly | Document public APIs

**DON'T**: Nest too deeply | Rebuild unnecessarily | Ignore keys in lists | Hardcode sizes | Forget accessibility
