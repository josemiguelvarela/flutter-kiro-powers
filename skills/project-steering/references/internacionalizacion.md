---
description: Internacionalización con easy_localization, 23 idiomas soportados y formato JSON por feature
inclusion: always
---

# Internacionalización (i18n)

## Configuración

Usar `easy_localization` con archivos JSON por idioma en `lib/l10n/`.

## Idiomas Soportados (mínimo 23)

en, es, it, ko, pt, pl, ca, ja, de, ru, nl, fr, zh, sv, cs, no, tr, hu, uk, el, ar, hi, da

## Estructura de Archivos

```
lib/l10n/
├── en.json    # English (base)
├── es.json    # Español
├── fr.json    # Français
├── de.json    # Deutsch
├── ...        # 23 idiomas
```

## Formato JSON

Usar claves con namespace por feature:

```json
{
  "home": {
    "title": "App Name",
    "recent_sessions": "Recent Sessions"
  },
  "recording": {
    "start": "Start Recording",
    "pause": "Pause",
    "stop": "Stop",
    "resume": "Resume"
  },
  "transcription": {
    "processing": "Processing transcription...",
    "complete": "Transcription complete"
  }
}
```

## Uso en Código

```dart
// ✅ Correcto: usar tr() para traducciones
Text('home.title'.tr())

// ✅ Correcto: con parámetros
Text('recording.duration'.tr(args: [minutes.toString()]))

// ❌ Incorrecto: strings hardcodeados
Text('Start Recording')
```

## Configuración en main.dart

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await EasyLocalization.ensureInitialized();

  runApp(
    EasyLocalization(
      supportedLocales: const [Locale('en'), Locale('es'), ...],
      path: 'lib/l10n',
      fallbackLocale: const Locale('en'),
      child: const AiTranscribeApp(),
    ),
  );
}
```
