# Flutter Powers Collection

## Purpose

This repository contains a collection of Kiro Powers designed specifically for Flutter development. Powers provide the Kiro agent with specialized knowledge, tools, and best practices to accelerate Flutter application development.

## Target Users

- Flutter developers using Kiro IDE
- Teams wanting to standardize Flutter development practices
- Developers looking to accelerate their workflow with AI assistance

## Main Features

1. **UI/Widget Powers**: Guides for creating widgets following Material Design and Cupertino
2. **State Powers**: Patterns for BLoC/Cubit state management
3. **Testing Powers**: Generation and guides for unit tests, widget tests, and integration tests
4. **Architecture Powers**: Clean Architecture, MVVM, and other patterns
5. **Integration Powers**: Firebase, REST APIs, GraphQL

## Project Structure

```text
/
├── powers/                    # Powers directory
│   ├── flutter-expert/        # Main Flutter expert power
│   ├── flutter-widgets/       # Widget creation power
│   ├── flutter-testing/       # Testing power
│   └── ...
├── .kiro/
│   └── steering/              # Development guides
└── README.md
```

## Conventions

- Each Power in its own directory under `powers/`
- Names in kebab-case: `flutter-widget-builder`
- Keywords in English for better compatibility
- All documentation in English
- All code comments in English
