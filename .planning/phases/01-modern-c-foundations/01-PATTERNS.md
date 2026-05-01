# Phase 1: Modern C# Foundations - Pattern Map

**Mapped:** 2026-05-01
**Files analyzed:** 3 new content files to be created
**Analogs found:** 0 exact / 3 total (no prior content files exist — Phase 1 establishes the template)

---

## Context: Documentation Project, Not Software

This project produces educational Markdown files, not executable software. The "files to be created" are study session documents. There are no controllers, services, models, or components. The analog search was performed across all existing `.md` files in the repository. No prior study content files exist — the planning and research files in `.planning/` are the closest structural analogs available.

**Analog search scope:** All `.md` files in the repository
**Files scanned:** 14 Markdown files
**Closest structural analogs:** `.planning/research/PITFALLS.md` (structured sections + code blocks), `.planning/research/FEATURES.md` (tables + tiered content), `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (challenge pattern with `<details>` blocks)

---

## File Classification

| New File | Role | Data Flow | Closest Analog | Match Quality |
|----------|------|-----------|----------------|---------------|
| `content/phase-01/01-01-csharp-moderno.md` | study-session | sequential-read | `.planning/research/PITFALLS.md` | structure-match |
| `content/phase-01/01-02-async-await.md` | study-session | sequential-read | `.planning/research/PITFALLS.md` | structure-match |
| `content/phase-01/01-03-linq-generics-delegates.md` | study-session | sequential-read | `.planning/research/PITFALLS.md` | structure-match |

**Role definitions for this project:**
- `study-session`: A self-contained Markdown document covering one topic, structured as theory → code model → challenges → solutions. Consumed linearly by a student in a single ~1.5 hour session.
- `sequential-read`: Data flows from top to bottom in the document. No branching, no API calls, no state. The "consumer" is a human reader.

---

## Pattern Assignments

### `content/phase-01/01-01-csharp-moderno.md` (study-session)

**Analog:** `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (lines 162–212) — this file already contains the definitive challenge pattern and table pattern for this project.

**Document opening pattern** (from `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` lines 125–161):

The RESEARCH.md defines the canonical session flow. Every study file MUST follow this order:

```
[Título del plan]
[Tiempo estimado y prerequisitos]

## Sección 1: [Nombre del tema]
[Introducción conceptual — español, 300-500 palabras]
[Modelo mental con código — snippet C# comentado, compilable en .NET 10]

### Challenge N: [Título]
[Enunciado — visible]
[Bloque de código vacío para el estudiante]
<details><summary>Ver solución</summary>
[Solución con código + Puntos clave]
</details>

## Resumen de Puntos Clave
[5-7 bullets]
```

**Translation table pattern** (from `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` lines 219–232):

Apply to plan 01-01 only, in the "Puente .NET Framework → .NET moderno" section:

```markdown
## Puente .NET Framework → .NET Moderno

| Concepto | .NET Framework | .NET Moderno |
|----------|---------------|--------------|
| Entry point | `Global.asax` | `Program.cs` (minimal hosting) |
| DI container | Unity, Autofac | Built-in `IServiceCollection` |
| Configuración | `web.config` / `ConfigurationManager` | `appsettings.json` + `IConfiguration` |
| Pipeline HTTP | HTTP Modules / Handlers | Middleware (`app.Use...`) |
| Serialización JSON | Newtonsoft.Json | `System.Text.Json` |
| Cliente HTTP | `WebClient` / `HttpWebRequest` | `IHttpClientFactory` + `HttpClient` |
```

Followed immediately by a before/after snippet block:

```markdown
**Antes (.NET Framework — ❌ no usar en código moderno):**
```csharp
// Global.asax.cs
protected void Application_Start() { ... }

