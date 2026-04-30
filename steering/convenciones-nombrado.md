---
description: Convenciones de nombrado para archivos, clases, variables e imports del proyecto
inclusion: always
---

# Convenciones de Nombrado

## Archivos

Todos los archivos usan `snake_case` con sufijos descriptivos:

| Tipo | Sufijo | Ejemplo |
|------|--------|---------|
| Screen | `_screen.dart` | `recording_screen.dart` |
| Widget | `_widget.dart` | `audio_wave_widget.dart` |
| BLoC | `_bloc.dart` | `recording_bloc.dart` |
| Cubit | `_cubit.dart` | `settings_cubit.dart` |
| State | `_state.dart` | `recording_state.dart` |
| Event | `_event.dart` | `recording_event.dart` |
| Repository (interfaz) | `_repository.dart` | `abstract_recording_repository.dart` |
| Repository (impl) | `_repository_impl.dart` | `recording_repository_impl.dart` |
| Model | `_model.dart` | `recording_session_model.dart` |
| Service | `_service.dart` | `language_service.dart` |
| DataSource | `_data_source.dart` | `whisper_data_source.dart` |
| Entity | sin sufijo especial | `recording_session.dart` |
| Use Case | sin sufijo especial | `start_recording.dart` |

## Clases y Variables

```dart
// PascalCase para clases
class RecordingSession {}
class TranscriptionBloc {}

// camelCase para variables y métodos
final sessionId = uuid.v4();
Future<void> startRecording() async {}

// camelCase o SCREAMING_SNAKE_CASE para constantes
const maxRecordingDuration = Duration(hours: 4);
const MAX_MARKERS = 500;
```

## Imports

Siempre usar imports de paquete (no relativos):

```dart
// ✅ Correcto
import 'package:aitranscribe/features/recording/domain/entities/recording_session.dart';

// ❌ Incorrecto
import '../../domain/entities/recording_session.dart';
```
