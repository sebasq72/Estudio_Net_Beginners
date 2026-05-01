# Phase 2: OOP + SOLID + Clean Code - Research

**Researched:** 2026-05-02
**Domain:** Educational content creation — OOP, SOLID principles, Clean Code in C# (.NET 10)
**Confidence:** HIGH

## Summary

Phase 2 produces three Markdown study documents covering OOP pillars, SOLID principles with trade-offs, and clean code practices including abstract vs interface decision-making. The output is educational content (not executable software), following the same format patterns established in Phase 1.

The research confirms that the CONTEXT.md decisions are well-aligned with current C# interview expectations. The key technical areas requiring accuracy are: (1) the `virtual`/`override`/`new` keyword behavior trap, (2) default interface implementations from C# 8+, (3) SOLID anti-patterns that are commonly over-applied, and (4) the abstract class vs interface decision tree in the context of modern C# where the line has blurred.

**Primary recommendation:** Follow Phase 1 patterns exactly. Focus content on interview-ready explanations with the "when NOT to apply" angle that differentiates this material from generic tutorials.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** Mismo formato establecido en Phase 1: archivos Markdown con snippets C# inline, solucion colapsable `<details>`, 3-5 challenges por plan.
- **D-02:** Dificultad progresiva dentro de cada plan: el primer challenge confirma comprension basica, el ultimo esta al nivel de una entrevista.
- **D-03:** Target .NET 10 — todo el codigo compila en .NET 10.
- **D-04:** Idioma: espanol para explicaciones, ingles para codigo, nombres de clases y terminos tecnicos.
- **D-05:** Mix 50/50 de challenges tecnicos/abstractos y con contexto de dominio de negocio. Dominios realistas: sistemas de pago, notificaciones, inventario, empleados, pedidos.
- **D-06:** Herencia cubre: virtual/override, new keyword (oculta metodo), sealed, diferencia override vs new como pregunta de entrevista.
- **D-07:** No profundizar en: explicit interface implementation multi-nivel, herencia multiple de interfaces profunda, covarianza/contravarianza generica.
- **D-08:** Cada principio SOLID: definicion + ejemplo, challenge practico, "cuando va demasiado lejos" con ejemplo concreto.
- **D-09:** Anti-patterns especificos: SRP→over-decomposition/anemia; OCP→abstraction overkill; LSP→NotImplementedException; ISP→interfaces granulares siempre juntas; DIP→DI que complica en proyectos pequenos.
- **D-10:** El estudiante debe responder "Cuando NO aplicarias [principio]?" con argumento concreto.
- **D-11:** Abstract vs interface: decision tree visual (3-4 preguntas), nota C# 8 default implementations, refactoring challenge.
- **D-12:** Decision tree como tabla o diagrama ASCII en Markdown.

### Claude's Discretion
- Dominios especificos de los challenges (dentro del mix 50/50)
- Estructura interna de cada seccion (headings, orden de sub-temas)
- Longitud de las explicaciones teoricas
- El anti-pattern especifico de "cuando va demasiado lejos" para cada principio SOLID

