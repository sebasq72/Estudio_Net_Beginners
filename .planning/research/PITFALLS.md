# Domain Pitfalls: .NET Study Plan & Interview Preparation

**Domain:** .NET Developer Interview Preparation
**Profile:** Returning .NET Framework developer, 1-2 hrs/day
**Researched:** 2026-04-30
**Confidence:** HIGH

## Critical Pitfalls (Study-Killers & Interview Failures)

### Pitfall 1: Using .NET Framework Patterns That Are Now Anti-Patterns
**What goes wrong:** Practicing patterns from .NET Framework (Global.asax, Web.config, HTTP modules, `System.Web`) and presenting them in interviews. Modern .NET has fundamentally different architecture.

**Translation gap:**
| .NET Framework | Modern .NET |
|---------------|-------------|
| Global.asax | Program.cs (minimal hosting) |
| Web.config | appsettings.json + IConfiguration |
| HTTP Modules/Handlers | Middleware pipeline |
| Startup.cs (Configure/ConfigureServices) | Builder pattern in Program.cs (removed in .NET 6+) |
| Unity/Autofac required | Built-in DI container |
| WebClient / HttpWebRequest | IHttpClientFactory |
| Newtonsoft.Json | System.Text.Json (default) |

**Warning signs:** `HttpContext.Current`, `web.config`, `System.Web` namespace usage.
**Prevention:** Build a fresh ASP.NET Core project on .NET 8 on day one.
**Phase:** Phase 1 — address immediately.

---

### Pitfall 2: Memorizing Answers Without Understanding Mechanisms
**What goes wrong:** Knows "async/await prevents blocking" but cannot answer "what happens when you call `.Result` on a Task in an ASP.NET controller?" Interviewers dig into the "what happens when..." layer.

**High-risk topics:**
- async/await state machine (what does the compiler generate?)
- DI lifetime captive dependency (Scoped injected into Singleton)
- IQueryable vs IEnumerable execution difference
- Middleware pipeline request/response flow

