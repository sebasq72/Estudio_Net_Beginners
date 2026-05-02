# Phase 2: OOP + SOLID + Clean Code - Pattern Map

**Mapped:** 2026-05-02
**Files analyzed:** 3 new content files to be created
**Analogs found:** 3 exact / 3 total (Phase 1 content files are direct analogs)

---

## Context: Documentation Project, Not Software

This project produces educational Markdown files. The "files to be created" are study session documents. Phase 1 content files now exist and serve as exact-match analogs for Phase 2. All three Phase 2 files follow the same role and data flow as Phase 1 files.

---

## File Classification

| New File | Role | Data Flow | Closest Analog | Match Quality |
|----------|------|-----------|----------------|---------------|
| `content/phase-02/02-01-oop-pilares.md` | study-session | sequential-read | `content/phase-01/01-01-csharp-moderno.md` | exact |
| `content/phase-02/02-02-solid-principios.md` | study-session | sequential-read | `content/phase-01/01-01-csharp-moderno.md` | exact |
| `content/phase-02/02-03-clean-code-abstracciones.md` | study-session | sequential-read | `content/phase-01/01-01-csharp-moderno.md` | exact |

**Role definitions:**
- `study-session`: Self-contained Markdown document covering one topic, structured as theory + code model + challenges + solutions. Consumed linearly in ~1.5 hour sessions.
- `sequential-read`: Data flows top to bottom. The consumer is a human reader.

---

## Pattern Assignments

### `content/phase-02/02-01-oop-pilares.md` (study-session, sequential-read)

**Analog:** `content/phase-01/01-01-csharp-moderno.md`

**Document header pattern** (lines 1-6):
```markdown
# C# Moderno: Features C# 8-14 y Puente .NET Framework

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** Experiencia previa con .NET Framework (cualquier version)
**Target:** .NET 10 LTS (todos los snippets modernos compilan en .NET 10)

---
```

Apply to 02-01 as:
```markdown
# OOP: Los 4 Pilares de la Programacion Orientada a Objetos

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** Conocimiento de C# moderno (Phase 1: records, pattern matching, nullable refs)
**Target:** .NET 10 LTS (todos los snippets compilan en .NET 10)

---
```

**Theory section pattern** (lines 133-160 — Nullable Reference Types section):
```markdown
## Nullable Reference Types — C# 8

Desde C# 8, con `#nullable enable` (habilitado por defecto en proyectos .NET 6+), el compilador trackea nullability. Los tipos referencia son **non-null por defecto** — debes marcar con `?` los que pueden ser null. Esto convierte `NullReferenceException` de un error de runtime a un warning de compilacion.

### Los operadores de nullability

```csharp
// Código demostrativo compilable en .NET 10
```
```

Structure: H2 heading with concept name, 2-4 sentence introduction in Spanish, then immediate code block demonstrating the concept.

**Challenge pattern** (lines 60-129 — Challenge 1 full structure):
```markdown
### Challenge 1: Identifica los patrones obsoletos

El siguiente codigo fue escrito para .NET Framework. Contiene **4 patrones que ya no se usan** en .NET moderno. Identifica cada uno y describe como se reemplaza.

```csharp
// Código con el problema — visible al estudiante
```

<details>
<summary>Ver solucion</summary>

```csharp
// Solución completa compilable en .NET 10
```

**Puntos clave:**
- [Punto 1: explicación de por qué funciona así]
- [Punto 2: trampa o edge case evitado]
- [Punto 3: nota práctica adicional]

</details>
```

**Resumen section pattern** (lines 594-601):
```markdown
---

## Resumen: Puntos Clave para la Entrevista

1. **[Pregunta de entrevista?]** Respuesta concisa en 1-2 lineas.
2. **[Pregunta de entrevista?]** Respuesta concisa en 1-2 lineas.
3. **[Pregunta de entrevista?]** Respuesta concisa en 1-2 lineas.
4. **[Pregunta de entrevista?]** Respuesta concisa en 1-2 lineas.
5. **[Pregunta de entrevista?]** Respuesta concisa en 1-2 lineas.
```

Format: Numbered list, each item starts with bold question, followed by concise answer. 5-7 items per plan.

---

### `content/phase-02/02-02-solid-principios.md` (study-session, sequential-read)

**Analog:** `content/phase-01/01-01-csharp-moderno.md`

Same header, theory, challenge, and resumen patterns apply. Additionally, this file requires a **unique three-part section structure per principle** (from D-08):

**SOLID principle section pattern** (new structure for Phase 2, no direct analog — synthesized from Phase 1 pattern + D-08/D-09):
```markdown
## SRP — Single Responsibility Principle