### Deferred Ideas (OUT OF SCOPE)
- Explicit interface implementation profunda
- Covarianza/contravarianza generica
- Links a LeetCode/Exercism para OOP
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| OOP-01 | El estudiante explica y aplica los 4 pilares de OOP con ejemplos en C# (encapsulacion, herencia, polimorfismo, abstraccion) | Plan 02-01: Each pillar gets a dedicated section with theory + challenge. virtual/override/new trap provides interview-level depth for polimorfismo. |
| OOP-02 | El estudiante aplica cada principio SOLID con codigo C# y puede explicar trade-offs | Plan 02-02: 5 sections (one per principle) each with definition, correct application, challenge, and "when it goes too far" anti-pattern. |
| OOP-03 | El estudiante distingue cuando usar clase abstracta vs interfaz (incluyendo default implementations desde C# 8) | Plan 02-03: Decision tree table + C# 8 DIM note + refactoring challenge where student corrects a wrong choice. |
| OOP-04 | El estudiante aplica mejores practicas de clean code: nombres descriptivos, metodos pequenos, no duplicacion | Plan 02-03: Clean code section with before/after refactoring examples and a "smell detection" challenge. |
</phase_requirements>

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Study content (Markdown) | Static files | -- | Output is `.md` files in `content/phase-02/` directory |
| C# code snippets | Inline in Markdown | -- | Snippets must compile in .NET 10 but are not standalone projects |
| Challenge solutions | Embedded `<details>` blocks | -- | Pattern established in Phase 1 |

## Standard Stack

### Core (Content Production)
| Tool | Version | Purpose | Why Standard |
|------|---------|---------|--------------|
| Markdown | GitHub-flavored | Content format | Established in Phase 1, renders in any viewer |
| C# | 14 (.NET 10) | Code snippets language | D-03 locked decision |
| `<details>` HTML | -- | Collapsible solutions | D-01 locked decision, Pattern 1 from PATTERNS.md |

### Reference Sources for Content Accuracy
| Source | URL | What to verify |
|--------|-----|----------------|
| Microsoft Learn - OOP | https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/ | Pillar definitions, sealed/virtual/override semantics |
| Microsoft Learn - Interfaces | https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/interfaces | Default interface methods behavior |
| Microsoft Learn - Coding Conventions | https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions | Clean code naming standards |
| refactoring.guru | https://refactoring.guru/refactoring | Refactoring catalog for clean code section |

## Architecture Patterns

### Content Structure (3 Plans)

```
content/phase-02/
├── 02-01-oop-pilares.md          # OOP 4 pillars + challenges (OOP-01)
├── 02-02-solid-principios.md     # SOLID 5 principles + trade-offs (OOP-02)
└── 02-03-clean-code-abstracciones.md  # Clean code + abstract vs interface (OOP-03, OOP-04)
```

### Plan 02-01: OOP Pillars — Section Order

| Section | Topic | Time Est. | Challenges |
|---------|-------|-----------|------------|
| 1 | Encapsulacion (access modifiers, properties, init-only) | ~20 min | 1 |
| 2 | Herencia (virtual/override/new/sealed + interview trap) | ~25 min | 1-2 |
| 3 | Polimorfismo (runtime dispatch, casting, is/as) | ~20 min | 1 |
| 4 | Abstraccion (abstract classes intro, abstract methods) | ~15 min | 1 |
| 5 | Resumen: Puntos Clave para la Entrevista | ~5 min | -- |

**Key content for Herencia (D-06):**
- `virtual` + `override` = runtime polymorphism (dispatch at runtime based on actual type)
- `new` keyword = method hiding (dispatch based on reference type, NOT actual type)
- Classic interview trap: `Base b = new Derived(); b.Method();` — different behavior with `override` vs `new`
- `sealed` — prevents further inheritance/override, used for performance and design intent

### Plan 02-02: SOLID Principles — Section Order

| Section | Principle | Anti-pattern (D-09) | Challenges |
|---------|-----------|---------------------|------------|
| 1 | SRP - Single Responsibility | Over-decomposition / anemic domain model | 1 |
| 2 | OCP - Open/Closed | Abstraction overkill (interface for everything) | 1 |
| 3 | LSP - Liskov Substitution | Subtypes throwing NotImplementedException | 1 |
| 4 | ISP - Interface Segregation | Over-granular interfaces always used together | 1 |
| 5 | DIP - Dependency Inversion | DI complexity in small projects | 1 |
| 6 | Resumen: Puntos Clave para la Entrevista | -- | -- |

**Each section follows D-08 structure:**
1. Definition + correct application example (business domain)
2. Challenge (student applies the principle)
3. "Cuando va demasiado lejos" — concrete anti-pattern with code showing the harm

### Plan 02-03: Clean Code + Abstract vs Interface — Section Order

| Section | Topic | Time Est. | Challenges |
|---------|-------|-----------|------------|
| 1 | Clean Code: Nombres descriptivos | ~15 min | 1 (rename refactoring) |
| 2 | Clean Code: Metodos pequenos + guard clauses | ~15 min | 1 (extract method) |
| 3 | Clean Code: Eliminacion de duplicacion (DRY) | ~10 min | -- (integrated in challenge 3) |
| 4 | Decision Tree: Abstract vs Interface (D-11, D-12) | ~15 min | 1 (refactoring) |
| 5 | C# 8 Default Interface Implementations | ~10 min | -- (awareness + note) |
| 6 | Challenge integrador (refactoring completo) | ~20 min | 1 |
| 7 | Resumen: Puntos Clave para la Entrevista | ~5 min | -- |

### Pattern: Decision Tree as Markdown Table (D-12)

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

### Pattern: virtual/override/new Interview Trap

```csharp
public class Animal
{
    public virtual void Speak() => Console.WriteLine("...");
    public void Move() => Console.WriteLine("Animal moves");
}

public class Dog : Animal
{
    public override void Speak() => Console.WriteLine("Woof!");  // override
    public new void Move() => Console.WriteLine("Dog runs");     // hiding
}

// Interview question:
Animal a = new Dog();
a.Speak();  // "Woof!" — override: runtime dispatch based on ACTUAL type
a.Move();   // "Animal moves" — new: compile-time dispatch based on REFERENCE type

Dog d = new Dog();
d.Move();   // "Dog runs" — reference type IS Dog, so Dog.Move() is called
```

### Anti-Patterns to Avoid in Content
- **"Animal/Dog/Cat" exclusively** — D-05 requires 50% business domain scenarios
- **Theory without code** — every concept needs a compilable snippet
- **Solutions without "Puntos clave"** — Pattern 1 requires 2-4 explanation bullets per solution
- **Missing "cuando NO aplicar"** — D-10 requires every SOLID principle to have this

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Content format | Custom template system | Phase 1 PATTERNS.md patterns | Consistency across phases |
| Challenge difficulty curve | Ad-hoc ordering | D-02 progressive structure (basic → interview) | Proven pedagogical approach |
| Code verification | Manual review only | `dotnet build` check on snippets | D-03 requires .NET 10 compilation |

**Key insight:** This is a documentation project. The "don't hand-roll" applies to content structure, not software libraries.

## Common Pitfalls

### Pitfall 1: Incorrect virtual/override/new Behavior in Examples
**What goes wrong:** Code examples show `new` keyword but don't demonstrate the surprising behavior difference when using a base-type reference.
**Why it happens:** Authors test with the derived type reference only, where both `new` and `override` appear to work the same.
**How to avoid:** Every `new` vs `override` example MUST show the behavior with BOTH a base-type reference AND a derived-type reference. The output difference is the teaching point.
**Warning signs:** If both examples produce the same console output, the trap isn't visible.

### Pitfall 2: SOLID Examples Too Abstract/Trivial
**What goes wrong:** SOLID examples use `ILogger`/`IRepository` without business context, making the principles feel academic rather than practical.
**Why it happens:** Generic examples are easier to write but don't show WHY the principle matters.
**How to avoid:** Use business domains (payment processing, order management, notifications) that show real pain when principles are violated. D-05 enforces this.
**Warning signs:** If a student could just memorize the pattern without understanding the WHY, the example is too abstract.

### Pitfall 3: Default Interface Methods Overstated
**What goes wrong:** Content presents default interface methods as making abstract classes obsolete.
**Why it happens:** The feature seems to close the gap, but critical limitations exist.
**How to avoid:** Clearly state: DIMs cannot have fields, cannot access instance state, and are only accessible via the interface reference (not the implementing class reference directly). [VERIFIED: Microsoft Learn docs]
**Warning signs:** If the content doesn't mention at least 2 limitations of DIMs, it's incomplete.

### Pitfall 4: Clean Code Section Too Prescriptive Without Context
**What goes wrong:** Rules like "methods should be 5 lines" presented as absolute truths.
**Why it happens:** Clean code guidelines are often taught dogmatically.
**How to avoid:** Present guidelines with pragmatic nuance: "prefer small methods, but don't extract a method that's only called once and makes the flow harder to follow."
**Warning signs:** If every guideline lacks a "when this doesn't apply" note.

### Pitfall 5: Anemic Domain Model Presented as Always Bad
**What goes wrong:** SRP anti-pattern section condemns anemic models without noting valid architectural patterns.
**Why it happens:** D-09 says include anemic domain model as anti-pattern of SRP over-decomposition.
**How to avoid:** Note that anemic models ARE intentional in CQRS/event-sourcing architectures. The problem is when it happens accidentally in a traditional layered app.
**Warning signs:** Student leaves thinking "anemic = always wrong."

## Code Examples

### OOP Pillar: Encapsulation with Modern C#

```csharp
// Source: C# language reference — properties, init-only setters
public class BankAccount
{
    private decimal _balance;
    
    public string AccountNumber { get; init; }  // immutable after construction
    public decimal Balance => _balance;          // read-only exposure
    
    public BankAccount(string accountNumber, decimal initialBalance)
    {
        AccountNumber = accountNumber;
        _balance = initialBalance;
    }
    
    public bool Withdraw(decimal amount)
    {
        if (amount <= 0 || amount > _balance)
            return false;
        
        _balance -= amount;
        return true;
    }
}
```

### SOLID: LSP Violation (NotImplementedException trap)

```csharp
// Source: Common interview pattern [ASSUMED - widely documented]
public interface IReadWriteRepository<T>
{
    T GetById(int id);
    void Add(T entity);
    void Delete(int id);
}

// LSP VIOLATION: ReadOnlyCache "is-a" IReadWriteRepository but can't fulfill the contract
public class ReadOnlyCache<T> : IReadWriteRepository<T>
{
    public T GetById(int id) => /* works */;
    public void Add(T entity) => throw new NotImplementedException();  // VIOLATES LSP
    public void Delete(int id) => throw new NotImplementedException(); // VIOLATES LSP
}

// FIX: Segregate the interface (ISP helps LSP)
public interface IReadRepository<T> { T GetById(int id); }
public interface IWriteRepository<T> { void Add(T entity); void Delete(int id); }
```

### Clean Code: Before/After Refactoring

```csharp
// BEFORE: Violates naming, method size, duplication
public class Proc
{
    public decimal Calc(List<Item> items, string t)
    {
        decimal total = 0;
        foreach (var i in items)
        {
            if (t == "domestic")
                total += i.Price * 1.16m;
            else if (t == "international")
                total += i.Price * 1.21m;
            else
                total += i.Price;
        }
        if (total > 1000)
            total *= 0.95m;
        return total;
    }
}

// AFTER: Descriptive names, small methods, no magic numbers
public class OrderCalculator
{
    private const decimal DomesticTaxRate = 0.16m;
    private const decimal InternationalTaxRate = 0.21m;
    private const decimal BulkDiscountThreshold = 1000m;
    private const decimal BulkDiscountRate = 0.05m;

    public decimal CalculateTotal(List<OrderItem> items, ShippingRegion region)
    {
        decimal subtotal = items.Sum(item => ApplyTax(item.Price, region));
        return ApplyBulkDiscount(subtotal);
    }

    private decimal ApplyTax(decimal price, ShippingRegion region) => region switch
    {
        ShippingRegion.Domestic => price * (1 + DomesticTaxRate),
        ShippingRegion.International => price * (1 + InternationalTaxRate),
        _ => price
    };

    private decimal ApplyBulkDiscount(decimal total) =>
        total > BulkDiscountThreshold ? total * (1 - BulkDiscountRate) : total;
}
```

### Default Interface Methods — Limitations Demo

```csharp
// Source: Microsoft Learn - default interface methods [VERIFIED: Microsoft Learn]
public interface INotificationChannel
{
    void Send(string message);
    
    // Default implementation — available since C# 8
    void SendUrgent(string message)
    {
        Send($"[URGENT] {message}");  // Can call other interface members
        // Cannot access: fields, instance state, constructors
    }
}

public class EmailChannel : INotificationChannel
{
    private readonly string _smtpServer;  // Interface CAN'T have this
    
    public EmailChannel(string smtp) => _smtpServer = smtp;
    public void Send(string message) => /* send via SMTP */;
    // SendUrgent is inherited from interface default — but ONLY via interface reference:
}

// Access pattern:
var email = new EmailChannel("smtp.example.com");
// email.SendUrgent("test");  // COMPILE ERROR — not accessible via class reference
INotificationChannel channel = email;
channel.SendUrgent("test");  // OK — accessible via interface reference
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Interfaces = pure contracts only | Interfaces can have default implementations | C# 8 (2019) | Blurs the abstract class/interface line |
| `sealed` rarely used | `sealed` recommended by default for perf + design intent | .NET 6+ analyzers | Challenges should show sealed as default |
| SOLID as absolute rules | SOLID as guidelines with pragmatic trade-offs | Industry trend 2020+ | D-08/D-10 already capture this |
| Constructor injection only | Primary constructors (C# 12) simplify DI | C# 12 (2023) | DIP examples can use primary constructors |

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | `new` keyword method hiding is still a common .NET interview question in 2026 | Code Examples / Plan 02-01 | LOW — fundamentals rarely stop being asked |
| A2 | Anemic domain model debate remains relevant for SRP discussion | Pitfall 5 | LOW — architectural pattern still in active use |
| A3 | Default interface methods accessible only via interface reference (not class reference) | Code Examples | VERIFIED via Microsoft Learn — not an assumption |

**All critical claims verified.** The only assumptions are about interview relevance (LOW risk).

## Open Questions

1. **Exact challenge domains for each plan**
   - What we know: D-05 says 50/50 abstract vs business domain, with examples like payment, notifications, inventory
   - What's unclear: Which specific domain maps best to which principle
   - Recommendation: Claude's discretion per CONTEXT.md. Recommend: payment→SRP/OCP, inventory→LSP/ISP, notifications→DIP, employees→OOP pillars

2. **Depth of C# 8 default interface methods section**
   - What we know: D-11 says include a note, not a full section
   - What's unclear: How much code to show
   - Recommendation: One focused example showing the limitation (field access) + the access pattern (interface reference only). No standalone challenge — integrate into the refactoring challenge.

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | Manual validation (content project, not software) |
| Config file | None — no automated tests |
| Quick run command | Visual review of Markdown rendering |
| Full suite command | `dotnet build` on extracted code snippets (if verification script exists) |

### Phase Requirements to Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| OOP-01 | 4 OOP pillars with C# examples | manual-only | Review `content/phase-02/02-01-oop-pilares.md` for all 4 pillars + challenges | N/A |
| OOP-02 | SOLID with trade-offs | manual-only | Review `content/phase-02/02-02-solid-principios.md` for 5 principles + anti-patterns | N/A |
| OOP-03 | Abstract vs interface + DIM | manual-only | Review `content/phase-02/02-03-clean-code-abstracciones.md` for decision tree + DIM note | N/A |
| OOP-04 | Clean code practices | manual-only | Review `content/phase-02/02-03-clean-code-abstracciones.md` for naming/small methods/DRY | N/A |

### Sampling Rate
- **Per task commit:** Visual review of Markdown structure + code snippet compilation check
- **Per wave merge:** Full read-through verifying all D-01 through D-12 decisions are honored
- **Phase gate:** All 3 files exist, follow Pattern 1/4/5, cover all 4 requirements

### Wave 0 Gaps
None — this is a content project. No test infrastructure needed. Verification is structural (format compliance) and content (accuracy of C# code).

## Environment Availability

Step 2.6: SKIPPED (no external dependencies identified). Phase 2 produces Markdown files only. The only potential dependency would be `dotnet build` for snippet verification, but that is optional and was confirmed available in Phase 1.

## Sources

### Primary (HIGH confidence)
- [Microsoft Learn - Interfaces](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/interfaces) — default interface methods, limitations
- [Microsoft Learn - Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions) — naming standards
- [Microsoft Learn - Override modifier](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/override) — virtual/override semantics
- Phase 1 PATTERNS.md — established content patterns (Pattern 1, 4, 5)
- Phase 1 content files — format reference

### Secondary (MEDIUM confidence)
- [ByteCrafted - SOLID Interview Questions](https://bytecrafted.dev/solid-principles-interview-questions/) — common interview angles
- [Code Maze - Default Interface Method](https://code-maze.com/csharp-default-interface-method/) — DIM best practices
- [NDepend Blog - Abstract Class vs Interface](https://blog.ndepend.com/abstract-class-vs-interface-in-c/) — decision criteria

### Tertiary (LOW confidence)
- None — all claims verified against primary or secondary sources

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — inherited from Phase 1, no new tooling needed
- Architecture: HIGH — 3-plan structure defined in CONTEXT.md, patterns from Phase 1 apply directly
- Pitfalls: HIGH — verified against Microsoft docs (DIM limitations) and common interview resources
- Content accuracy: HIGH — C# semantics verified against official language reference

**Research date:** 2026-05-02
**Valid until:** 2026-08-02 (stable — OOP/SOLID fundamentals don't change, C# 14 features confirmed)
