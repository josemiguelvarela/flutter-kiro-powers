---
description: Clean Architecture por feature con reglas de dependencia, estructura de capas e inyección de dependencias
inclusion: always
---

# Arquitectura del Proyecto — Clean Architecture por Feature

## Estructura General

Cada feature es un módulo independiente con 4 capas:

```
lib/features/{feature}/
├── domain/          # Entidades, interfaces de repositorio, use cases
│   ├── entities/
│   ├── repositories/
│   └── usecases/
├── data/            # Implementaciones, modelos, datasources
│   ├── models/
│   ├── datasources/
│   └── repositories/
├── presentation/    # Screens, widgets
│   ├── screens/
│   └── widgets/
└── bloc/            # BLoC y Cubit del feature
```

## Reglas de Dependencia

- **domain/** NO importa de data/ ni presentation/ ni bloc/.
- **data/** importa de domain/ (implementa interfaces).
- **bloc/** importa de domain/ (usa use cases).
- **presentation/** importa de bloc/ (observa estados).

```dart
// ✅ Correcto: Use case depende solo de la interfaz del repositorio
class GetSessions {
  final AbstractHistoryRepository repository;
  const GetSessions(this.repository);

  Future<List<MeetingSession>> call() => repository.getSessions();
}

// ❌ Incorrecto: Use case importando implementación concreta
import 'package:aitranscribe/features/history/data/repositories/history_repository_impl.dart';
```

## Código Compartido

El directorio `lib/core/` contiene código transversal:

```
lib/core/
├── constants/    # Constantes globales
├── theme/        # Material Design 3
├── router/       # GoRouter
├── di/           # get_it + injectable
├── database/     # SQLite setup
├── storage/      # File system helpers
├── platform/     # Platform channels
├── widgets/      # Widgets reutilizables
├── animations/   # Lottie, shimmer
├── extensions/   # Extension methods
├── errors/       # Failure classes
└── utils/        # Utilidades
```

## Inyección de Dependencias

Usar `get_it` + `injectable`. Registrar dependencias por feature:

```dart
@InjectableInit()
Future<void> configureDependencies() async => getIt.init();
```