[2-4 sentence definition in Spanish + one-line rule statement]

### Aplicacion correcta

```csharp
// Business-domain example showing correct SRP application
// Must use realistic domain (payment, orders, inventory)
```

### Challenge N: [Titulo descriptivo]

[Enunciado — student applies the principle]

```csharp
// Código para que el estudiante trabaje
```

<details>
<summary>Ver solucion</summary>

```csharp
// Solución compilable .NET 10
```

**Puntos clave:**
- [2-4 bullets]

</details>

### Cuando va demasiado lejos

[1-2 sentence intro explaining the anti-pattern]

```csharp
// Concrete anti-pattern code showing harm of over-application
// For SRP: anemic domain model with logic scattered across services
```

> **Respuesta de entrevista:** "No aplicaria SRP cuando [argumento concreto]."
```

---

### `content/phase-02/02-03-clean-code-abstracciones.md` (study-session, sequential-read)

**Analog:** `content/phase-01/01-01-csharp-moderno.md`

Same base patterns apply. Additionally requires two unique structural patterns:

**Before/After refactoring pattern** (adapted from Phase 1 Framework vs Modern pattern, lines 24-53):
```markdown
### Nombres descriptivos

**Antes (code smell — ❌):**

```csharp
public class Proc
{
    public decimal Calc(List<Item> items, string t) { /* ... */ }
}
```

**Despues (clean code — ✓):**

```csharp
public class OrderCalculator
{
    public decimal CalculateTotal(List<OrderItem> items, ShippingRegion region) { /* ... */ }
}
```
```

**Decision tree table pattern** (from RESEARCH.md, D-12 — new for Phase 2):
```markdown
## Decision Tree: Clase Abstracta vs Interfaz

