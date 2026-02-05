---
inclusion: fileMatch
fileMatchPattern: "**/*_test.dart,**/test/**,**/integration_test/**"
---

# Testing Guide

## Setup

```bash
flutter pub add --dev bloc_test mocktail
```

## Unit Tests

```dart
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';

class MockUserRepository extends Mock implements UserRepository {}

void main() {
  late MockUserRepository mockRepo;
  late UserService service;

  setUp(() {
    mockRepo = MockUserRepository();
    service = UserService(mockRepo);
  });

  test('returns user when repository succeeds', () async {
    when(() => mockRepo.getUser(any())).thenAnswer((_) async => testUser);
    final result = await service.getUser('1');
    expect(result, equals(testUser));
    verify(() => mockRepo.getUser('1')).called(1);
  });

  test('throws when repository fails', () async {
    when(() => mockRepo.getUser(any())).thenThrow(Exception('Network error'));
    expect(() => service.getUser('1'), throwsA(isA<Exception>()));
  });
}
```

## Cubit Tests

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:mocktail/mocktail.dart';

class MockUserRepository extends Mock implements UserRepository {}

void main() {
  late MockUserRepository mockRepo;

  setUp(() => mockRepo = MockUserRepository());

  blocTest<UserCubit, UserState>(
    'emits [loading, loaded] when loadUser succeeds',
    build: () {
      when(() => mockRepo.getUser(any())).thenAnswer((_) async => testUser);
      return UserCubit(mockRepo);
    },
    act: (cubit) => cubit.loadUser('123'),
    expect: () => [const UserState.loading(), UserState.loaded(testUser)],
  );

  blocTest<UserCubit, UserState>(
    'emits [loading, error] when loadUser fails',
    build: () {
      when(() => mockRepo.getUser(any())).thenThrow(Exception('Error'));
      return UserCubit(mockRepo);
    },
    act: (cubit) => cubit.loadUser('123'),
    expect: () => [
      const UserState.loading(),
      isA<UserState>().having((s) => s.whenOrNull(error: (m) => m), 'error', contains('Error')),
    ],
  );
}
```

## Widget Tests

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:mocktail/mocktail.dart';

class MockUserCubit extends MockCubit<UserState> implements UserCubit {}

void main() {
  late MockUserCubit mockCubit;

  setUp(() => mockCubit = MockUserCubit());

  testWidgets('shows loading indicator', (tester) async {
    when(() => mockCubit.state).thenReturn(const UserState.loading());
    await tester.pumpWidget(MaterialApp(
      home: BlocProvider<UserCubit>.value(value: mockCubit, child: const UserScreen()),
    ));
    expect(find.byType(CircularProgressIndicator), findsOneWidget);
  });

  testWidgets('shows user name when loaded', (tester) async {
    when(() => mockCubit.state).thenReturn(UserState.loaded(testUser));
    await tester.pumpWidget(MaterialApp(
      home: BlocProvider<UserCubit>.value(value: mockCubit, child: const UserScreen()),
    ));
    expect(find.text(testUser.name), findsOneWidget);
  });
}
```

## Integration Tests

```yaml
# pubspec.yaml
dev_dependencies:
  integration_test:
    sdk: flutter
```

```dart
// integration_test/app_test.dart
import 'package:integration_test/integration_test.dart';
import 'package:my_app/main.dart' as app;

void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();

  testWidgets('login flow', (tester) async {
    app.main();
    await tester.pumpAndSettle();

    await tester.enterText(find.byKey(const Key('email_field')), 'test@example.com');
    await tester.enterText(find.byKey(const Key('password_field')), 'password123');
    await tester.tap(find.byKey(const Key('login_button')));
    await tester.pumpAndSettle();

    expect(find.text('Welcome'), findsOneWidget);
  });
}
```

## Test File Structure

```text
test/
├── unit/          # Business logic tests
├── cubit/         # Cubit tests
├── widget/        # Widget tests
└── helpers/       # Shared mocks and helpers
```

## Quick Reference

**DO**: Arrange-Act-Assert | One assertion per test | Mock external deps | Test edge cases

**DON'T**: Test implementation details | Skip tearDown | Use real repositories | Forget pumpAndSettle
