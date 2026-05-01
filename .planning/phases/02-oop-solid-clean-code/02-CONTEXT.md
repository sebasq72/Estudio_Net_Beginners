# Phase 2: OOP + SOLID + Clean Code — Context

**Gathered:** 2026-05-02
**Status:** Ready for planning

<domain>
## Phase Boundary

Phase 2 entrega el material de estudio para 3 planes que cubren OOP, SOLID y Clean Code:
- **02-01:** Los 4 pilares de OOP con coding challenges
- **02-02:** SOLID principios uno a uno con trade-offs (incluyendo cuándo NO aplicarlos)
- **02-03:** Clean code, abstract vs interface (decision tree + refactoring), y challenges integrados

El output es documentación de estudio (archivos Markdown con teoría, snippets C#, challenges y soluciones). No es un proyecto ejecutable.

**Fuera de scope:** Design patterns creacionales/estructurales/de comportamiento (Phase 3). Testing (Phase 4). Algoritmos (Phase 6).

</domain>

<decisions>
## Implementation Decisions

### Formato del contenido (heredado de Phase 1)

- **D-01:** Mismo formato establecido en Phase 1: archivos Markdown con snippets C# inline, solución colapsable `<details>`, 3-5 challenges por plan.
- **D-02:** Dificultad progresiva dentro de cada plan (D-11 de Phase 1): el primer challenge confirma comprensión básica, el último está al nivel de una entrevista.
- **D-03:** Target .NET 10 — todo el código compila en .NET 10 (D-07 de Phase 1).
- **D-04:** Idioma: español para explicaciones, inglés para código, nombres de clases y términos técnicos (D-05 pattern 5 de Phase 1).

### Escenarios de challenges

- **D-05:** Mix 50/50 de challenges técnicos/abstractos y con contexto de dominio de negocio (consistente con D-10 de Phase 1). Ejemplos de dominios realistas para OOP/SOLID: sistemas de pago, notificaciones, inventario, empleados, pedidos. No depender exclusivamente de jerarquías "Animal → Dog/Cat" — mezclarlo con escenarios de entrevista real.

### Profundidad de herencia C# (plan 02-01)

- **D-06:** Herencia cubre concepto + trampas de entrevista. Incluir:
  - `virtual` / `override` — cómo funciona el dispatch en tiempo de ejecución
  - `new` keyword — cómo oculta el método del padre (trampa clásica: comportamiento diferente según el tipo de la referencia)
  - `sealed` — cuándo y por qué bloquear la herencia
  - Diferencia entre `override` (reemplaza) y `new` (oculta) como pregunta típica de entrevista
- **D-07:** No profundizar en: explicit interface implementation multi-nivel, herencia múltiple de interfaces profunda, covarianza/contravarianza genérica (eso puede tocar Phase 3 si aplica a patterns).

### Trade-offs SOLID (plan 02-02)

- **D-08:** Cada principio SOLID incluye tres partes:
  1. Definición + ejemplo de aplicación correcta
  2. Challenge práctico
  3. "Cuándo va demasiado lejos" — ejemplo concreto donde sobre-aplicar el principio crea más problemas que beneficios
- **D-09:** Anti-patterns específicos a incluir por principio:
  - SRP → over-decomposition / anemia domain model (clases con un solo método trivial)
  - OCP → abstraction overkill (interfaz para todo aunque nunca cambie)
  - LSP → subtipos que lanzan `NotImplementedException` en lugar de cumplir el contrato
  - ISP → interfaces granulares que se usan siempre juntas (peor que una sola interfaz)
  - DIP → inyección de dependencias que complica más de lo que ayuda en proyectos pequeños
- **D-10:** El estudiante debe poder responder en entrevista: "¿Cuándo NO aplicarías [principio]?" con un argumento concreto.

### Abstract vs Interface — formato de enseñanza (plan 02-03)

- **D-11:** Estructura para la sección abstract vs interface:
  1. **Decision tree visual** — árbol de 3-4 preguntas que llevan al desarrollador a la decisión correcta (¿necesitas estado compartido? → abstract; ¿múltiple herencia de comportamiento? → interface; etc.)
  2. **Nota sobre C# 8 default implementations** — cuándo blur la línea y qué sigue siendo diferente
  3. **Refactoring challenge** — código que usa `abstract class` donde debería ser `interface` (o viceversa), el estudiante identifica el problema y lo corrige con justificación
- **D-12:** El decision tree es una referencia que el estudiante puede consultar después — formato tabla o diagrama ASCII en Markdown.

### Claude's Discretion

- Dominios específicos de los challenges (dentro del mix 50/50 decidido) — elegir los más representativos para cada concepto.
- Estructura interna de cada sección (headings, orden de sub-temas dentro de cada pilar OOP).
- Longitud de las explicaciones teóricas — suficiente para entender, sin ser un libro.
- El anti-pattern específico de "cuándo va demasiado lejos" para cada principio SOLID (D-09 da la dirección, Claude elige el ejemplo más ilustrativo).

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Roadmap y Requisitos del Proyecto
- `.planning/ROADMAP.md` §Phase 2 — Goal, success criteria y planes 02-01, 02-02, 02-03
- `.planning/REQUIREMENTS.md` §OOP + SOLID + Mejores Practicas — Requirements OOP-01 a OOP-04

### Contexto de Phase 1 (patrones establecidos)
- `.planning/phases/01-modern-c-foundations/01-CONTEXT.md` — Decisiones de formato que Phase 2 hereda (D-01 a D-11 de Phase 1). Leer antes de planificar cualquier plan de Phase 2.
- `.planning/phases/01-modern-c-foundations/01-PATTERNS.md` — Patrones concretos de Markdown establecidos: challenge con solución colapsable, idioma, sección de resumen. Phase 2 sigue los mismos patrones (Pattern 1, 4, 5 son directamente aplicables).

### Stack del Proyecto
- `CLAUDE.md` §Technology Stack — Versiones oficiales (.NET 10, C# 14, VS 2022). Confirma target de compilación.

No hay ADRs, specs externas ni docs de arquitectura adicionales para esta fase de contenido educativo. Los requisitos están completamente capturados en las decisiones y los archivos de referencia de Phase 1.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `content/phase-01/` — Los 3 archivos de Phase 1 son el único analog de contenido disponible. El planner y ejecutor de Phase 2 DEBEN leerlos para mantener consistencia de formato, tono y estructura de challenges.

### Established Patterns
- **Challenge con solución colapsable** (Pattern 1 de PATTERNS.md) — Aplicar a todos los challenges de Phase 2.
- **Sección de resumen final** (Pattern 4 de PATTERNS.md) — "Resumen: Puntos Clave para la Entrevista" al final de cada plan.
- **Reglas de idioma** (Pattern 5 de PATTERNS.md) — Headings en español, código en inglés, términos técnicos en inglés dentro del texto en español.

### Integration Points
- Phase 2 construye directamente sobre el C# de Phase 1. Los challenges pueden asumir que el estudiante conoce records, pattern matching, nullable refs, LINQ y generics.
- Phase 3 (Design Patterns) construye sobre Phase 2 — los patterns de comportamiento como Strategy y Observer son extensiones directas del polimorfismo y la separación de interfaces que se enseñan aquí.

</code_context>

<specifics>
## Specific Ideas

- Para el challenge de `virtual`/`override`/`new`: un escenario donde una referencia del tipo base llama al método "overridden" con `new` y el resultado no es el esperado. Clásico bug de entrevista.
- El decision tree de abstract vs interface puede presentarse como tabla Markdown de decisión (filas = preguntas, columnas = Sí/No → recomendación) — más escaneable que un diagrama que no renderiza bien en todos los visualizadores.
- Para el anti-pattern de SRP → anemia domain model: mostrar un `Order` con toda la lógica en servicios y la entidad con solo propiedades, luego discutir cuándo eso es un problema vs cuándo es intencional (CQRS, etc.).

</specifics>

<deferred>
## Deferred Ideas

- **Explicit interface implementation profunda** — mencionado como candidato pero fuera de scope de Phase 2. Puede aparecer en Phase 3 o 4 si aplica a un pattern específico.
- **Covarianza/contravarianza genérica** — toca Phase 1 (generics) y Phase 2 (herencia) pero es suficientemente avanzado para dejarlo fuera. Puede agregarse como sección "Práctica Extra" si el estudiante lo pide.
- **Links a LeetCode/Exercism para OOP** — mismo criterio que Phase 1: challenges originales primero. Si el usuario quiere práctica extra después, se puede agregar sección al final.

</deferred>

---

*Phase: 02-oop-solid-clean-code*
*Context gathered: 2026-05-02*