// web.config
var connStr = ConfigurationManager.ConnectionStrings["MyDb"].ConnectionString;
```

**Ahora (.NET moderno — ✓ compilable en .NET 10):**
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IMyService, MyService>();
var connStr = builder.Configuration.GetConnectionString("MyDb");
var app = builder.Build();
app.Run();
```
```

**Section coverage order for 01-01** (guided by D-05 priority hierarchy):
1. Puente .NET Framework → .NET Moderno (MEDIUM — sets context, ~20 min)
2. Nullable Reference Types — C# 8 (HIGH — ~15 min)
3. Records y Value Equality — C# 9-10 (HIGHEST — ~20 min)
4. Pattern Matching — C# 8-12 (HIGHEST — ~20 min)
5. Primary Constructors + Collection Expressions — C# 12 (MEDIUM — ~10 min)
6. C# 13-14 Awareness Table (AWARENESS ONLY — ~5 min, no challenge)

---

### `content/phase-01/01-02-async-await.md` (study-session)

**Analog:** `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (lines 293–330) — the guía de contenido section defines the exact structure for this plan.

**State machine explanation pattern** (from RESEARCH.md lines 492–517):

The core theory section must show the "before compiler" vs "after compiler" mental model as paired code blocks:

```markdown
## El Compilador y la State Machine

Lo que el desarrollador escribe:

```csharp
public async Task<string> FetchDataAsync(string url)
{
    var client = new HttpClient();
    string data = await client.GetStringAsync(url);  // punto de suspensión
    return data.ToUpper();
}
```

Lo que el compilador genera (pseudocódigo simplificado):

```csharp
// 1. Crea una clase interna IAsyncStateMachine
// 2. Estado 0: ejecuta hasta el primer await, guarda client en un campo
// 3. Registra callback para cuando GetStringAsync() complete
// 4. RETORNA al caller — el hilo NO está bloqueado
// 5. Cuando el Task completa: retoma en Estado 1, ejecuta .ToUpper()
```

> Herramienta: pega el código en https://sharplab.io/ para ver el IL real generado.
```

**"Encuentra el bug" challenge pattern** (from RESEARCH.md lines 322–329, decision D-10):

Plan 01-02 must include at least one challenge where the code has intentional async bugs. The challenge format is:

```markdown
### Challenge 4: Encuentra los 3 problemas async

El siguiente código tiene tres problemas async clásicos. Identifícalos y corrige cada uno.

```csharp
// CÓDIGO CON BUGS — encontrar y corregir
public class NotificationService
{
    public async void SendAll(IEnumerable<string> recipients)  // bug 1
    {
        foreach (var r in recipients)
        {
            var result = GetTemplateAsync().Result;            // bug 2
            await Task.Run(() => Send(r, result));             // bug 3
        }
    }
}
```

<details>
<summary>Ver solución y explicación</summary>

```csharp
public async Task SendAll(IEnumerable<string> recipients)  // ✓ Task, no void
{
    foreach (var r in recipients)
    {
        var result = await GetTemplateAsync();              // ✓ await, no .Result
        await Send(r, result);                             // ✓ directo si Send es async
    }
}
```

**Puntos clave:**
- `async void`: las excepciones se pierden silenciosamente y no se puede hacer await. Solo válido para event handlers.
- `.Result`: bloquea el thread. En contextos con SynchronizationContext (legacy ASP.NET) causa deadlock.
- `Task.Run` innecesario: si `Send` ya es async, wrapearlo en `Task.Run` solo consume un thread pool thread adicional sin beneficio.

</details>
```

**Section coverage order for 01-02** (guided by D-06, CSHA-03, CSHA-04):
1. Modelo Mental — State Machine (HIGHEST — ~25 min)
2. Task vs ValueTask (~10 min, no challenge standalone — puede ser parte del challenge 2)
3. ConfigureAwait(false) (~10 min)
4. CancellationToken (~15 min)
5. Trampas Comunes — "encuentra el bug" (HIGHEST from CSHA-04 — ~20 min, 2 challenges)

---

### `content/phase-01/01-03-linq-generics-delegates.md` (study-session)

**Analog:** `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (lines 333–365) — guía de contenido para plan 01-03.

**IQueryable vs IEnumerable contrast pattern** (from RESEARCH.md lines 562–579):

The LINQ section must show a concrete before/after to make the performance difference visible, not just theoretical:

```markdown
### IEnumerable vs IQueryable — La Diferencia que Importa

