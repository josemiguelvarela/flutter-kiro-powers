---
description: Rendimiento on-device AI con Isolates, gestión de memoria, límites de CPU y monitoreo térmico
inclusion: always
---

# Rendimiento On-Device AI

## Isolates para Modelos

Ejecutar toda inferencia AI en `Isolate` dedicado para no bloquear el hilo principal:

```dart
// Ejecutar transcripción en Isolate
Future<TranscriptionResult> transcribeInIsolate(String audioPath) async {
  return await Isolate.run(() {
    // Cargar modelo y ejecutar inferencia
    return _performTranscription(audioPath);
  });
}
```

Para operaciones con comunicación bidireccional (streaming), usar `ReceivePort`/`SendPort`:

```dart
final receivePort = ReceivePort();
await Isolate.spawn(_transcriptionWorker, receivePort.sendPort);
final sendPort = await receivePort.first as SendPort;
// Enviar chunks de audio al isolate
sendPort.send(audioChunk);
```

## Gestión de Memoria

- Liberar modelos de memoria cuando no estén en uso.
- Monitorear uso de RAM antes de cargar modelos grandes.
- Un solo modelo cargado a la vez (no cargar whisper + LLM simultáneamente).

```dart
// Verificar RAM disponible antes de cargar modelo
final availableRam = await getAvailableMemory();
if (availableRam < model.requiredRam) {
  throw ResourceFailure('RAM insuficiente para el modelo ${model.name}');
}
```

## Límites de CPU

- No exceder 80% de uso de CPU sostenido.
- Permitir pausar/cancelar operaciones AI largas.
- Mostrar progreso al usuario durante procesamiento.

## Monitoreo de Temperatura

- Detectar throttling térmico del dispositivo.
- Reducir carga de trabajo si la temperatura es alta.
- Notificar al usuario si el procesamiento se ralentiza por temperatura.

```dart
// Monitorear y adaptar carga
if (await isDeviceThrottling()) {
  // Reducir tamaño de chunks o pausar procesamiento
  await Future.delayed(const Duration(seconds: 5));
}
```
