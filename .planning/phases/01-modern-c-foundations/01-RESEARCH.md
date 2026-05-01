# Phase 1: Modern C# Foundations - Research

**Researched:** 2026-05-01
**Domain:** C# 8-14 language features, async/await internals, LINQ, generics/delegates — entregado como material de estudio Markdown
**Confidence:** HIGH

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions

- **D-01:** Material de cada plan entregado como **archivos Markdown** con snippets de código C# inline. El estudiante copia y ejecuta en su IDE.
- **D-02:** Enunciado del challenge + solución en el mismo documento (solución en bloque colapsable o claramente separada).
- **D-03:** Cada plan tiene **3-5 challenges**. Sesión de ~1.5 horas, balance teoría/práctica.
- **D-04:** Sin proyectos .sln, xUnit ni .NET Interactive Notebooks para Phase 1. Formato standalone Markdown.
- **D-05:** Cobertura jerarquizada por frecuencia en entrevistas:
  - **HIGHEST** (más tiempo): async/await, LINQ, pattern matching, records/value equality
  - **HIGH** (cobertura completa): nullable refs, generics, delegates/Func/Action/Predicate
  - **MEDIUM** (overview + un challenge): primary constructors, collection expressions (C# 12)
  - **AWARENESS ONLY** (sin challenge profundo): C# 13 features (params collections, new lock type), C# 14 features (field-backed properties, extension blocks, null-conditional assignment)
- **D-06:** Async/await hasta **state machine + práctica**. No llega a SynchronizationContext profundo ni IAsyncEnumerable/Channels.
- **D-07:** Ejercicios y snippets usan **.NET 10 LTS** como target principal.
- **D-08:** Se menciona .NET 8 donde es relevante para entrevistas.
- **D-09:** Challenges **originales**, sin dependencia de plataformas externas.
- **D-10:** Estilo **50/50**: mitad técnicos/abstractos, mitad con contexto de negocio/dominio.
- **D-11:** Dificultad **progresiva dentro de cada plan**: primer challenge sencillo, último a nivel entrevista.

### Claude's Discretion

- Estructura interna de cada archivo Markdown (headings, secciones)
- Sub-temas específicos de C# 8-12 que toman más espacio dentro de cada plan (guiado por D-05)
- Longitud exacta de explicaciones teóricas: concisa, orientada a práctica

### Deferred Ideas (OUT OF SCOPE)

- Links a LeetCode/Exercism en Phase 1
- Proyecto C# ejecutable (.sln)
- Notas de compatibilidad .NET 8 (salvo donde relevante para entrevistas)
</user_constraints>

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| CSHA-01 | El estudiante comprende las diferencias clave entre .NET Framework y .NET moderno (hosting model, DI, config, serialization) | Plan 01-01: tabla de traducción Framework→moderno, antes/después con código |
| CSHA-02 | El estudiante escribe código C# usando features de C# 8-12 (nullable refs, records, pattern matching, primary constructors, collection expressions) | Plan 01-01: cobertura completa de C# 8-12 con ejemplos y challenges |
| CSHA-03 | El estudiante comprende y aplica async/await profundo: state machine, Task vs ValueTask, ConfigureAwait, cancelación | Plan 01-02: modelo mental state machine + challenges de implementación correcta |
| CSHA-04 | El estudiante identifica y evita trampas comunes de async: async void, .Result deadlocks, Task.Run mal uso | Plan 01-02: challenges tipo "encuentra el bug" con trampas clásicas |
| CSHA-05 | El estudiante usa LINQ idiomáticamente: deferred execution, IQueryable vs IEnumerable, GroupBy/SelectMany/Aggregate | Plan 01-03: LINQ de deferred execution a operadores avanzados |
| CSHA-06 | El estudiante domina generics, delegates, events, lambdas y Func/Action/Predicate | Plan 01-03: generics con constraints + sistema de delegates/events completo |
</phase_requirements>

---

## Summary

Phase 1 produce tres documentos Markdown de estudio (planes 01-01, 01-02, 01-03). El output NO es código ejecutable — es material educativo diseñado para que el estudiante copie snippets en su IDE y complete challenges con solución incluida. El estudiante tiene experiencia previa en .NET Framework, por lo que el mayor valor de esta fase no es enseñar desde cero sino cerrar el gap entre paradigmas.

Los tres temas de la fase (C# moderno, async/await, LINQ+generics+delegates) son los más preguntados en entrevistas .NET generalistas según el research previo del proyecto. Async/await es el tema #1 más preguntado y merece la mayor profundidad (hasta state machine). LINQ es omnipresente — "cada entrevista". Generics y delegates son la base sobre la que se construyen los patrones de fases siguientes.

La estructura de cada plan debe seguir el patrón: introducción conceptual → modelo mental con código → challenges progresivos (fácil→difícil) → soluciones. El idioma de las explicaciones es español; los términos técnicos, nombres de clases, variables y bloques de código van en inglés.

**Primary recommendation:** Diseñar cada plan como una sesión autocontenida de 1.5 horas: 20-25 min de teoría + modelo mental, 60-70 min de challenges progresivos. El material debe poder completarse sin conexión a internet ni configuración especial.

---

## Architectural Responsibility Map

> Esta fase produce contenido educativo, no software. El "mapa de responsabilidad" describe qué cubre cada plan.

| Capability | Plan Owner | Secondary | Rationale |
|------------|-----------|-----------|-----------|
| Puente .NET Framework → .NET moderno | 01-01 | — | Contexto fundamental para un developer que viene de Framework |
| C# 8-12 features (nullable refs, records, pattern matching) | 01-01 | — | Language features con mayor frecuencia en entrevistas (HIGHEST/HIGH) |
| C# 12-14 features (primary constructors, collection expressions, awareness C# 13-14) | 01-01 | — | Cierre del espectro moderno, menor profundidad |
| Async/await state machine + implementación correcta | 01-02 | — | Tema #1 más preguntado; necesita plan propio por profundidad requerida |
| Trampas async comunes (async void, .Result, Task.Run) | 01-02 | — | CSHA-04 requiere challenges tipo "encuentra el bug" |
| Task vs ValueTask, ConfigureAwait, CancellationToken | 01-02 | — | Nivel entrevista — preguntas de profundidad |
| LINQ (deferred execution, operadores avanzados, IQueryable vs IEnumerable) | 01-03 | — | CSHA-05 completo; base para EF Core en Phase 5 |
| Generics con constraints, covarianza/contravarianza | 01-03 | — | CSHA-06 parcial; muy preguntado en entrevistas |
| Delegates, eventos, Func/Action/Predicate | 01-03 | — | CSHA-06 completo; base de LINQ y patrones eventualmente |

---

## Standard Stack

### Para el Estudiante (Entorno de Ejecución)

| Herramienta | Versión | Propósito | Nota |
|-------------|---------|-----------|------|
| .NET SDK | 10.0 (LTS) | Compilar y ejecutar snippets | Target de todos los ejercicios (D-07) |
| Visual Studio 2022 | v17.14+ | IDE principal | Copilot Agent Mode GA, LINQ debugger integrado |
| VS Code + C# Dev Kit | Latest | Alternativa cross-platform | Para estudiantes en macOS/Linux |
| .NET CLI | 10.0 | `dotnet script` o proyectos temporales | Para probar snippets sin solución .sln |

### Para el Contenido (Herramientas del Planner/Implementador)

| Herramienta | Propósito | Nota |
|-------------|-----------|------|
| Markdown (GitHub Flavored) | Formato de entrega | Soporta `<details>/<summary>` para soluciones colapsables |
| Bloques de código fenced con `csharp` | Syntax highlighting | ```` ```csharp ```` para todos los snippets |
| `<details><summary>Ver solución</summary>` | Ocultar soluciones | HTML inline soportado en GitHub y VS Code Preview |

**Instalación del entorno del estudiante:**
```bash
# Verificar instalación
dotnet --version   # debe mostrar 10.x

# Para probar snippets de forma rápida (sin proyecto)
dotnet new console -n Scratch --use-program-main false
cd Scratch && dotnet run
```

[VERIFIED: official docs — .NET 10 LTS current stable as of April 2026, dotnet.microsoft.com/download/dotnet/10.0]

---

## Architecture Patterns

### Estructura de Contenido: Flujo de una Sesión de Estudio

```
Inicio de sesión
     |
     v
[Introducción conceptual]   ← español, 300-500 palabras
     |
     v
[Modelo mental con código]  ← snippets C# comentados, compilables en .NET 10
     |
     v
[Challenge 1: Confirmación] ← sencillo, verifica comprensión básica
     |
     v
[Challenge 2: Aplicación]   ← contexto técnico o de negocio
     |
     v
[Challenge 3: Profundidad]  ← nivel entrevista, trampa o edge case
     |
     v
[Challenge 4-5: Opcional]   ← si el tema tiene suficiente variedad
     |
     v
[Resumen de puntos clave]   ← 5-7 bullets para revisión rápida
```

### Estructura Recomendada de Archivos

```
content/
└── phase-01/
    ├── 01-01-csharp-moderno.md          # C# 8-14 + puente Framework
    ├── 01-02-async-await.md             # Async/await profundo
    └── 01-03-linq-generics-delegates.md # LINQ + generics + delegates
```

### Patrón 1: Challenge con Solución Colapsable

**Qué es:** Estructura HTML inline de Markdown que muestra el enunciado visible y oculta la solución hasta que el estudiante hace clic.

**Cuándo usar:** Todos los challenges en todos los planes.

**Ejemplo:**
```markdown
### Challenge 2: Implementar un cache genérico con TTL

Implementa una clase genérica `TimedCache<TKey, TValue>` que:
- Almacena pares clave-valor con un tiempo de expiración
- Retorna `bool TryGet(TKey key, out TValue value)` (false si expiró)
- Usa `DateTimeOffset` para el TTL

```csharp
// Tu implementación aquí
```

<details>
<summary>Ver solución</summary>

```csharp
public class TimedCache<TKey, TValue> where TKey : notnull
{
    private readonly Dictionary<TKey, (TValue Value, DateTimeOffset Expiry)> _store = new();
    private readonly TimeSpan _ttl;

    public TimedCache(TimeSpan ttl) => _ttl = ttl;

    public void Set(TKey key, TValue value) =>
        _store[key] = (value, DateTimeOffset.UtcNow.Add(_ttl));

    public bool TryGet(TKey key, out TValue value)
    {
        if (_store.TryGetValue(key, out var entry) && entry.Expiry > DateTimeOffset.UtcNow)
        {
            value = entry.Value;
            return true;
        }
        value = default!;
        return false;
    }
}
```

**Puntos clave:** El constraint `where TKey : notnull` previene keys nulas. `default!` con null-forgiving operator porque el caller ya sabe que `value` es inválido cuando retorna `false`.

</details>
```

### Patrón 2: Tabla de Traducción Framework → Moderno (para plan 01-01)

**Qué es:** Tabla con dos columnas mostrando "cómo se hacía antes" vs "cómo se hace ahora", seguida de un snippet antes/después.

**Cuándo usar:** Plan 01-01, sección de puente .NET Framework → .NET moderno.

**Ejemplo de estructura:**
```markdown
## Puente .NET Framework → .NET Moderno

| Concepto | .NET Framework | .NET Moderno |
|----------|---------------|--------------|
| Entry point de la app | `Global.asax` + `Startup.cs` | `Program.cs` (minimal hosting, top-level statements) |
| Contenedor DI | Unity, Autofac (terceros) | Built-in `IServiceCollection` |
| Configuración | `web.config` / `ConfigurationManager` | `appsettings.json` + `IConfiguration` |
| Pipeline HTTP | HTTP Modules / Handlers | Middleware pipeline (`app.Use...`) |
| Serialización JSON | Newtonsoft.Json | `System.Text.Json` (diferencias en comportamiento) |
| Cliente HTTP | `WebClient` / `HttpWebRequest` | `IHttpClientFactory` + `HttpClient` |
```

### Anti-Patrones a Evitar en el Contenido

- **Snippets que no compilan en .NET 10:** Todo código debe compilar con `dotnet run` sin modificaciones. Verificar que no se usen APIs obsoletas.
- **Challenges sin solución o con solución incompleta:** La solución debe incluir explicación de los puntos clave, no solo código.
- **Teoría sin código:** Cada concepto debe acompañarse de al menos un snippet funcional antes del primer challenge.
- **Challenges todos del mismo tipo:** Mezclar técnico/abstracto con contexto de negocio (D-10).
- **No usar `async void` en ejemplos correctos:** Los snippets de "cómo hacerlo bien" nunca deben usar `async void` fuera de la sección de trampas.

---

## Don't Hand-Roll

> Aplicado al contenido educativo: cosas que el material NO debe intentar reimplementar, y qué usar en cambio.

| Problema | No construir | Usar en cambio | Por qué |
|----------|-------------|----------------|---------|
| Ocultar soluciones | CSS/JS custom | `<details><summary>` HTML nativo | Funciona en GitHub, VS Code Preview, sin dependencias |
| Syntax highlighting | Resaltado manual con backticks simples | Bloques fenced con `csharp` | Soporte universal en todos los renderers Markdown |
| Ejemplos de async/await state machine | Diagrama ASCII de estado | Explicación del IL generado + código antes/después del compilador | El código IL generado es verificable con SharpLab.io |
| Tabla de features C# por versión | Tabla propia | Referencia a `learn.microsoft.com/dotnet/csharp/whats-new/` | Siempre actualizada; el estudiante debe conocer la fuente oficial |

---

## Guía de Contenido por Plan

### Plan 01-01: C# Moderno y Puente Framework → .NET 10

**Estructura recomendada (1.5 horas):**

1. **Sección: Puente .NET Framework → .NET Moderno** (20 min)
   - Tabla de traducción completa (7-8 diferencias clave)
   - Snippet antes/después: `Global.asax + web.config` vs `Program.cs + appsettings.json`
   - Challenge 1 (CONFIRMACIÓN): "Identifica qué es incorrecto en este código que usa `HttpContext.Current` y `ConfigurationManager`"

2. **Sección: Nullable Reference Types (C# 8)** (15 min)
   - El problema que resuelve: NullReferenceException en tiempo de ejecución → error de compilación
   - `?` en tipos referencia, `!` null-forgiving, `??` y `?.`
   - Anotaciones: `[NotNull]`, `[MaybeNull]`
   - Challenge 2 (APLICACIÓN): Fix a method signature that has nullable issues

3. **Sección: Records y Value Equality (C# 9)** (20 min) — HIGHEST priority
   - `record` vs `class`: value equality por defecto, immutability, `with` expression
   - `record struct` (C# 10)
   - Cuándo usar record vs class (pregunta frecuente de entrevista)
   - Challenge 3 (PROFUNDIDAD): Implementar un value object de dominio como record

4. **Sección: Pattern Matching (C# 8-12)** (20 min) — HIGHEST priority
   - `switch expression`, type patterns, `when` clauses
   - Positional patterns, property patterns
   - List patterns (C# 11)
   - Challenge 4 (ENTREVISTA): Refactorizar cadena de if-else a switch expression

5. **Sección: Primary Constructors (C# 12) + Collection Expressions (C# 12)** (10 min) — MEDIUM
   - Overview conciso
   - Challenge 5 (SENCILLO): Convertir clase tradicional a primary constructor

**Tiempo estimado: ~1.5 horas incluyendo tiempo de práctica**

---

### Plan 01-02: Async/Await Profundo y Trampas Comunes

**Estructura recomendada (1.5 horas):**

1. **Sección: Modelo Mental — Qué Hace el Compilador** (25 min) — HIGHEST priority
   - El compilador transforma `async` en una state machine
   - Estados: antes del primer `await`, en cada `await`, completado
   - Ejemplo visual: código con `await` → pseudocódigo de lo que genera el compilador
   - No requiere conocer el IL exacto — el modelo mental es suficiente
   - Referencia: SharpLab.io para explorar el IL generado (awareness)
   - Challenge 1 (CONFIRMACIÓN): "¿En qué punto se suspende la ejecución? ¿Qué hilo continúa?"

2. **Sección: Task vs ValueTask** (10 min)
   - `Task<T>`: objeto en heap, overhead de allocación
   - `ValueTask<T>`: struct, evita allocación cuando el resultado es síncrono
   - Cuándo usar cada uno (pregunta de entrevista frecuente)

3. **Sección: ConfigureAwait(false)** (10 min)
   - Por qué importa en librerías: no capturar SynchronizationContext
   - ASP.NET Core NO tiene SynchronizationContext (diferencia clave de .NET Framework)
   - Regla: en librerías siempre `ConfigureAwait(false)`; en aplicaciones no es necesario
   - Challenge 2 (APLICACIÓN): Servicio de notificaciones con múltiples awaits correctamente configurados

4. **Sección: CancellationToken** (15 min)
   - `CancellationTokenSource`, `CancellationToken.ThrowIfCancellationRequested()`
   - Propagar el token a toda la cadena de llamadas
   - `OperationCanceledException` vs otras excepciones
   - Challenge 3 (APLICACIÓN): Implementar operación cancelable con timeout

5. **Sección: Trampas Comunes** (20 min) — CSHA-04, tipo "encuentra el bug"
   - **async void:** excepciones perdidas, no se puede await. Solo para event handlers.
   - **.Result / .Wait():** deadlock en contextos con SynchronizationContext (legacy ASP.NET)
   - **Task.Run innecesario:** wrapping de código ya async, presión innecesaria en thread pool
   - Challenge 4 (TRAMPA): "Encuentra y corrige los 3 problemas async en este código"
   - Challenge 5 (ENTREVISTA): "Implementa correctamente un servicio que descarga N archivos en paralelo con límite de concurrencia"

**Tiempo estimado: ~1.5 horas incluyendo tiempo de práctica**

---

### Plan 01-03: LINQ, Generics, Delegates y Programación Funcional en C#

**Estructura recomendada (1.5 horas):**

1. **Sección: LINQ — Deferred Execution** (20 min) — HIGHEST priority
   - `IEnumerable<T>` es lazy: la query NO se ejecuta hasta iterar
   - `ToList()`, `ToArray()`, `Count()`: materializan la query
   - IQueryable vs IEnumerable: IQueryable construye expression trees (traducción a SQL)
   - Operadores clave: `Select`, `Where`, `GroupBy`, `SelectMany`, `Aggregate`, `Any/All`
   - Challenge 1 (CONFIRMACIÓN): "¿Cuántas veces se ejecuta esta query? ¿Por qué?"
   - Challenge 2 (APLICACIÓN): Análisis de ventas con `GroupBy`, `SelectMany` y `Aggregate`

2. **Sección: Generics y Constraints** (20 min) — HIGH priority
   - Generic classes, methods, interfaces
   - Constraints: `where T : class`, `where T : struct`, `where T : new()`, `where T : IComparable<T>`
   - Covarianza (`out`) y contravarianza (`in`) — awareness + pregunta de entrevista
   - Challenge 3 (PROFUNDIDAD): Implementar `GenericRepository<T>` con constraint `where T : class, IEntity`

3. **Sección: Delegates, Func, Action, Predicate** (20 min) — HIGH priority
   - `delegate` como tipo: firma de método
   - `Func<T, TResult>`, `Action<T>`, `Predicate<T>`: atajos predefinidos
   - Multicast delegates
   - Lambdas como shorthand de delegates
   - Challenge 4 (APLICACIÓN): Pipeline de transformación usando `Func<T,T>` encadenados

4. **Sección: Eventos** (10 min)
   - `event` keyword: delegate con restricciones de acceso
   - El patrón `EventHandler<TEventArgs>`
   - Diferencia delegate vs event (pregunta de entrevista)
   - Challenge 5 (ENTREVISTA): Sistema de notificaciones usando event + EventArgs personalizado

**Tiempo estimado: ~1.5 horas incluyendo tiempo de práctica**

---

## Common Pitfalls

### Pitfall 1: Código de Ejemplo que No Compila en .NET 10
**Qué va mal:** Snippet escrito con APIs de .NET Framework (`System.Web`, `ConfigurationManager`, `HttpContext.Current`) dentro de secciones que muestran "cómo hacerlo bien".
**Por qué ocurre:** Mezcla de ejemplos "antes" y "después" sin delimitación clara.
**Cómo evitar:** Separar claramente los bloques "Framework (❌)" de los bloques "Moderno (✓)". Los snippets etiquetados como modernos deben compilar en .NET 10.
**Señal de advertencia:** El snippet tiene `using System.Web;` o `using System.Configuration;`.

### Pitfall 2: Explicar Async/Await Sin el Modelo Mental de la State Machine
**Qué va mal:** El estudiante entiende "agrega async/await a los métodos" pero no puede responder "¿qué pasa cuando llamas `.Result` en un controller?"
**Por qué ocurre:** Enseñar solo el happy path sin el mecanismo subyacente.
**Cómo evitar:** El modelo mental de la state machine (aunque sea simplificado) debe ir ANTES de los challenges. Incluir al menos un challenge de "qué pasa en este código".
**Señal de advertencia:** No hay ningún snippet que muestre el "antes" (qué genera el compilador) vs el "después" (código async legible).

### Pitfall 3: Challenges Todos del Mismo Patrón
**Qué va mal:** Los 5 challenges de un plan son variaciones del mismo ejercicio técnico. El estudiante los pasa mecánicamente sin generalizar el concepto.
**Por qué ocurre:** Es más fácil escribir variaciones que contextos distintos.
**Cómo evitar:** Al menos 2-3 challenges con contexto de negocio/dominio (servicios, e-commerce, notificaciones, etc.). Variar el tipo de error buscado (implementación vs bug-find vs refactoring).

### Pitfall 4: IQueryable vs IEnumerable Sin Consecuencias Visibles
**Qué va mal:** El estudiante lee la diferencia pero no la "siente". No entiende el impacto en performance.
**Por qué ocurre:** Explicación puramente teórica sin demostración de la query SQL generada.
**Cómo evitar:** Incluir un ejemplo concreto mostrando qué pasa cuando se aplica `.Where()` a un `IQueryable` (filtro en SQL) vs a un `IEnumerable` (filtro en memoria después de traer todo). Usar un comentario que explique la query SQL equivalente.

### Pitfall 5: Olvidar el Nivel de Awareness para C# 13-14
**Qué va mal:** Se profundiza en features de C# 13-14 con challenges complejos, usando tiempo que debería ir a async/await y LINQ.
**Por qué ocurre:** Querer cubrir todo el espectro de C# 14 exhaustivamente.
**Cómo evitar:** Para C# 13-14: máximo una sección de 10 min con descripción + snippet simple + sin challenge. La tabla de awareness al final del plan 01-01 es suficiente.

### Pitfall 6: Solución Sin Explicación de Puntos Clave
**Qué va mal:** La solución es solo código, sin comentarios sobre por qué se eligió ese approach.
**Por qué ocurre:** Ahorra tiempo al escribir el material.
**Cómo evitar:** Cada solución debe incluir 2-4 "Puntos clave" en texto después del bloque de código, explicando decisiones y edge cases.

---

## Code Examples

Snippets verificados para los temas principales de la fase:

### Nullable Reference Types (C# 8)

```csharp
// Source: learn.microsoft.com/dotnet/csharp/nullable-references
#nullable enable

// Sin nullable: el compilador no sabe si name puede ser null
public string GetGreeting(string name) => $"Hello, {name}";

// Con nullable: el compilador trackea nullability
public string GetGreeting(string? name)
{
    // CS8602 si no verificamos primero
    if (name is null) return "Hello, stranger";
    return $"Hello, {name}"; // aquí name está garantizado non-null
}

// Null-conditional y null-coalescing
string? maybeNull = GetPossiblyNullValue();
int length = maybeNull?.Length ?? 0;
```

[VERIFIED: learn.microsoft.com/dotnet/csharp/nullable-references]

### Records y Value Equality (C# 9-10)

```csharp
// Source: learn.microsoft.com/dotnet/csharp/language-reference/builtin-types/record
// Record: immutable by default, value equality, with-expression
public record Product(string Name, decimal Price, string Category);

var p1 = new Product("Widget", 9.99m, "Tools");
var p2 = new Product("Widget", 9.99m, "Tools");
Console.WriteLine(p1 == p2);   // True (value equality, no reference)
Console.WriteLine(p1.Equals(p2)); // True

// with-expression: crea copia con propiedad modificada
var discounted = p1 with { Price = 7.99m };

// record struct (C# 10): value type + value equality
public record struct Point(double X, double Y);

// Cuándo usar record vs class:
// record: DTOs, value objects inmutables, snapshots de estado
// class: entidades con identidad, objetos mutables con comportamiento
```

[VERIFIED: learn.microsoft.com/dotnet/csharp/language-reference/builtin-types/record]

### Pattern Matching — Switch Expression (C# 8+)

```csharp
// Source: learn.microsoft.com/dotnet/csharp/language-reference/operators/switch-expression
// Antes (if-else chain):
string ClassifyOld(int score)
{
    if (score >= 90) return "A";
    else if (score >= 80) return "B";
    else if (score >= 70) return "C";
    else return "F";
}

// Ahora (switch expression con when):
string Classify(int score) => score switch
{
    >= 90 => "A",
    >= 80 => "B",
    >= 70 => "C",
    _     => "F"
};

// Property patterns + type patterns:
string DescribeShape(object shape) => shape switch
{
    Circle { Radius: > 10 } c => $"Large circle, area = {Math.PI * c.Radius * c.Radius:F2}",
    Circle c                   => $"Small circle",
    Rectangle { Width: var w, Height: var h } => $"Rectangle {w}x{h}",
    null                       => "null",
    _                          => "Unknown shape"
};
```

[VERIFIED: learn.microsoft.com/dotnet/csharp/language-reference/operators/switch-expression]

### Async/Await — Modelo Mental de State Machine

```csharp
// Source: learn.microsoft.com/dotnet/csharp/asynchronous-programming/
// Lo que el dev escribe:
public async Task<string> FetchDataAsync(string url)
{
    var client = new HttpClient();
    string data = await client.GetStringAsync(url);  // punto de suspensión
    return data.ToUpper();
}

// Lo que el compilador genera (pseudocódigo simplificado):
// - Crea una clase "state machine" con un enum de estado
// - Estado 0: ejecuta hasta el primer await
// - Guarda `client` y el Task devuelto en campos de la state machine
// - Registra un callback para cuando el Task complete
// - RETORNA al caller (no bloquea el hilo)
// - Cuando el Task completa: reanuda en Estado 1, ejecuta .ToUpper(), completa el Task externo

// La trampa de .Result (deadlock en legacy ASP.NET con SynchronizationContext):
// var result = FetchDataAsync(url).Result; // ¡DEADLOCK! El hilo que espera es el mismo
                                             // que el SynchronizationContext necesita para reanudar
// En ASP.NET Core: no hay SynchronizationContext, .Result no causa deadlock
//   pero SIGUE siendo mala práctica — bloquea un thread pool thread
```

[CITED: learn.microsoft.com/dotnet/csharp/asynchronous-programming/task-asynchronous-programming-model]

### Task vs ValueTask

```csharp
// Source: learn.microsoft.com/dotnet/api/system.threading.tasks.valuetask-1
// Task<T>: siempre allocates in heap — usar para operaciones que casi siempre son async
public async Task<int> GetCountAsync() { ... }

// ValueTask<T>: struct — usar cuando la operación a menudo completa síncronamente
public ValueTask<int> GetCachedCountAsync()
{
    if (_cache.TryGetValue("count", out int cached))
        return new ValueTask<int>(cached);  // sin allocación: resultado ya disponible

    return new ValueTask<int>(FetchCountFromDbAsync()); // async cuando no está en cache
}

// REGLA: No await ValueTask más de una vez — no está diseñado para eso
```

[VERIFIED: learn.microsoft.com/dotnet/api/system.threading.tasks.valuetask-1]

### ConfigureAwait(false)

```csharp
// Source: learn.microsoft.com/dotnet/fundamentals/networking/http/httpclient
// En una librería (no sabe en qué contexto se usa):
public async Task<string> FetchAsync(string url)
{
    using var client = new HttpClient();
    return await client.GetStringAsync(url).ConfigureAwait(false);
    // ConfigureAwait(false): no captura el SynchronizationContext actual
    // Reanuda en cualquier thread pool thread disponible
}

// En ASP.NET Core: no hay SynchronizationContext, ConfigureAwait(false) no es necesario
// pero tampoco daña — es una buena práctica en código de librería reutilizable
```

[CITED: learn.microsoft.com/dotnet/fundamentals/networking/http/httpclient]

### LINQ — Deferred Execution e IQueryable vs IEnumerable

```csharp
// Source: learn.microsoft.com/dotnet/standard/linq/
// IEnumerable<T>: LINQ to Objects — ejecuta en memoria
List<Product> products = GetAllProducts(); // trae TODOS a memoria
var expensive = products.Where(p => p.Price > 100); // filtra en C#, en memoria

// IQueryable<T>: LINQ to Entities (EF Core) — construye expression tree → SQL
IQueryable<Product> dbProducts = _context.Products; // no ejecuta nada
var expensiveFromDb = dbProducts.Where(p => p.Price > 100); // no ejecuta nada
var result = expensiveFromDb.ToList(); // AQUÍ ejecuta: SELECT * WHERE Price > 100

// Trampa clásica: materializar antes de filtrar
var wrong = _context.Products.ToList().Where(p => p.Price > 100);
//                             ^ aquí trae TODOS los productos a memoria, luego filtra en C#
```

[VERIFIED: learn.microsoft.com/dotnet/standard/linq/]

### Generics con Constraints

```csharp
// Source: learn.microsoft.com/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters
public interface IEntity
{
    int Id { get; }
}

// Constraint: T debe ser una clase (ref type) y debe implementar IEntity
public class GenericRepository<T> where T : class, IEntity
{
    private readonly List<T> _store = new();

    public T? GetById(int id) => _store.FirstOrDefault(e => e.Id == id);
    public void Add(T entity) => _store.Add(entity);
}

// Constraint con new(): T debe tener constructor sin parámetros
public T CreateInstance<T>() where T : new() => new T();

// Constraint de tipo valor:
public void ProcessValue<T>(T value) where T : struct { ... }
```

[VERIFIED: learn.microsoft.com/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters]

### Delegates, Func, Action, Predicate

```csharp
// Source: learn.microsoft.com/dotnet/csharp/programming-guide/delegates/
// Delegate custom:
public delegate int Transform(int value);
Transform doubler = x => x * 2;

// Func<TIn, TOut>: delegate que retorna un valor
Func<int, int> square = x => x * x;
Func<string, string, string> concat = (a, b) => a + b;

// Action<T>: delegate que no retorna valor (void)
Action<string> log = msg => Console.WriteLine($"[LOG] {msg}");

// Predicate<T>: delegate que retorna bool — equivalente a Func<T, bool>
Predicate<int> isEven = n => n % 2 == 0;

// Multicast delegate: encadena múltiples métodos
Action<string> handlers = LogToConsole + LogToFile + LogToDb;
handlers("mensaje"); // llama los 3

// Pipeline usando Func encadenados:
Func<int, int> pipeline = Compose(x => x * 2, x => x + 1, x => x * x);
```

[VERIFIED: learn.microsoft.com/dotnet/csharp/programming-guide/delegates/]

---

## State of the Art

| Enfoque Antiguo | Enfoque Actual | Cambió En | Impacto para el Material |
|----------------|----------------|-----------|--------------------------|
| `Startup.cs` con `Configure`/`ConfigureServices` | `Program.cs` minimal hosting (top-level statements + `WebApplication.CreateBuilder`) | .NET 6 | El plan 01-01 debe mostrar el nuevo `Program.cs`, no el viejo `Startup.cs` |
| `System.Web.HttpContext` | `Microsoft.AspNetCore.Http.HttpContext` | .NET Core 1.0 | Señal de alerta en código legacy |
| `ConfigurationManager` / `web.config` | `IConfiguration` + `appsettings.json` | .NET Core 1.0 | Tabla de traducción obligatoria en 01-01 |
| `Newtonsoft.Json` como default | `System.Text.Json` (diferencias en `NullValueHandling`, case sensitivity, etc.) | .NET Core 3.0 | Mencionar en 01-01 como gotcha para devs de Framework |
| `HttpWebRequest` / `WebClient` | `IHttpClientFactory` + `HttpClient` | .NET Core 2.1 | Pitfall de socket exhaustion si se usa `new HttpClient()` directo |
| `async void` event handlers | `async Task` + `async void` SOLO para event handlers | C# 5 (pero sigue siendo confundido) | Challenge "encuentra el bug" en 01-02 |
| `Thread` / `ThreadPool.QueueUserWorkItem` | `Task` / `async-await` | .NET 4.5+ / .NET Core | Plan 01-02 usa Task como primitiva base |
| Bloques `try-catch` con múltiples tipos | Pattern matching en catch (C# 6) + switch expressions | C# 6 → C# 8 | Plan 01-01 sección pattern matching |
| Clases de record manual (mutable, con `Equals` override) | `record` keyword (C# 9) | C# 9 | Plan 01-01: records son el standard para value objects e inmutabilidad |
| `var` implícito sin target-typed new | Target-typed `new()` (C# 9) + collection expressions `[]` (C# 12) | C# 9, C# 12 | Mencionar en 01-01; collection expressions merecen snippet |

**Deprecated/Outdated que el material debe mencionar para evitar confusión:**
- `BinaryFormatter`: eliminado en .NET 9 — si el código de ejemplo lo usa, es una señal de que es Framework
- `System.Web` namespace: no existe en .NET moderno — cualquier using de este namespace es Framework
- `.edmx` designer: no existe en EF Core — solo Code-First (Phase 5)

---

## Assumptions Log

| # | Claim | Section | Risk si es Incorrecto |
|---|-------|---------|----------------------|
| A1 | `<details><summary>` funciona en el renderer Markdown que usa el estudiante | Standard Stack / Patrón de Challenge | El estudiante no puede colapsar/expandir soluciones. Mitigación: indicar "ver abajo" como fallback |
| A2 | El estudiante tiene acceso a VS 2022 o VS Code en su máquina | Standard Stack | No puede ejecutar snippets. Mitigación: los snippets deben funcionar también con `dotnet run` desde CLI |
| A3 | La mayoría de interviewers .NET en 2026 preguntan async/await como #1 | Guía de contenido 01-02 | Si el orden cambia, la jerarquía D-05 puede necesitar ajuste. [ASSUMED: basado en FEATURES.md ya investigado] |

---

## Open Questions

1. **Longitud máxima por archivo Markdown**
   - Qué sabemos: cada plan es ~1.5 horas de sesión
   - Qué no está definido: si hay un límite de líneas/caracteres por archivo para comodidad de lectura
   - Recomendación: mantener cada plan en ~400-600 líneas de Markdown. Si crece más, señal de que hay demasiado contenido para una sesión.

2. **Convención de nombre para archivos**
   - Qué sabemos: el directorio es `content/phase-01/` según estructura sugerida
   - Qué no está definido: si el estudiante quiere número de plan en el nombre (`01-01-...`) o nombre descriptivo (`csharp-moderno.md`)
   - Recomendación: usar `01-01-csharp-moderno.md` — número de plan garantiza orden correcto en file explorers.

---

## Environment Availability

Step 2.6: SKIPPED — Phase 1 produce documentación Markdown. No requiere herramientas externas, bases de datos, ni servicios en el entorno de ejecución del planner. El entorno del **estudiante** requiere .NET 10 SDK (ya documentado en Standard Stack).

---

## Validation Architecture

> `nyquist_validation: true` en config.json. Sin embargo, esta fase produce contenido Markdown educativo — no hay código ejecutable que testear. Las verificaciones son cualitativas.

### Framework de Validación para Contenido Educativo

| Propiedad | Valor |
|-----------|-------|
| Framework | Revisión manual (no hay test runner) |
| Archivo de config | N/A — no aplica para contenido |
| Verificación rápida | Compilar snippets de código: `dotnet run` en proyecto de scratch |
| Verificación completa | Lectura completa de cada plan + ejecución de todos los challenges |

### Phase Requirements → Mapa de Validación

| Req ID | Comportamiento a Verificar | Tipo | Cómo Verificar | ¿Existe? |
|--------|---------------------------|------|----------------|---------|
| CSHA-01 | Tabla .NET Framework → .NET moderno completa (7+ diferencias) | Revisión manual | Contar filas de la tabla de traducción | Wave 0 |
| CSHA-01 | Snippet "antes vs después" compilable | Compilación | `dotnet run` con el snippet moderno | Wave 0 |
| CSHA-02 | Snippets de nullable refs, records, pattern matching compilan en .NET 10 | Compilación | `dotnet run` en proyecto scratch | Wave 0 |
| CSHA-02 | Cada feature tiene al menos un challenge | Revisión manual | Verificar sección por sección del plan 01-01 | Wave 0 |
| CSHA-03 | Sección de state machine presente con modelo mental y código | Revisión manual | Verificar que el plan 01-02 tiene sección de state machine | Wave 0 |
| CSHA-03 | Challenge de Task vs ValueTask presente | Revisión manual | Contar challenges que cubren Task vs ValueTask | Wave 0 |
| CSHA-04 | Al menos un challenge tipo "encuentra el bug" con async void / .Result / Task.Run | Revisión manual | Verificar que el challenge de trampas tiene bugs intencionados | Wave 0 |
| CSHA-05 | Challenge de IQueryable vs IEnumerable presente con ejemplo concreto | Revisión manual | Verificar sección LINQ del plan 01-03 | Wave 0 |
| CSHA-05 | Challenges de GroupBy, SelectMany, Aggregate presentes | Revisión manual | Contar operadores cubiertos en challenges | Wave 0 |
| CSHA-06 | Challenges de Func/Action/Predicate en ejercicios prácticos | Revisión manual | Verificar sección delegates del plan 01-03 | Wave 0 |

### Sampling Rate

- **Por cada plan entregado:** Compilar todos los snippets de código en un proyecto scratch temporal
- **Al finalizar la fase:** Lectura completa de los 3 planes simulando ser el estudiante — ¿se puede completar en 1.5 horas? ¿las soluciones son correctas y explican los puntos clave?
- **Phase gate:** Los 3 planes deben existir, todos los snippets deben compilar en .NET 10, y todos los requirements CSHA-01 a CSHA-06 deben estar cubiertos

### Wave 0 Gaps

- [ ] Crear estructura de directorio `content/phase-01/` (si no existe)
- [ ] Definir plantilla de archivo Markdown para los planes (heading structure, secciones estándar)
- [ ] Verificar que el entorno tiene `dotnet 10.x` disponible para validar snippets

*(No hay test runner ni conftest que crear — el "framework" es compilación manual de snippets)*

---

## Security Domain

> `security_enforcement` no está explícitamente en config.json, por lo que se trata como habilitado. Sin embargo, Phase 1 es material educativo Markdown — no hay superficie de ataque. Se documenta brevemente por completitud.

Esta fase no introduce endpoints, autenticación, input externo ni persistencia de datos. Los únicos "activos" son archivos Markdown en el repositorio git.

| Consideración | Aplica | Acción |
|---------------|--------|--------|
| Snippets que enseñan patrones inseguros como "correctos" | Sí | Nunca mostrar `async void` como correcto fuera de la sección de trampas. No mostrar `.Result` como práctica válida. |
| Credenciales en snippets de código | No aplica | Los snippets no se conectan a servicios reales |
| XSS via HTML en Markdown | Muy bajo riesgo | El HTML inline usado (`<details>`) es benigno |

---

## Sources

### Primary (HIGH confidence)
- `learn.microsoft.com/dotnet/csharp/nullable-references` — Nullable reference types, operadores `?` y `!`
- `learn.microsoft.com/dotnet/csharp/language-reference/builtin-types/record` — Records, value equality, `with` expressions
- `learn.microsoft.com/dotnet/csharp/language-reference/operators/switch-expression` — Switch expressions y pattern matching
- `learn.microsoft.com/dotnet/csharp/asynchronous-programming/` — Async/await, TAP model
- `learn.microsoft.com/dotnet/standard/linq/` — LINQ, deferred execution
- `learn.microsoft.com/dotnet/csharp/programming-guide/generics/` — Generics y constraints
- `learn.microsoft.com/dotnet/csharp/programming-guide/delegates/` — Delegates, Func, Action
- `.planning/research/FEATURES.md` — Frecuencia de entrevistas, tiempo estimado por tema (HIGH confidence — investigación previa del proyecto)
- `.planning/research/STACK.md` — Versiones de .NET y C#, .NET 10 como target (HIGH confidence)
- `.planning/research/PITFALLS.md` — Errores comunes, trampas async, gap Framework→moderno (HIGH confidence)

### Secondary (MEDIUM confidence)
- `.planning/phases/01-modern-c-foundations/01-CONTEXT.md` — Decisiones del usuario (D-01 a D-11)
- `learn.microsoft.com/dotnet/api/system.threading.tasks.valuetask-1` — ValueTask vs Task

---

## Metadata

**Confidence breakdown:**
- Contenido de C# features: HIGH — verificado contra documentación oficial Microsoft
- Jerarquía de prioridades por entrevista: HIGH — basado en FEATURES.md ya investigado con fuentes oficiales
- Estructura de planes (tiempo por sección): MEDIUM — estimaciones basadas en experiencia pedagógica de diseño de materiales de ~1.5 horas; puede necesitar ajuste tras la primera sesión
- Pitfalls del contenido: HIGH — documentados en PITFALLS.md del proyecto

**Research date:** 2026-05-01
**Valid until:** 2026-08-01 (C# 14 / .NET 10 son estables; poco riesgo de cambios que afecten Phase 1)
