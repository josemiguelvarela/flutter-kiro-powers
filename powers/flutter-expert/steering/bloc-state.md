---
inclusion: fileMatch
fileMatchPattern: "**/*cubit*.dart,**/*bloc*.dart,**/*state*.dart"
---

# BLoC State Management Guide

## When to Use

**ALWAYS use flutter_bloc for state management in this project.**

- Use **Cubit** for most cases - simpler, less boilerplate
- Use **Bloc** only when you need event-driven architecture with audit trail

## Setup

### Dependencies

Add these packages (use latest versions from pub.dev):

```bash
flutter pub add flutter_bloc bloc equatable
flutter pub add --dev bloc_test
```

## Cubit (Preferred)

Cubit is the recommended approach. See POWER.md for basic template.

### Cubit with Equatable (alternative to freezed)

```dart
import 'package:equatable/equatable.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

abstract class UserState extends Equatable {
  const UserState();
  @override
  List<Object?> get props => [];
}

class UserInitial extends UserState { const UserInitial(); }
class UserLoading extends UserState { const UserLoading(); }
class UserLoaded extends UserState {
  const UserLoaded(this.user);
  final User user;
  @override
  List<Object?> get props => [user];
}
class UserError extends UserState {
  const UserError(this.message);
  final String message;
  @override
  List<Object?> get props => [message];
}
```

## Bloc (When Events Are Needed)

Use Bloc only when you need:
- Event-driven architecture
- Audit trail of all events
- Complex event transformations (debounce, throttle)

### Events

```dart
abstract class AuthEvent extends Equatable {
  const AuthEvent();

  @override
  List<Object?> get props => [];
}

class LoginRequested extends AuthEvent {
  const LoginRequested({
    required this.email,
    required this.password,
  });

  final String email;
  final String password;

  @override
  List<Object?> get props => [email, password];
}

class LogoutRequested extends AuthEvent {
  const LogoutRequested();
}
```

### Bloc Implementation

```dart
class AuthBloc extends Bloc<AuthEvent, AuthState> {
  AuthBloc({required AuthRepository authRepository})
      : _authRepository = authRepository,
        super(const AuthState.initial()) {
    on<LoginRequested>(_onLoginRequested);
    on<LogoutRequested>(_onLogoutRequested);
  }

  final AuthRepository _authRepository;

  Future<void> _onLoginRequested(
    LoginRequested event,
    Emitter<AuthState> emit,
  ) async {
    emit(const AuthState.loading());
    try {
      final user = await _authRepository.login(
        email: event.email,
        password: event.password,
      );
      emit(AuthState.authenticated(user));
    } catch (e) {
      emit(AuthState.error(e.toString()));
    }
  }

  Future<void> _onLogoutRequested(
    LogoutRequested event,
    Emitter<AuthState> emit,
  ) async {
    await _authRepository.logout();
    emit(const AuthState.unauthenticated());
  }
}
```

## Widget Integration

### BlocProvider & MultiBlocProvider

```dart
// Single
BlocProvider(
  create: (_) => getIt<UserCubit>()..loadUser(userId),
  child: const UserScreen(),
)

// Multiple
MultiBlocProvider(
  providers: [
    BlocProvider(create: (_) => getIt<AuthCubit>()),
    BlocProvider(create: (_) => getIt<ThemeCubit>()),
  ],
  child: const MyApp(),
)
```

### BlocBuilder & BlocListener & BlocConsumer

```dart
// Builder - rebuilds UI
BlocBuilder<UserCubit, UserState>(
  builder: (context, state) => state.when(
    initial: () => const SizedBox.shrink(),
    loading: () => const CircularProgressIndicator(),
    loaded: (user) => UserCard(user: user),
    error: (msg) => Text(msg),
  ),
)

// Listener - side effects (navigation, snackbars)
BlocListener<AuthCubit, AuthState>(
  listener: (context, state) {
    state.whenOrNull(
      authenticated: (_) => context.go('/home'),
      error: (msg) => ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text(msg))),
    );
  },
  child: const LoginForm(),
)

// Consumer - both
BlocConsumer<UserCubit, UserState>(
  listener: (context, state) { /* side effects */ },
  builder: (context, state) { /* UI */ },
)
```

### BlocSelector (Granular rebuilds)

```dart
BlocSelector<UserCubit, UserState, String?>(
  selector: (state) => state.whenOrNull(loaded: (user) => user.name),
  builder: (context, name) => Text(name ?? ''),
)
```

### Reading Cubit in Callbacks

```dart
onPressed: () => context.read<CounterCubit>().increment(),
```

## Testing

### Cubit Test

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:mocktail/mocktail.dart';

class MockUserRepository extends Mock implements UserRepository {}

void main() {
  late MockUserRepository mockRepo;
  late UserCubit cubit;

  setUp(() {
    mockRepo = MockUserRepository();
    cubit = UserCubit(mockRepo);
  });
  tearDown(() => cubit.close());

  blocTest<UserCubit, UserState>(
    'emits [loading, loaded] when loadUser succeeds',
    build: () {
      when(() => mockRepo.getUser(any())).thenAnswer((_) async => testUser);
      return cubit;
    },
    act: (c) => c.loadUser('123'),
    expect: () => [const UserState.loading(), UserState.loaded(testUser)],
  );
}
```

### Widget Test with Mock Cubit

```dart
class MockUserCubit extends MockCubit<UserState> implements UserCubit {}

testWidgets('shows loading', (tester) async {
  final cubit = MockUserCubit();
  when(() => cubit.state).thenReturn(const UserState.loading());

  await tester.pumpWidget(MaterialApp(
    home: BlocProvider<UserCubit>.value(value: cubit, child: const UserScreen()),
  ));

  expect(find.byType(CircularProgressIndicator), findsOneWidget);
});
```

## Cubit-to-Cubit Communication

```dart
class FeatureCubit extends Cubit<FeatureState> {
  FeatureCubit({required AuthCubit authCubit})
      : super(const FeatureState.initial()) {
    _authSubscription = authCubit.stream.listen((authState) {
      authState.whenOrNull(
        unauthenticated: () => emit(const FeatureState.initial()),
      );
    });
  }

  late final StreamSubscription<AuthState> _authSubscription;

  @override
  Future<void> close() {
    _authSubscription.cancel();
    return super.close();
  }
}
```

## Quick Reference

**DO**: Prefer Cubit over Bloc | Use freezed for states | One Cubit per feature | Use `context.read` in callbacks | Close subscriptions | Test all transitions

**DON'T**: Use Bloc when Cubit suffices | Emit in constructor | Use mutable state | Skip Equatable/freezed | Put UI logic in Cubit
