---
description: Guía de animaciones Lottie, micro-interacciones con Flutter Animations API y staggered lists
inclusion: always
---

# Lottie y Animaciones

## Cuándo usar Lottie vs código

| Caso | Usar |
|------|------|
| Animaciones complejas (onboarding, splash, éxito) | Lottie |
| Micro-interacciones (pulse, fade, scale) | Flutter Animations API |
| Transiciones entre pantallas | Hero + PageRouteBuilder |
| Listas animadas | `AnimatedList` / staggered |

## Fuentes de Lottie

Usar solo animaciones gratuitas de [LottieFiles](https://lottiefiles.com/free-animations).
Almacenar archivos `.json` en `assets/animations/`.

## Pre-carga y Fallbacks

```dart
// Pre-cargar animación para evitar jank
late final Future<LottieComposition> _composition;

@override
void initState() {
  super.initState();
  _composition = AssetLottie('assets/animations/recording.json').load();
}

@override
Widget build(BuildContext context) {
  return FutureBuilder<LottieComposition>(
    future: _composition,
    builder: (context, snapshot) {
      if (snapshot.hasData) {
        return Lottie(composition: snapshot.data!);
      }
      // Fallback estático mientras carga
      return const Icon(Icons.mic, size: 48);
    },
  );
}
```

## Animaciones por Código

Para micro-interacciones, usar `AnimatedContainer`, `ScaleTransition`, etc.:

```dart
// Pulse de grabación (1000ms loop)
AnimatedContainer(
  duration: const Duration(milliseconds: 1000),
  curve: Curves.easeInOut,
  width: isRecording ? 80 : 64,
  height: isRecording ? 80 : 64,
  decoration: BoxDecoration(
    color: Colors.red,
    shape: BoxShape.circle,
  ),
)
```

## Staggered List Animation

Para listas, usar delay escalonado (300ms por item, 50ms delay):

```dart
AnimationConfiguration.staggeredList(
  position: index,
  duration: const Duration(milliseconds: 300),
  child: SlideAnimation(
    verticalOffset: 50.0,
    child: FadeInAnimation(child: child),
  ),
)
```
