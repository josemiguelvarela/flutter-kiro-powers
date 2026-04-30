---
description: Gestión de estado con BLoC y Cubit, reglas de uso, estados con Equatable y testing
inclusion: always
---

# Gestión de Estado con BLoC/Cubit

## Cuándo usar Cubit vs Bloc

- **Cubit**: Lógica simple, sin eventos complejos (settings, analytics, search).
- **Bloc**: Flujos complejos con múltiples eventos (recording, transcription, export).

```dart
// Cubit — lógica simple
class SettingsCubit extends Cubit<SettingsState> {
  SettingsCubit(this._getSettings) : super(const SettingsInitial());

  final GetSettings _getSettings;

  Future<void> loadSettings() async {
    emit(const SettingsLoading());
    final result = await _getSettings();
    emit(SettingsLoaded(result));
  }
}

// Bloc — flujo complejo con eventos
class RecordingBloc extends Bloc<RecordingEvent, RecordingState> {
  RecordingBloc(this._startRecording) : super(const RecordingInitial()) {
    on<StartRecordingEvent>(_onStart);
    on<PauseRecordingEvent>(_onPause);
    on<StopRecordingEvent>(_onStop);
  }

  final StartRecording _startRecording;

  Future<void> _onStart(StartRecordingEvent event, Emitter<RecordingState> emit) async {
    // ...
  }
}
```

## Reglas

- **Nunca** usar `setState` para estado de aplicación.
- Los estados extienden `Equatable` para comparación eficiente.
- Usar `sealed class` o `freezed` para estados cuando sea posible.

```dart
sealed class RecordingState extends Equatable {
  const RecordingState();

  @override
  List<Object?> get props => [];
}

final class RecordingInitial extends RecordingState {
  const RecordingInitial();
}

final class RecordingInProgress extends RecordingState {
  const RecordingInProgress({required this.duration});
  final Duration duration;

  @override
  List<Object?> get props => [duration];
}
```

## Testing

Usar `bloc_test` para verificar transiciones de estado:

```dart
blocTest<RecordingBloc, RecordingState>(
  'emits [RecordingInProgress] when StartRecordingEvent is added',
  build: () => RecordingBloc(mockStartRecording),
  act: (bloc) => bloc.add(const StartRecordingEvent()),
  expect: () => [isA<RecordingInProgress>()],
);
```