**Prevention:** For each concept, prepare 3 layers: definition → mechanism → edge case/failure mode.
**Phase:** Phase 2 (C#) + Phase 3 (ASP.NET Core).

---

### Pitfall 3: Not Knowing 7 Versions of C# Evolution (C# 8-14)
**What goes wrong:** Writes dated C# 6-era code in live coding interviews. Key missed features:
- C# 8: Nullable reference types, switch expressions, async streams
- C# 9: Records, init-only setters, top-level statements
- C# 10: Global usings, file-scoped namespaces, record structs
- C# 11: Required members, raw string literals, generic math
- C# 12: Primary constructors, collection expressions, alias any type
- C# 13: params collections, new lock type, partial properties

**Warning signs:** `class Program { static void Main() }`, `new List<string>()` instead of `[]`, no pattern matching.
**Prevention:** Prioritize: nullable refs, records, pattern matching, top-level statements, primary constructors.
**Phase:** Phase 1 — must learn before any coding exercises.

---

### Pitfall 4: Assuming EF6 Knowledge Transfers to EF Core
**What goes wrong:** EF Core is a ground-up rewrite, NOT an upgrade. Key differences:
- No lazy loading by default (must opt in)
- No EDMX designer (Code-First only practical)
- Different change tracking behavior
- No ObjectContext
- Different migrations (CLI-driven, not GUI)
- Split queries for collection navigation
- AsNoTracking() is critical for performance

**Warning signs:** Expecting `.edmx` files, `Database.SetInitializer`, assuming lazy loading works.
**Prevention:** Treat EF Core as a new ORM to learn from scratch.
**Phase:** Phase 4.

---

### Pitfall 5: Neglecting System Design for Mid/Senior Interviews
**What goes wrong:** All study time goes to C# syntax and algorithms. Cannot design a system when asked "Design a URL shortener." At mid-senior level, system design is often a hard gate.

**Prevention:**
- Allocate 20-30% of study time to system design from Phase 2 onward
- Learn structured approach: requirements → high-level design → components → tradeoffs
- Know .NET-specific patterns: Clean Architecture, Vertical Slice, CQRS

**Phase:** Phase 6, but introduce gradually from Phase 2.

---

## Moderate Pitfalls

### Pitfall 6: Wrong Topic Order
**Wrong:** Starting with Azure/microservices before understanding DI, middleware, async.
**Right order:** Modern C# → OOP/SOLID → Design Patterns → ASP.NET Core → EF Core/SQL → Azure → System Design

### Pitfall 7: Solving Algorithms in Non-C# Style
**What goes wrong:** Writes Java-style or Python-style solutions. Doesn't use C# idioms: LINQ, Span<T>, HashSet<T>, PriorityQueue<T,T> (.NET 6+), collection expressions.
**Prevention:** Always solve in C#. Actively use LINQ, framework collections, pattern matching.

### Pitfall 8: async/await Traps
| Trap | Effect | Prevention |
|------|--------|-----------|
| `async void` | Exceptions lost, can't await | Only for event handlers |
| `.Result` / `.Wait()` | Deadlock (legacy SynchronizationContext) | Always await |
| Not using `ConfigureAwait(false)` in libraries | Context issues | Know when/why it matters |
| `Task.Run` misuse | Unnecessary thread pool pressure | Understand Task.Run vs await |

**Note:** ASP.NET Core has no SynchronizationContext (unlike .NET Framework ASP.NET) — but interviewers still test this.

### Pitfall 9: Skipping Testing
**Why it matters:** Mid-to-senior interviews ask "How would you test this?" or include live testing exercises.
**Minimum to learn:** xUnit [Fact]/[Theory], Moq/NSubstitute basics, Arrange-Act-Assert, WebApplicationFactory.

### Pitfall 10: Underestimating the .NET Framework → Modern .NET Gap
See Pitfall 1 translation table. Additional gaps:
- IIS-only → Kestrel cross-platform (IIS now just a reverse proxy)
- Windows Services / Thread → IHostedService / BackgroundService
- Newtonsoft.Json → System.Text.Json (behavior differences!)

### Pitfall 11: Not Practicing Under Time Pressure
**What goes wrong:** Studies comfortably at own pace. In 45-min timed interviews with screen-sharing, blanks on syntax.
**Prevention:** Use a timer from Phase 2 onward. Do 2+ mock interviews. Practice talking while coding.

### Pitfall 12: Studying Deprecated Technologies
Things NOT worth studying for generalista interviews: WCF, Web Forms, Silverlight, Remoting, LINQ to SQL, .NET Standard (historical knowledge only), Xamarin, WPF/WinForms (unless desktop role).

---

## Common Interview Trap Questions

| Question | Wrong Answer | Correct Answer |
|----------|-------------|----------------|
| "Is `string` a value type?" | "Yes, it's immutable" | Reference type, immutable and interned — behaves like value type semantically |
| "What's async void?" | "Same as async Task without return" | Fire-and-forget — exceptions can't be caught by caller, only valid for event handlers |
| "Does interface support implementation?" | "No" | Since C# 8: YES, default interface implementations are supported |
| "Is `List<T>` thread-safe?" | "Yes, it's standard library" | No. Use `ConcurrentBag<T>`, `ConcurrentDictionary<K,V>`, or `ImmutableList<T>` |
| "What's the DI captive dependency problem?" | Blank | Scoped injected into Singleton becomes de-facto Singleton — causes bugs |
| "IEnumerable vs IQueryable?" | "They're similar" | IQueryable builds expression trees for SQL; IEnumerable runs in memory |

---

## Phase-Specific Warnings

| Phase | Key Pitfall | Mitigation |
|-------|-------------|------------|
| Phase 1 (Foundation) | Using VS 2019 or .NET Framework templates | Install .NET 8 SDK, VS 2022 / VS Code + C# Dev Kit |
| Phase 1 (Modern C#) | Learning all features at once | Prioritize: nullable refs, records, pattern matching, primary constructors |
| Phase 2 (Async) | Only learning the happy path | Deliberately create and fix a deadlock scenario |
| Phase 3 (ASP.NET Core) | Only studying controller-based APIs | Learn BOTH controllers AND Minimal APIs |
| Phase 4 (EF Core) | N+1 queries | AsNoTracking(), Include(), split queries |
| Phase 5 (Algorithms) | Solving in non-C# style | Always use C# idioms: LINQ, framework collections |
| Phase 6 (System Design) | Not practicing structured approach | Use template: requirements → components → tradeoffs |

---

## Study Anti-Patterns

| Anti-Pattern | Prevention |
|-------------|------------|
| Tutorial hell (watching without building) | Build 2-3 real projects |
| Breadth over depth | Know 5 Azure services well vs 20 superficially |
| No spaced repetition | 10-15 min review at start of each session; Anki for key concepts |
| Cramming without practice | Timed exercises from Phase 2 onward |
| Skipping verbal practice | Explain concepts out loud — "Feynman technique" |
