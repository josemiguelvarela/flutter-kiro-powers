---
description: Política zero-network, auditoría de conexiones, no telemetría y permisos mínimos
inclusion: always
---

# Privacidad y Seguridad

## Zero Network Policy

Después de la instalación y descarga inicial de modelos, la app **no realiza conexiones de red** excepto:
- RevenueCat para verificación de compras (único servicio permitido).
- Descarga de modelos AI (solo cuando el usuario lo solicita explícitamente).

## Auditoría de Conexiones

```dart
// Verificar que no hay conexiones no autorizadas
// Usar HttpOverrides en modo debug para auditar
class NetworkAuditOverrides extends HttpOverrides {
  @override
  HttpClient createHttpClient(SecurityContext? context) {
    final client = super.createHttpClient(context);
    // Log todas las conexiones en debug
    assert(() {
      debugPrint('HTTP connection attempt detected');
      return true;
    }());
    return client;
  }
}
```

## No Telemetría

- No analytics de terceros (Firebase Analytics, Mixpanel, etc.).
- No crash reporting externo.
- Logs solo locales y en modo debug.

## Almacenamiento Seguro

- Audio y transcripciones en almacenamiento del dispositivo.
- No cifrado adicional (el cifrado del dispositivo es suficiente).
- Eliminación completa al borrar sesión (audio + texto + artefactos).

## Permisos

Solicitar solo los permisos estrictamente necesarios:
- Micrófono (grabación).
- Almacenamiento (guardar archivos accesibles).
- Calendario (integración opcional).
- Notificaciones (recordatorios opcionales).

```dart
// Solicitar permisos de forma progresiva, no todos al inicio
Future<bool> requestMicrophonePermission() async {
  final status = await Permission.microphone.request();
  return status.isGranted;
}
```
