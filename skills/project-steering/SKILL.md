---
name: project-steering
description: Generic project steering rules. Use when Codex writes, reviews, refactors, tests, or documents project code and needs the local architecture, accessibility, naming, BLoC state, localization, logging, Lottie, privacy, security, or on-device AI performance conventions converted from .kiro/steering.
---

# Project Steering

Use this skill as the Codex mirror of `.kiro/steering`. Keep the Kiro source
files and the Codex references aligned when project guidance changes.

## Workflow

1. Read the reference files that match the task before editing code.
2. Prefer these project rules over generic Flutter or Dart advice.
3. If a reference conflicts with existing source code, inspect nearby code and
   follow the established project behavior unless the user asks for a change.
4. Keep `.kiro/steering` intact. Update this skill mirror separately when
   Codex-specific guidance changes.

## Always Relevant

- `references/arquitectura-proyecto.md`: Clean Architecture by feature,
  dependency rules, layer structure, and dependency injection.
- `references/code-quality.md`: project-wide code quality standards.
- `references/convenciones-nombrado.md`: names for files, classes, variables,
  and imports.
- `references/privacidad-seguridad.md`: zero-network policy, no telemetry,
  least privilege, and security review expectations.
- `references/logging-rules.md`: logging constraints and allowed patterns.

## Load When Relevant

- `references/accesibilidad.md`: widgets, semantics, contrast, TalkBack,
  VoiceOver, and reduced motion.
- `references/gestion-estado-bloc.md`: BLoC/Cubit state, Equatable states, and
  state testing.
- `references/internacionalizacion.md`: easy_localization and JSON resources.
- `references/lottie-animaciones.md`: Lottie and Flutter animation patterns.
- `references/rendimiento-ai.md`: on-device AI performance, isolates, memory,
  CPU, and thermal constraints.
- `references/agent-workflow.md`: Kiro-origin workflow for expert agents. For
  Codex, prefer `project-agent-workflow`.

## Validation

After code changes, run the smallest useful checks for the touched area:

- `flutter analyze` for Dart/Flutter code.
- `flutter test` or targeted tests when behavior changes.
- Security/privacy review when touching storage, model download, file IO,
  permissions, networking, logging, or native integrations.