```csharp
// ❌ Materializa TODO antes de filtrar (IEnumerable):
var wrong = _context.Products
    .ToList()                           // SELECT * FROM Products — trae todo
    .Where(p => p.Price > 100);        // filtra en C#, en memoria

// ✓ Filtra en la base de datos (IQueryable):
var right = _context.Products          // no ejecuta nada
    .Where(p => p.Price > 100)         // agrega WHERE al expression tree
    .ToList();                          // AQUÍ ejecuta: SELECT * WHERE Price > 100
```

> La query SQL equivalente del caso ✓: `SELECT * FROM Products WHERE Price > 100`
> La query SQL equivalente del caso ❌: `SELECT * FROM Products` (luego filtra en memoria)
```

**Generic repository challenge pattern** (from RESEARCH.md lines 583–606):

```markdown
### Challenge 3: Implementar GenericRepository<T>

Implementa una clase `GenericRepository<T>` con los constraints necesarios para garantizar que `T` es una entidad con identidad.

```csharp
public interface IEntity { int Id { get; } }

// Tu implementación aquí
public class GenericRepository<T> where T : ???
{
    public T? GetById(int id) => throw new NotImplementedException();
    public void Add(T entity) => throw new NotImplementedException();
}
```

<details>
<summary>Ver solución</summary>

```csharp
public class GenericRepository<T> where T : class, IEntity
{
    private readonly List<T> _store = new();
    public T? GetById(int id) => _store.FirstOrDefault(e => e.Id == id);
    public void Add(T entity) => _store.Add(entity);
}
```

**Puntos clave:**
- `where T : class` garantiza que `T` es un tipo referencia (necesario para `FirstOrDefault` retornar `null`).
- `where T : IEntity` garantiza que `T` tiene la propiedad `Id` — sin este constraint, el compilador no permite acceder a `e.Id`.
- Los constraints se aplican en orden: `class` siempre antes de las interfaces.

</details>
```

**Section coverage order for 01-03** (guided by CSHA-05, CSHA-06):
1. LINQ — Deferred Execution e IQueryable vs IEnumerable (HIGHEST — ~20 min, 2 challenges)
2. Generics con Constraints (HIGH — ~20 min, 1 challenge)
3. Delegates, Func, Action, Predicate (HIGH — ~20 min, 1 challenge)
4. Eventos — event keyword y EventHandler pattern (MEDIUM — ~10 min, 1 challenge)

---

## Shared Patterns

### Pattern 1: Challenge con Solución Colapsable
**Source:** `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (lines 162–212)
**Apply to:** ALL challenges in ALL three plans (D-02)

```markdown
### Challenge N: [Título descriptivo]

[Enunciado del problema — 3-5 bullets con requisitos claros]

```csharp
// Tu implementación aquí
```

<details>
<summary>Ver solución</summary>

```csharp
[Solución completa y compilable en .NET 10]
```

**Puntos clave:**
- [Punto 1: decisión de diseño o por qué funciona]
- [Punto 2: edge case o trampa evitada]
- [Punto 3 opcional: alternativa y por qué no se eligió]

</details>
```

Constraint: Every solution block MUST include 2-4 "Puntos clave" bullets in prose after the code (from Pitfall 6 in RESEARCH.md lines 396–401).

---

### Pattern 2: Código con Etiqueta Framework vs Moderno
**Source:** `.planning/research/PITFALLS.md` (lines 10–27)
**Apply to:** Plan 01-01 only, in the "Puente" section and anywhere a Framework anti-pattern appears

```markdown
**Antes (.NET Framework — ❌):**
```csharp
// código de Framework aquí
```

**Ahora (.NET moderno — ✓):**
```csharp
// código moderno compilable en .NET 10
```
```

NEVER show Framework code (with `System.Web`, `ConfigurationManager`, `HttpContext.Current`) without the `❌` label. NEVER show modern code without verifying it compiles in .NET 10.

---

### Pattern 3: Tabla de Awareness para Features de Baja Profundidad
**Source:** `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (lines 29–36, D-05 hierarchy)
**Apply to:** Plan 01-01, sección final de C# 13-14 (AWARENESS ONLY — no challenges)

```markdown
## C# 13-14: Nivel Awareness

