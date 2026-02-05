---
inclusion: fileMatch
fileMatchPattern: "**/pubspec.yaml,**/main.dart,**/injection*.dart,**/di/**"
---

# Project Structure Guide

## Feature-First Architecture

```text
lib/
├── main.dart
├── app/
│   ├── app.dart              # MaterialApp config
│   └── router.dart           # GoRouter config
├── core/
│   ├── di/injection.dart     # get_it setup
│   ├── network/api_client.dart
│   └── widgets/              # Shared widgets
└── features/
    └── feature_name/
        ├── data/
        │   ├── datasources/
        │   ├── models/
        │   └── repositories/
        ├── domain/
        │   ├── entities/
        │   └── repositories/
        └── presentation/
            ├── cubit/
            ├── pages/
            └── widgets/

assets/translations/
├── en.json
└── es.json
```

## Dependency Injection (get_it)

```dart
import 'package:get_it/get_it.dart';

final getIt = GetIt.instance;

Future<void> configureDependencies() async {
  // Core
  getIt.registerLazySingleton<ApiClient>(() => ApiClient());

  // Repositories
  getIt.registerLazySingleton<AuthRepository>(() => AuthRepositoryImpl(getIt()));

  // Cubits (factory = new instance)
  getIt.registerFactory(() => AuthCubit(getIt()));
}
```

## main.dart

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await configureDependencies();
  await EasyLocalization.ensureInitialized();

  runApp(EasyLocalization(
    supportedLocales: const [Locale('en'), Locale('es')],
    path: 'assets/translations',
    fallbackLocale: const Locale('en'),
    child: const MyApp(),
  ));
}
```

## pubspec.yaml

Use latest versions from [pub.dev](https://pub.dev):

```yaml
dependencies:
  flutter_bloc:
  go_router:
  easy_localization:
  get_it:
  freezed_annotation:
  dio:
  dartz:
  equatable:

dev_dependencies:
  build_runner:
  freezed:
  json_serializable:
  bloc_test:
  mocktail:

flutter:
  assets:
    - assets/translations/
```

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Files | snake_case | `user_repository.dart` |
| Classes | PascalCase | `UserRepository` |
| Variables | camelCase | `userRepository` |

## Quick Reference

**DO**: Feature-first | Clear layer boundaries | Use get_it for DI | Single responsibility

**DON'T**: Mix layers | Skip abstractions | Hardcode dependencies
