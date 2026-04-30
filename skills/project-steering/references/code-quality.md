---
inclusion: always
description: "Estándares de calidad de código que deben aplicarse en toda escritura de código del proyecto."
---

# Estándares de Calidad de Código

Estas reglas se aplican a TODO el código escrito en el proyecto. Antes de escribir o modificar código, verifica que cumple con estos estándares.

## Reglas Universales (todos los lenguajes)

1. **Error handling**: No silenciar errores — siempre manejar o propagar. Nunca `catch` vacíos.
2. **Sin secretos hardcodeados**: No incluir API keys, passwords, tokens ni valores de configuración directamente en el código. Usar variables de entorno o gestores de secretos.
3. **Queries parametrizadas**: Nunca concatenar strings para construir queries SQL/NoSQL. Usar siempre queries parametrizadas.
4. **Validación de input**: Validar toda entrada en los límites del sistema (API endpoints, formularios, webhooks).
5. **Naming conventions**: Seguir las convenciones del lenguaje y del proyecto. Consultar los steering files de arquitectura para convenciones específicas.
6. **Limpieza de recursos**: Cerrar conexiones, file handles, timers y subscriptions correctamente.
7. **Sin código muerto**: No dejar código comentado, funciones sin usar, ni imports innecesarios.

## TypeScript / JavaScript

- No usar `any` — usar tipos específicos, generics o `unknown` con type guards.
- Usar validación de schemas (Zod, Joi, etc.) en los límites del sistema si el proyecto lo usa.
- Naming: `camelCase` para funciones y variables, `PascalCase` para tipos/clases/interfaces, `kebab-case` para archivos.
- Preferir `const` sobre `let`. Nunca usar `var`.
- Usar `async/await` sobre `.then()` chains.

## Dart / Flutter

- Usar el logger del proyecto (consultar steering files) en vez de `print()` o `debugPrint()`.
- Naming: `camelCase` para variables/funciones, `PascalCase` para clases/enums/typedefs, `snake_case` para archivos.
- Preferir `final` sobre `var` cuando el valor no cambia.
- Usar pattern matching y sealed classes donde aplique.

## Cuándo aplicar

Estas reglas aplican al escribir código nuevo o modificar código existente. Si al modificar un archivo encuentras violaciones existentes en el código que tocas, corrígelas como parte del cambio.