| Feature | Versión | Qué es | Cuándo usar |
|---------|---------|--------|-------------|
| params collections | C# 13 | `params IEnumerable<T>` acepta cualquier colección | Al diseñar APIs flexibles |
| new lock type | C# 13 | `System.Threading.Lock` reemplaza `object` para `lock` | Código multithreaded nuevo |
| field-backed properties | C# 14 | `field` keyword accede al campo auto-generado en propiedades | Properties con lógica custom sin declarar campo explícito |
| extension blocks | C# 14 | Bloque `extension` agrupa métodos de extensión | Organización de métodos de extensión relacionados |
| null-conditional assignment | C# 14 | `obj?.Property ??= value` | Asignación condicional concisa |

> Referencia oficial: https://learn.microsoft.com/dotnet/csharp/whats-new/
```

---

### Pattern 4: Sección de Resumen al Final de Cada Plan
**Source:** `.planning/phases/01-modern-c-foundations/01-RESEARCH.md` (lines 143–151, session flow)
**Apply to:** ALL three plans, as the final section

```markdown
## Resumen: Puntos Clave para la Entrevista

- [Punto 1: definición concisa del concepto principal]
- [Punto 2: cuándo usarlo vs cuándo no]
- [Punto 3: la trampa más común y cómo evitarla]
- [Punto 4: pregunta típica de entrevista y respuesta en una línea]
- [Punto 5-7 opcionales: edge cases o comparaciones frecuentes]
```

---

### Pattern 5: Idioma — Español para Explicaciones, Inglés para Código
**Source:** `.planning/phases/01-modern-c-foundations/01-CONTEXT.md` (line 97, `<specifics>` section)
**Apply to:** ALL three plans, every section

Rules:
- Headings: español (ej. `## Records y Value Equality`)
- Prose explanations: español
- Technical terms inline in prose: inglés sin traducción (ej. "el compilador genera una *state machine*")
- All code blocks: inglés (class names, method names, variable names, comments)
- "Puntos clave" bullets: español con términos técnicos en inglés

---

## No Analog Found

There are no existing content/study files in this repository. Phase 1 establishes the format. All three plans are greenfield documents.

| File | Role | Reason |
|------|------|--------|
| `content/phase-01/01-01-csharp-moderno.md` | study-session | First content file ever created in this project |
| `content/phase-01/01-02-async-await.md` | study-session | First content file ever created in this project |
| `content/phase-01/01-03-linq-generics-delegates.md` | study-session | First content file ever created in this project |

The planner MUST use the patterns extracted above from RESEARCH.md (which contains fully worked examples and architecture patterns verified by the researcher) rather than looking for external analogs.

---

## Code Examples Available in RESEARCH.md

The RESEARCH.md already contains verified, compilable code snippets for every major topic. The implementer MUST use these as starting points (adapting them into challenge context), not write new snippets from scratch.

| Topic | Location in RESEARCH.md | Lines |
|-------|--------------------------|-------|
| Nullable Reference Types | `## Code Examples` section | 410–429 |
| Records y Value Equality | `## Code Examples` section | 432–455 |
| Pattern Matching / Switch Expression | `## Code Examples` section | 458–488 |
| Async/Await State Machine mental model | `## Code Examples` section | 492–517 |
| Task vs ValueTask | `## Code Examples` section | 521–540 |
| ConfigureAwait(false) | `## Code Examples` section | 543–558 |
| LINQ Deferred Execution + IQueryable vs IEnumerable | `## Code Examples` section | 562–579 |
| Generics con Constraints | `## Code Examples` section | 582–606 |
| Delegates, Func, Action, Predicate | `## Code Examples` section | 609–634 |

---

## Metadata

**Analog search scope:** All `.md` files in `/Users/sebasq72/claude_code_prj/Estudio_Net_Beginners/`
**Files scanned:** 14
**Pattern extraction date:** 2026-05-01
**Note:** Because this is a documentation project with no prior content files, the "patterns" extracted here are structural conventions derived from the research artifacts. Phases 2+ will have content/phase-01/ files available as high-quality analogs.
