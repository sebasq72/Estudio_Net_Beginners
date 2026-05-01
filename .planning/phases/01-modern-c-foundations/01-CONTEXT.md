# Phase 1: Modern C# Foundations — Context

**Gathered:** 2026-05-01
**Status:** Ready for planning

<domain>
## Phase Boundary

Phase 1 entrega el material de estudio para 3 planes que cubren C# moderno:
- **01-01:** Features C# 8-14 y el puente .NET Framework → .NET moderno
- **01-02:** Async/await profundo y trampas comunes
- **01-03:** LINQ, generics, delegates y programación funcional en C#

El output de esta fase es documentación de estudio (archivos Markdown con teoría, snippets de código, challenges y soluciones). No es un proyecto de software ejecutable.

**Fuera de scope:** OOP, SOLID, Design Patterns (eso es Phase 2+). Algoritmos en profundidad (eso es Phase 6).

</domain>

<decisions>
## Implementation Decisions

### Formato del contenido

- **D-01:** El material de cada plan se entrega como **archivos Markdown** con snippets de código C# inline. El estudiante copia y ejecuta en su IDE.
- **D-02:** Cada archivo tiene **enunciado del challenge + solución juntos** en el mismo documento. La solución puede estar en un bloque colapsable o claramente separada, para que el estudiante intente primero y luego verifique.
- **D-03:** Cada plan tiene **3-5 challenges**. Cantidad manejable en una sesión de ~1.5 horas manteniendo el balance teoría/práctica.
- **D-04:** No se usan proyectos .sln separados, xUnit, ni .NET Interactive Notebooks para Phase 1. El formato es standalone Markdown.

### Profundidad por tema

- **D-05:** La cobertura está **jerarquizada por frecuencia en entrevistas**. Más tiempo y profundidad en los temas de mayor frecuencia:
  - **HIGHEST** (más tiempo): async/await, LINQ, pattern matching, records/value equality
  - **HIGH** (cobertura completa): nullable refs, generics, delegates/Func/Action/Predicate
  - **MEDIUM** (overview + un challenge): primary constructors, collection expressions (C# 12)
  - **AWARENESS ONLY** (qué es y cuándo usarlo, sin challenge profundo): C# 13 features (params collections, new lock type), C# 14 features (field-backed properties, extension blocks, null-conditional assignment)
- **D-06:** Para **async/await** (tema #1 más preguntado): profundidad hasta **state machine + práctica**. Incluye el modelo mental de cómo el compilador transforma async/await, identificación de trampas comunes (.Result deadlocks, async void, ConfigureAwait, Task.Run mal uso). No llega a SynchronizationContext profundo ni IAsyncEnumerable/Channels (eso es avanzado).

### Target .NET version

- **D-07:** Los ejercicios y snippets usan **.NET 10 LTS** (LTS actual, Apr 2026) como target principal. El CLAUDE.md del proyecto y STACK.md alinean con esto. Se actualiza STATE.md para reflejar .NET 10 (había una inconsistencia con .NET 8 anotada en STATE.md).
- **D-08:** Se menciona .NET 8 donde es relevante para entrevistas (ej: muchas empresas aún en .NET 8), pero el código de ejercicios compila en .NET 10.

### Fuente y estilo de challenges

- **D-09:** Los challenges son **originales**, diseñados específicamente para reforzar el concepto del plan. Sin dependencia de cuentas externas ni plataformas.
- **D-10:** El estilo es **mezcla 50/50**: aproximadamente la mitad de los challenges son técnicos/abstractos (ej: "implement a generic cache with TTL") y la otra mitad tienen contexto de negocio/dominio (ej: "el servicio de notificaciones necesita..."). Esto mantiene variedad y familiariza al estudiante con ambos estilos de entrevista.
- **D-11:** La dificultad es **progresiva dentro de cada plan**: el primer challenge confirma que se entendió la teoría (sencillo), los siguientes van subiendo, y el último está al nivel de una pregunta típica de entrevista.

### Claude's Discretion

- Estructura interna de cada archivo Markdown (headings, secciones) — Claude decide el formato que sea más claro y escaneable.
- Qué sub-temas específicos de C# 8-12 toman el mayor espacio dentro de cada plan — guiado por la jerarquía de D-05 pero con flexibilidad en el orden.
- Longitud exacta de las explicaciones teóricas — suficiente para entender, sin ser un libro. El tono es conciso y orientado a la práctica.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Roadmap y Requisitos del Proyecto
- `.planning/ROADMAP.md` §Phase 1 — Goal, success criteria y planes 01-01, 01-02, 01-03
- `.planning/REQUIREMENTS.md` §C# Moderno — Requirements CSHA-01 a CSHA-06 que esta fase debe cumplir

### Investigación Previa
- `.planning/research/FEATURES.md` — Tabla de features con frecuencia de entrevistas, tiempo de estudio estimado y preguntas comunes. **Fuente principal para priorización de temas (D-05).**
- `.planning/research/STACK.md` — Stack recomendado, versiones .NET y C#, confirmación de .NET 10 como target (D-07).
- `.planning/research/PITFALLS.md` — Errores comunes que debe conocer el planner al diseñar los challenges.

### Stack del Proyecto
- `CLAUDE.md` §Technology Stack — Versiones oficiales del proyecto (.NET 10, C# 14, VS 2022).

No hay ADRs, specs externas ni docs de arquitectura para una fase de contenido educativo. Los requisitos están completamente capturados en las decisiones y los archivos de investigación.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- Ninguno — este es un proyecto de documentación, no de software. No hay componentes, hooks ni utilities reutilizables.

### Established Patterns
- Ningún patrón de contenido establecido aún — Phase 1 establece la plantilla. Las fases siguientes seguirán el formato decidido aquí (Markdown + snippets + solución + challenges progresivos).

### Integration Points
- El material de Phase 1 es prerequisito de Phase 2. El estudiante debe llegar a Phase 2 con C# moderno sólido para poder trabajar OOP/SOLID sin fricción de sintaxis.
- La investigación en `.planning/research/` ya hizo el trabajo de identificar frecuencias y tiempo estimado — el planner debe leer FEATURES.md antes de asignar tiempo a cada sub-tema.

</code_context>

<specifics>
## Specific Ideas

- El tono del material es en **español** para las explicaciones, con **términos técnicos en inglés** (como indica PROJECT.md). Los nombres de clases, métodos, y bloques de código siempre en inglés.
- Para la sección del puente .NET Framework → .NET moderno (plan 01-01), se puede incluir un mini-ejemplo de "cómo se hacía antes" vs "cómo se hace ahora" en cada diferencia clave (hosting model, DI, config, serialization).
- Los challenges de async/await deberían incluir al menos un escenario de "encuentra el bug" donde hay una trampa clásica (.Result deadlock o async void), no solo "implementa esto".

</specifics>

<deferred>
## Deferred Ideas

- **Links a LeetCode/Exercism:** El usuario prefirió challenges originales para Phase 1. Se puede agregar links opcionales a plataformas en una sección "Práctica Extra" en fases posteriores si quiere extender la práctica.
- **Proyecto C# ejecutable:** El formato elegido es Markdown. Si en una fase futura el usuario quiere ejercicios compilables (ej. Phase 6 Algoritmos), se puede reconsiderar el formato para esa fase específica.
- **.NET 8 compatibility notes:** Se decidió .NET 10 como target. Si en entrevistas el usuario nota que las empresas preguntan específicamente sobre .NET 8, se puede agregar notas de compatibilidad en una revisión de contenido.

</deferred>

---

*Phase: 01-modern-c-foundations*
*Context gathered: 2026-05-01*
