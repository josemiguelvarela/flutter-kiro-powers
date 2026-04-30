# Reglas de logging

## OBLIGATORIO: Usar AppLogger

En todo el código Dart del proyecto, SIEMPRE se debe usar `AppLogger` (`lib/core/utils/logger.dart`) para cualquier tipo de logging.

### Prohibido

- `print()`
- `debugPrint()`
- `log()` de `dart:developer`
- Cualquier otra función de logging que no sea `AppLogger`

### Uso correcto

```dart
import 'package:roast_my_life/core/utils/logger.dart';

AppLogger.info('Mensaje informativo');
AppLogger.debug('Mensaje de debug');
AppLogger.warning('Mensaje de advertencia');
AppLogger.error('Mensaje de error', error: e, stackTrace: stackTrace);
```

### Niveles

- `AppLogger.info` — flujo normal (llamadas a CF, compresión completada, etc.)
- `AppLogger.debug` — detalles internos (respuestas de CF, payloads, etc.)
- `AppLogger.warning` — situaciones inesperadas pero recuperables (fallbacks, reintentos)
- `AppLogger.error` — errores reales (excepciones, fallos irrecuperables)

### Al tocar un archivo existente

Si al modificar un archivo encuentras `print`, `debugPrint` o similares, reemplázalos por `AppLogger` como parte del cambio.
