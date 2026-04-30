---
description: Guía de flujo de trabajo coordinado entre los 5 agentes expertos — spec-analyst, impl-coder, quality-guardian, security-sentinel y change-summarizer. SIEMPRE activa el power flutter-expert al inicio. Aplicar este flujo en tareas de implementación, refactoring o desarrollo de features. NO aplicar para preguntas simples, consultas sobre código, búsquedas web o conversaciones informales.
inclusion: auto
---

# Flujo de Trabajo con Agentes

Este documento describe cómo usar los 5 agentes expertos del equipo de forma coordinada.

## Equipo de Agentes

| Agente | Rol | Cuándo Usarlo |
| ------ | --- | ------------- |
| `spec-analyst` | Analista de Requisitos | Revisar specs, validar requisitos, identificar gaps |
| `impl-coder` | Ingeniero Senior | Implementar features, escribir código de producción |
| `quality-guardian` | Guardián de Calidad | Documentar, revisar código, crear tests |
| `change-summarizer` | Resumidor de Cambios | Generar resúmenes al completar tareas |
| `security-sentinel` | Centinela de Seguridad | Auditoría de seguridad exhaustiva |

## Activación Obligatoria del Power flutter-expert

**SIEMPRE** activar el power `flutter-expert` al inicio de cualquier tarea de implementación, refactoring o desarrollo de features. Este power proporciona documentación actualizada, herramientas especializadas y guías de Flutter/Dart que son esenciales para mantener la calidad del código.

```text
kiroPowers → action: "activate", powerName: "flutter-expert"
```

Este paso es **obligatorio** y debe ejecutarse antes de cualquier otro agente o acción en el flujo.

## Flujo Recomendado por Tarea

### 0. Activar Power flutter-expert (OBLIGATORIO)

Antes de cualquier paso, activar el power `flutter-expert` para tener acceso a documentación actualizada de Flutter, patrones BLoC/Cubit, GoRouter, easy_localization y mejores prácticas.

### 1. Antes de Implementar

```text
spec-analyst → Revisar requisitos del task
```

Invoca `@spec-analyst` para validar que los requisitos están completos, sin ambigüedades, y son implementables y deterministas.

### 2. Implementación

```text
impl-coder → Escribir el código
```

Invoca `@impl-coder` con referencia al requisito específico. El agente implementará siguiendo Clean Architecture y los estándares del proyecto.

### 3. Calidad

```text
quality-guardian → Documentar + Revisar + Testear
```

Invoca `@quality-guardian` para:

- Mantener actualizados README, steering files y guías del proyecto
- Code review exhaustivo
- Crear suite de tests (unit + integration)

### 4. Seguridad (OBLIGATORIO)

```text
security-sentinel → Auditoría de seguridad
```

**ANTES de considerar una tarea completa**, invoca `@security-sentinel` para una revisión exhaustiva. Este paso NO es opcional.

### 5. Resumen

```text
change-summarizer → Generar resumen de cambios
```

Al finalizar, invoca `@change-summarizer` para documentar qué se hizo, el impacto, y los próximos pasos.

## Ejemplo de Uso Completo

```text
0. kiroPowers → activate "flutter-expert"  (SIEMPRE primero)
1. @spec-analyst "Revisa el Requisito 4: Vectorización y Filtrado Semántico"
2. @impl-coder "Implementa el Vector_Engine según el Requisito 4"
3. @quality-guardian "Documenta, revisa y testea el módulo Vector_Engine"
4. @security-sentinel "Audita la seguridad del módulo Vector_Engine"
5. @change-summarizer "Resume los cambios realizados en la implementación del Vector_Engine"
```
