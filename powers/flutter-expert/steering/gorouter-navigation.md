---
inclusion: fileMatch
fileMatchPattern: "**/*router*.dart,**/routes.dart,**/*navigation*.dart"
---

# GoRouter Navigation Guide

## When to Use

Use GoRouter when you need:
- Declarative routing
- Deep linking support
- Web URL support
- Nested navigation
- Route guards and redirects
- Type-safe route parameters

## Setup

### Dependencies

```bash
flutter pub add go_router
```

### Basic Configuration

```dart
final router = GoRouter(
  initialLocation: '/',
  debugLogDiagnostics: true,
  routes: [
    GoRoute(
      path: '/',
      name: 'home',
      builder: (context, state) => const HomeScreen(),
    ),
    GoRoute(
      path: '/profile/:userId',
      name: 'profile',
      builder: (context, state) {
        final userId = state.pathParameters['userId']!;
        return ProfileScreen(userId: userId);
      },
    ),
  ],
);

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerConfig: router,
    );
  }
}
```

## Route Types

### Simple Routes

```dart
GoRoute(
  path: '/settings',
  name: 'settings',
  builder: (context, state) => const SettingsScreen(),
)
```

### Routes with Path Parameters

```dart
GoRoute(
  path: '/user/:id',
  name: 'user',
  builder: (context, state) {
    final id = state.pathParameters['id']!;
    return UserScreen(userId: id);
  },
)

// Navigate
context.goNamed('user', pathParameters: {'id': '123'});
```

### Routes with Query Parameters

```dart
GoRoute(
  path: '/search',
  name: 'search',
  builder: (context, state) {
    final query = state.uri.queryParameters['q'] ?? '';
    final page = int.tryParse(state.uri.queryParameters['page'] ?? '1') ?? 1;
    return SearchScreen(query: query, page: page);
  },
)

// Navigate
context.goNamed('search', queryParameters: {'q': 'flutter', 'page': '2'});
```

### Nested Routes

```dart
GoRoute(
  path: '/dashboard',
  name: 'dashboard',
  builder: (context, state) => const DashboardScreen(),
  routes: [
    GoRoute(
      path: 'analytics',
      name: 'analytics',
      builder: (context, state) => const AnalyticsScreen(),
    ),
    GoRoute(
      path: 'reports',
      name: 'reports',
      builder: (context, state) => const ReportsScreen(),
    ),
  ],
)

// Results in: /dashboard, /dashboard/analytics, /dashboard/reports
```

## Shell Routes (Nested Navigation)

```dart
ShellRoute(
  builder: (context, state, child) {
    return ScaffoldWithNavBar(child: child);
  },
  routes: [
    GoRoute(
      path: '/home',
      name: 'home',
      builder: (context, state) => const HomeScreen(),
    ),
    GoRoute(
      path: '/explore',
      name: 'explore',
      builder: (context, state) => const ExploreScreen(),
    ),
    GoRoute(
      path: '/profile',
      name: 'profile',
      builder: (context, state) => const ProfileScreen(),
    ),
  ],
)
```

### StatefulShellRoute (Preserve state)

```dart
StatefulShellRoute.indexedStack(
  builder: (context, state, navigationShell) {
    return ScaffoldWithNavBar(navigationShell: navigationShell);
  },
  branches: [
    StatefulShellBranch(
      routes: [
        GoRoute(
          path: '/home',
          builder: (context, state) => const HomeScreen(),
        ),
      ],
    ),
    StatefulShellBranch(
      routes: [
        GoRoute(
          path: '/search',
          builder: (context, state) => const SearchScreen(),
        ),
      ],
    ),
  ],
)
```

## Navigation Methods

### go vs push

```dart
// go - Replaces the entire stack
context.go('/home');

// push - Adds to the stack
context.push('/details');

// Named navigation
context.goNamed('home');
context.pushNamed('details', pathParameters: {'id': '123'});
```

### Pop

```dart
// Pop current route
context.pop();

// Pop with result
context.pop(result);

// Check if can pop
if (context.canPop()) {
  context.pop();
}
```

### Replace

```dart
// Replace current route
context.pushReplacement('/new-route');
context.pushReplacementNamed('newRoute');
```

## Redirects and Guards

### Global Redirect

```dart
final router = GoRouter(
  redirect: (context, state) {
    final isLoggedIn = authService.isLoggedIn;
    final isLoggingIn = state.matchedLocation == '/login';

    if (!isLoggedIn && !isLoggingIn) {
      return '/login';
    }
    if (isLoggedIn && isLoggingIn) {
      return '/home';
    }
    return null; // No redirect
  },
  routes: [...],
);
```

### Route-level Redirect

```dart
GoRoute(
  path: '/admin',
  redirect: (context, state) {
    if (!userService.isAdmin) {
      return '/unauthorized';
    }
    return null;
  },
  builder: (context, state) => const AdminScreen(),
)
```

### Refresh Listenable (Auth state changes)

```dart
final router = GoRouter(
  refreshListenable: authNotifier, // ChangeNotifier
  redirect: (context, state) {
    // Re-evaluated when authNotifier notifies
  },
  routes: [...],
);
```

## Integration with BLoC

```dart
class AppRouter {
  AppRouter(this._authCubit);
  
  final AuthCubit _authCubit;

  late final router = GoRouter(
    refreshListenable: GoRouterRefreshStream(_authCubit.stream),
    redirect: (context, state) {
      final isLoggedIn = _authCubit.state.maybeWhen(
        authenticated: (_) => true,
        orElse: () => false,
      );
      final isAuthRoute = state.matchedLocation.startsWith('/auth');

      if (!isLoggedIn && !isAuthRoute) {
        return '/auth/login';
      }
      if (isLoggedIn && isAuthRoute) {
        return '/home';
      }
      return null;
    },
    routes: [...],
  );
}

class GoRouterRefreshStream extends ChangeNotifier {
  GoRouterRefreshStream(Stream<dynamic> stream) {
    _subscription = stream.listen((_) => notifyListeners());
  }

  late final StreamSubscription<dynamic> _subscription;

  @override
  void dispose() {
    _subscription.cancel();
    super.dispose();
  }
}
```

## Page Transitions

```dart
GoRoute(
  path: '/details',
  pageBuilder: (context, state) {
    return CustomTransitionPage(
      key: state.pageKey,
      child: const DetailsScreen(),
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        return FadeTransition(
          opacity: animation,
          child: child,
        );
      },
    );
  },
)
```

## Error Handling

```dart
final router = GoRouter(
  errorBuilder: (context, state) {
    return ErrorScreen(error: state.error);
  },
  routes: [...],
);
```

## Type-Safe Routes (Recommended)

```dart
// Define route data classes
class UserRouteData extends GoRouteData {
  const UserRouteData({required this.userId});

  final String userId;

  @override
  Widget build(BuildContext context, GoRouterState state) {
    return UserScreen(userId: userId);
  }
}

// Use with go_router_builder for code generation
```

## Quick Reference

**DO**: Use named routes | Centralize router config | Use redirects for auth | Handle deep links | Use ShellRoute for tabs

**DON'T**: Mix go/push randomly | Forget null checks on params | Create infinite redirects | Hardcode paths
