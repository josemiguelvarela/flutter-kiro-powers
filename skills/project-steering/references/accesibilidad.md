---
description: Guía de accesibilidad WCAG 2.1 AA para widgets, semántica, contraste y reducción de movimiento
inclusion: always
---

# Accesibilidad — WCAG 2.1 AA

## Principios

- Todo widget interactivo debe tener `Semantics` con label descriptivo.
- Contraste mínimo de texto: 4.5:1 (AA). Modo alto contraste: 7:1 (AAA).
- Tamaños de fuente ajustables: 3 niveles (1.0x, 1.25x, 1.5x).
- Respetar `MediaQuery.textScaleFactor` del sistema.

## Semantics

```dart
// ✅ Correcto: Botón con semántica clara
Semantics(
  label: 'Iniciar grabación',
  button: true,
  child: IconButton(
    icon: const Icon(Icons.mic),
    onPressed: _startRecording,
  ),
)

// ✅ Correcto: Imagen decorativa excluida
Semantics(
  excludeSemantics: true,
  child: Lottie.asset('assets/animations/wave.json'),
)

// ✅ Correcto: Estado dinámico
Semantics(
  label: 'Grabación en curso, $minutes minutos $seconds segundos',
  liveRegion: true,
  child: TimerWidget(duration: elapsed),
)
```

## TalkBack / VoiceOver

- Verificar navegación con TalkBack (Android) y VoiceOver (iOS).
- Orden de foco lógico (lectura de izquierda a derecha, arriba a abajo).
- Acciones personalizadas para gestos complejos.

## Contraste y Colores

```dart
// Verificar contraste programáticamente
double contrastRatio(Color foreground, Color background) {
  final fgLuminance = foreground.computeLuminance();
  final bgLuminance = background.computeLuminance();
  final lighter = max(fgLuminance, bgLuminance);
  final darker = min(fgLuminance, bgLuminance);
  return (lighter + 0.05) / (darker + 0.05);
}
// Mínimo 4.5:1 para texto normal, 3:1 para texto grande
```

## Reducción de Movimiento

Respetar la preferencia del sistema:

```dart
final reduceMotion = MediaQuery.of(context).disableAnimations;
if (reduceMotion) {
  // Usar transiciones instantáneas en lugar de animaciones
}
```
