---
inclusion: fileMatch
fileMatchPattern: "**/translations/**,**/*.arb,**/l10n/**,**/*localization*.dart"
---

# Localization Guide (easy_localization)

## When to Use

Use this guide when:

- Setting up localization in a new project
- Adding new translations
- Implementing language switching
- Handling pluralization and formatting

## Setup

### Dependencies

```bash
flutter pub add easy_localization
```

### Project Structure

```
assets/
└── translations/
    ├── en.json
    ├── es.json
    └── ... (other languages)
```

### pubspec.yaml Assets

```yaml
flutter:
  assets:
    - assets/translations/
```

### App Initialization

```dart
import 'package:easy_localization/easy_localization.dart';
import 'package:flutter/material.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await EasyLocalization.ensureInitialized();

  runApp(
    EasyLocalization(
      supportedLocales: const [
        Locale('en'),
        Locale('es'),
      ],
      path: 'assets/translations',
      fallbackLocale: const Locale('en'),
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      localizationsDelegates: context.localizationDelegates,
      supportedLocales: context.supportedLocales,
      locale: context.locale,
      home: const HomeScreen(),
    );
  }
}
```

## Translation File Structure

### Recommended JSON Structure

```json
{
  "app": {
    "name": "My App",
    "version": "Version {0}"
  },
  "common": {
    "loading": "Loading...",
    "retry": "Retry",
    "cancel": "Cancel",
    "save": "Save",
    "delete": "Delete",
    "edit": "Edit",
    "confirm": "Confirm",
    "back": "Back",
    "next": "Next",
    "done": "Done"
  },
  "errors": {
    "generic": "An error occurred: {0}",
    "network": "Network error. Please check your connection.",
    "not_found": "Resource not found.",
    "unauthorized": "You are not authorized to perform this action.",
    "validation": "Please check your input."
  },
  "auth": {
    "login": "Log In",
    "logout": "Log Out",
    "register": "Register",
    "email": "Email",
    "password": "Password",
    "forgot_password": "Forgot Password?",
    "welcome_back": "Welcome back, {0}!"
  },
  "home": {
    "title": "Home",
    "greeting": "Hello, {0}!"
  },
  "settings": {
    "title": "Settings",
    "language": "Language",
    "theme": "Theme",
    "notifications": "Notifications"
  }
}
```

## Usage Patterns

### Basic Translation

```dart
import 'package:easy_localization/easy_localization.dart';

// Using .tr() extension
Text('common.loading'.tr())

// Using tr() function
Text(tr('common.loading'))
```

### Translation with Arguments

```dart
// JSON: "greeting": "Hello, {0}!"
Text('home.greeting'.tr(args: ['John']))
// Output: "Hello, John!"

// Multiple arguments
// JSON: "message": "Hello {0}, you have {1} messages"
Text('message'.tr(args: ['John', '5']))
```

### Named Arguments

```dart
// JSON: "welcome": "Welcome {name} to {app}"
Text('welcome'.tr(namedArgs: {'name': 'John', 'app': 'MyApp'}))
```

### Pluralization

```dart
// JSON structure for plurals
{
  "items": {
    "zero": "No items",
    "one": "One item",
    "other": "{} items"
  }
}

// Usage
Text('items'.plural(count))
```

### Gender Support

```dart
// JSON structure
{
  "greeting": {
    "male": "Welcome Mr. {0}",
    "female": "Welcome Ms. {0}",
    "other": "Welcome {0}"
  }
}

// Usage
Text('greeting'.tr(gender: 'male', args: ['Smith']))
```

## Language Switching

### Change Language

```dart
// Change to Spanish
context.setLocale(const Locale('es'));

// Change to English
context.setLocale(const Locale('en'));
```

### Get Current Locale

```dart
final currentLocale = context.locale;
final languageCode = context.locale.languageCode;
```

### Language Selector Widget

```dart
/// Widget for selecting the app language.
class LanguageSelector extends StatelessWidget {
  const LanguageSelector({super.key});

  @override
  Widget build(BuildContext context) {
    return DropdownButton<Locale>(
      value: context.locale,
      items: context.supportedLocales.map((locale) {
        return DropdownMenuItem(
          value: locale,
          child: Text(_getLanguageName(locale.languageCode)),
        );
      }).toList(),
      onChanged: (locale) {
        if (locale != null) {
          context.setLocale(locale);
        }
      },
    );
  }

  String _getLanguageName(String code) {
    switch (code) {
      case 'en':
        return 'English';
      case 'es':
        return 'Español';
      default:
        return code;
    }
  }
}
```

## Integration with GoRouter

```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => const HomeScreen(),
    ),
  ],
);

// In MaterialApp.router
MaterialApp.router(
  localizationsDelegates: context.localizationDelegates,
  supportedLocales: context.supportedLocales,
  locale: context.locale,
  routerConfig: router,
)
```

## Testing with Localization

```dart
import 'package:easy_localization/easy_localization.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  setUp(() async {
    // Initialize easy_localization for tests
    await EasyLocalization.ensureInitialized();
  });

  testWidgets('displays translated text', (tester) async {
    await tester.pumpWidget(
      EasyLocalization(
        supportedLocales: const [Locale('en')],
        path: 'assets/translations',
        fallbackLocale: const Locale('en'),
        child: const MaterialApp(
          home: MyWidget(),
        ),
      ),
    );

    await tester.pumpAndSettle();

    expect(find.text('Loading...'), findsOneWidget);
  });
}
```

## Quick Reference

**DO**: Organize by feature | Use nested keys | Keep keys descriptive | Test all locales

**DON'T**: Hardcode strings | Forget fallbackLocale | Skip ensureInitialized() | Use dynamic keys | Forget assets in pubspec