| Pregunta | Si | No |
|----------|----|----|
| Necesitas compartir estado (fields) entre clases derivadas? | **Abstract class** | Siguiente pregunta |
| Las clases necesitan heredar de otra clase base? | **Interface** (C# no tiene herencia multiple) | Siguiente pregunta |
| Necesitas que tipos no relacionados implementen el contrato? | **Interface** | Siguiente pregunta |
| Necesitas proveer implementacion comun que las derivadas pueden override? | **Abstract class** (o interface con default method si no hay estado) | **Interface** pura |

> **C# 8+ nota:** Default interface methods permiten implementaciones en interfaces, pero NO pueden acceder a estado de instancia (no hay fields). Si tu implementacion default necesita un field, usa abstract class.
```

---

## Shared Patterns

### Pattern 1: Challenge con Solucion Colapsable
**Source:** `content/phase-01/01-01-csharp-moderno.md` (lines 60-129)
**Apply to:** ALL challenges in ALL three Phase 2 plans (3-5 per plan)

```markdown
### Challenge N: [Titulo descriptivo]

[Enunciado — 2-5 sentences con requisitos claros]

```csharp
// Código inicial o esqueleto para el estudiante
```

<details>
<summary>Ver solucion</summary>

```csharp
// Solución completa y compilable en .NET 10
```

**Puntos clave:**
- [Punto 1: decisión de diseño o por qué funciona]
- [Punto 2: edge case o trampa evitada]
- [Punto 3 opcional: alternativa y por qué no se eligió]

</details>
```

Constraints:
- Every solution block MUST include 2-4 "Puntos clave" bullets after the code
- Difficulty progresses from basic to interview-level within each plan (D-02)
- 50/50 mix of abstract/technical and business-domain scenarios (D-05)

---

### Pattern 2: Seccion de Resumen Final
**Source:** `content/phase-01/01-01-csharp-moderno.md` (lines 592-601)
**Apply to:** ALL three plans, as the last section

```markdown
---

## Resumen: Puntos Clave para la Entrevista

1. **[Pregunta tipica de entrevista?]** Respuesta concisa.
2. **[Pregunta tipica de entrevista?]** Respuesta concisa.
3. **[Pregunta tipica de entrevista?]** Respuesta concisa.
4. **[Pregunta tipica de entrevista?]** Respuesta concisa.
5. **[Pregunta tipica de entrevista?]** Respuesta concisa.
```

---

### Pattern 3: Idioma — Espanol para Explicaciones, Ingles para Codigo
**Source:** Phase 1 PATTERNS.md (Pattern 5) + all Phase 1 content files
**Apply to:** ALL three plans, every section

Rules:
- Headings: espanol (ej. `## Encapsulacion`, `## Cuando va demasiado lejos`)
- Prose explanations: espanol
- Technical terms inline: ingles sin traduccion (ej. "el *method hiding* con `new`")
- All code blocks: ingles (class names, method names, variable names, comments)
- "Puntos clave" bullets: espanol con terminos tecnicos en ingles

---

### Pattern 4: Section Separator
**Source:** `content/phase-01/01-01-csharp-moderno.md` (line 132)
**Apply to:** Between major sections (H2 headings)

```markdown
---

## [Next Section Title]
```

A horizontal rule `---` separates each major topic section.

---

### Pattern 5: Blockquote for Important Notes
**Source:** `content/phase-01/01-01-csharp-moderno.md` (lines 55-58, 162)
**Apply to:** Important caveats, interview tips, awareness notes

```markdown
> **Nota importante:** [Aclaracion o caveat sobre el tema actual]
```

Used for: C# 8 default interface methods limitations, sealed keyword performance note, "Respuesta de entrevista" one-liners.

---

## Content Structure per Plan

### Plan 02-01: OOP Pillars (5 sections + resumen)

| Section | H2 Heading | Challenges | Key Pattern |
|---------|------------|------------|-------------|
| 1 | Encapsulacion | 1 | Theory + code model + challenge |
| 2 | Herencia | 1-2 | virtual/override/new interview trap (RESEARCH.md lines 152-170) |
| 3 | Polimorfismo | 1 | Runtime dispatch + casting |
| 4 | Abstraccion | 1 | Abstract class intro |
| 5 | Resumen: Puntos Clave para la Entrevista | -- | Pattern 2 |

### Plan 02-02: SOLID Principles (5 principles + resumen)

| Section | H2 Heading | Sub-sections | Key Pattern |
|---------|------------|--------------|-------------|
| 1 | SRP — Single Responsibility Principle | Aplicacion + Challenge + Cuando va demasiado lejos | Three-part SOLID pattern |
| 2 | OCP — Open/Closed Principle | Same | Three-part SOLID pattern |
| 3 | LSP — Liskov Substitution Principle | Same | Three-part SOLID pattern |
| 4 | ISP — Interface Segregation Principle | Same | Three-part SOLID pattern |
| 5 | DIP — Dependency Inversion Principle | Same | Three-part SOLID pattern |
| 6 | Resumen: Puntos Clave para la Entrevista | -- | Pattern 2 |

### Plan 02-03: Clean Code + Abstract vs Interface (6 sections + resumen)

| Section | H2 Heading | Challenges | Key Pattern |
|---------|------------|------------|-------------|
| 1 | Clean Code: Nombres descriptivos | 1 | Before/After refactoring pattern |
| 2 | Clean Code: Metodos pequenos + guard clauses | 1 | Before/After refactoring pattern |
| 3 | Clean Code: DRY | -- (integrated) | Theory only |
| 4 | Decision Tree: Clase Abstracta vs Interfaz | 1 | Decision tree table pattern |
| 5 | C# 8 Default Interface Implementations | -- | Blockquote awareness note |
| 6 | Challenge integrador | 1 | Full refactoring challenge |
| 7 | Resumen: Puntos Clave para la Entrevista | -- | Pattern 2 |

---

## No Analog Found

No files without analogs. All Phase 2 files directly follow Phase 1 content file patterns.

The only **new structural element** without a Phase 1 analog is the "three-part SOLID principle section" (Aplicacion + Challenge + Cuando va demasiado lejos). This is synthesized from:
- Phase 1 challenge pattern (for the Challenge sub-section)
- Phase 1 Before/After pattern (for the anti-pattern sub-section)
- D-08/D-09/D-10 decisions (for the structural requirement)

The decision tree table (Plan 02-03) is also new but uses standard Markdown table syntax already present in Phase 1 files.

---

## Code Examples Available in RESEARCH.md

The RESEARCH.md contains verified, compilable code snippets that implementers should use as starting points:

| Topic | Location in RESEARCH.md | Lines |
|-------|--------------------------|-------|
| Encapsulation with modern C# (BankAccount) | Code Examples section | 226-249 |
| virtual/override/new interview trap | Architecture Patterns section | 152-170 |
| LSP violation (NotImplementedException) | Code Examples section | 255-273 |
| Clean Code before/after refactoring | Code Examples section | 278-323 |
| Default Interface Methods limitations | Code Examples section | 328-354 |
| Decision tree table format | Architecture Patterns section | 137-148 |

---

## Metadata

**Analog search scope:** `content/phase-01/` (3 files), `.planning/` directory
**Files scanned:** 3 content files + Phase 1 PATTERNS.md
**Pattern extraction date:** 2026-05-02
**Note:** Phase 2 has exact analogs available from Phase 1. The primary constraint is maintaining format consistency while adding the new three-part SOLID section structure unique to Plan 02-02.
