# Requirements: Estudio .NET Beginners — Plan de Estudios

**Defined:** 2026-04-30
**Core Value:** Preparar al desarrollador para pasar entrevistas tecnicas de roles .NET generalistas, con fundamentos solidos y practica aplicada en cada tema.

## v1 Requirements

### C# Moderno

- [x] **CSHA-01**: El estudiante comprende las diferencias clave entre .NET Framework y .NET moderno (hosting model, DI, config, serialization)
- [x] **CSHA-02**: El estudiante escribe codigo C# usando features de C# 8-12 (nullable refs, records, pattern matching, primary constructors, collection expressions)
- [ ] **CSHA-03**: El estudiante comprende y aplica async/await profundo: state machine, Task vs ValueTask, ConfigureAwait, cancelacion
- [ ] **CSHA-04**: El estudiante identifica y evita trampas comunes de async: async void, .Result deadlocks, Task.Run mal uso
- [x] **CSHA-05**: El estudiante usa LINQ idiomaticamente: deferred execution, IQueryable vs IEnumerable, GroupBy/SelectMany/Aggregate
- [x] **CSHA-06**: El estudiante domina generics, delegates, events, lambdas y Func/Action/Predicate

### OOP + SOLID + Mejores Practicas

- [ ] **OOP-01**: El estudiante explica y aplica los 4 pilares de OOP con ejemplos en C# (encapsulacion, herencia, polimorfismo, abstraccion)
- [ ] **OOP-02**: El estudiante aplica cada principio SOLID con codigo C# y puede explicar trade-offs
- [ ] **OOP-03**: El estudiante distingue cuándo usar clase abstracta vs interfaz (incluyendo default implementations desde C# 8)
- [ ] **OOP-04**: El estudiante aplica mejores practicas de clean code: nombres descriptivos, metodos pequenos, no duplicacion

### Design Patterns

- [ ] **DPAT-01**: El estudiante implementa en C# los patrones Creacionales principales: Factory Method, Abstract Factory, Builder, Singleton, Prototype
- [ ] **DPAT-02**: El estudiante implementa en C# los patrones Estructurales: Adapter, Decorator, Facade, Proxy, Composite
- [ ] **DPAT-03**: El estudiante implementa en C# los patrones de Comportamiento: Observer, Strategy, Command, Chain of Responsibility, Template Method, Iterator, Mediator
- [ ] **DPAT-04**: El estudiante identifica qué patron usar dado un escenario y puede justificar la eleccion
- [ ] **DPAT-05**: El estudiante conoce cuando NO aplicar un patron (over-engineering trap)
- [ ] **DPAT-06**: El estudiante conecta patrones con ASP.NET Core: Builder (WebApplicationBuilder), Chain of Responsibility (middleware), Factory (DI), Singleton (DI lifetime)

### ASP.NET Core + Testing

- [ ] **ASPN-01**: El estudiante configura un proyecto ASP.NET Core 8 desde cero: DI, logging, configuration (IOptions pattern)
- [ ] **ASPN-02**: El estudiante crea una REST API completa con Minimal APIs y Controllers
- [ ] **ASPN-03**: El estudiante implementa JWT authentication y authorization policies
- [ ] **ASPN-04**: El estudiante crea middleware personalizado y entiende el orden del pipeline
- [ ] **ASPN-05**: El estudiante implementa global error handling, health checks y background services
- [ ] **ASPN-06**: El estudiante escribe unit tests con xUnit y Moq/NSubstitute (Arrange-Act-Assert)
- [ ] **ASPN-07**: El estudiante escribe integration tests con WebApplicationFactory

### EF Core + SQL Server

- [ ] **EFCO-01**: El estudiante crea un DbContext con Code-First, configura relaciones via Fluent API y aplica migrations via CLI
- [ ] **EFCO-02**: El estudiante escribe queries EF Core eficientes: Include (eager loading), AsNoTracking, compiled queries
- [ ] **EFCO-03**: El estudiante identifica y soluciona el problema N+1 en EF Core
- [ ] **EFCO-04**: El estudiante escribe T-SQL: SELECT con JOINs, GROUP BY, subqueries, CTEs
- [ ] **EFCO-05**: El estudiante entiende indexing basico, query execution plans y cuando usarlos
- [ ] **EFCO-06**: El estudiante aplica el patron Repository y puede debatir si tiene sentido con EF Core
- [ ] **EFCO-07**: El estudiante conecta la API de ASP.NET Core con una base de datos real via EF Core

### Algoritmos + Big O

- [ ] **ALGO-01**: El estudiante analiza la complejidad temporal y espacial de un algoritmo con notacion Big O
- [ ] **ALGO-02**: El estudiante resuelve problemas de arrays y strings: two pointers, sliding window
- [ ] **ALGO-03**: El estudiante implementa y usa linked lists, stacks y queues en C# idiomatico
- [ ] **ALGO-04**: El estudiante aplica hash tables (Dictionary, HashSet) para optimizar soluciones
- [ ] **ALGO-05**: El estudiante realiza BFS y DFS en arboles y grafos
- [ ] **ALGO-06**: El estudiante aplica binary search y sus variantes
- [ ] **ALGO-07**: El estudiante resuelve problemas basicos de dynamic programming (memoizacion vs tabulacion)
- [ ] **ALGO-08**: El estudiante resuelve problemas LeetCode Easy de forma consistente y Medium ocasionalmente en C#

### Azure

- [ ] **AZUR-01**: El estudiante comprende conceptos cloud: IaaS, PaaS, SaaS y los modelos de responsabilidad compartida
- [ ] **AZUR-02**: El estudiante conoce los servicios core de Azure: App Service, Functions, Azure SQL, Blob Storage (AZ-900 level)
- [ ] **AZUR-03**: El estudiante aprueba el examen AZ-900 Azure Fundamentals
- [ ] **AZUR-04**: El estudiante despliega una app ASP.NET Core en Azure App Service
- [ ] **AZUR-05**: El estudiante implementa una Azure Function (HTTP trigger + Timer trigger)
- [ ] **AZUR-06**: El estudiante usa Azure Blob Storage y Azure Service Bus desde C#
- [ ] **AZUR-07**: El estudiante configura Azure Key Vault con Managed Identity desde ASP.NET Core
- [ ] **AZUR-08**: El estudiante conoce Cosmos DB, Container Apps y Application Insights a nivel conceptual

### System Design + Interview Capstone

- [ ] **SYSD-01**: El estudiante aplica un framework estructurado para responder preguntas de system design: requirements → componentes → data flow → trade-offs
- [ ] **SYSD-02**: El estudiante disena en pizarra sistemas basicos: URL shortener, notification service, chat app
- [ ] **SYSD-03**: El estudiante conoce patrones de escalabilidad: caching, load balancing, message queues, CDN
- [ ] **SYSD-04**: El estudiante puede discutir trade-offs entre monolito vs microservicios con argumentos concretos
- [ ] **SYSD-05**: El estudiante tiene un portfolio project (REST API + DB + Azure deploy) para presentar en entrevistas
- [ ] **SYSD-06**: El estudiante completa al menos 2 mock interviews y puede explicar su codigo bajo presion de tiempo
- [ ] **SYSD-07**: El estudiante prepara respuestas para las preguntas trampa mas comunes de entrevistas .NET

## v2 Requirements

### Avanzado (Post-Interview Prep)

- **ADV-01**: gRPC con ASP.NET Core
- **ADV-02**: SignalR para real-time communication
- **ADV-03**: Docker + containerization profunda
- **ADV-04**: Clean Architecture completa con vertical slice
- **ADV-05**: CQRS + MediatR implementation completa
- **ADV-06**: .NET Aspire (cloud-native development)
- **ADV-07**: Microsoft.Extensions.AI (LLMs desde .NET)

## Out of Scope

| Feature | Reason |
|---------|--------|
| WPF / WinForms | Desktop-only, no aplica a roles .NET generalista |
| Xamarin / .NET MAUI | Mobile-specific, fuera del scope |
| WCF / Web Forms | Tecnologias obsoletas — no preguntan en entrevistas modernas |
| Blazor (profundo) | No es standard aun en entrevistas; awareness solamente |
| F# / VB.NET | Lenguajes de nicho |
| Kubernetes (profundo) | Territorio de roles DevOps |
| Microservicios (profundo) | Conceptos si, implementacion completa no |
| Frontend (React/Angular) | Fuera del scope .NET generalista |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| CSHA-01 | Phase 1 | Complete |
| CSHA-02 | Phase 1 | Complete |
| CSHA-03 | Phase 1 | Pending |
| CSHA-04 | Phase 1 | Pending |
| CSHA-05 | Phase 1 | Complete |
| CSHA-06 | Phase 1 | Complete |
| OOP-01 | Phase 2 | Pending |
| OOP-02 | Phase 2 | Pending |
| OOP-03 | Phase 2 | Pending |
| OOP-04 | Phase 2 | Pending |
| DPAT-01 | Phase 3 | Pending |
| DPAT-02 | Phase 3 | Pending |
| DPAT-03 | Phase 3 | Pending |
| DPAT-04 | Phase 3 | Pending |
| DPAT-05 | Phase 3 | Pending |
| DPAT-06 | Phase 3 | Pending |
| ASPN-01 | Phase 4 | Pending |
| ASPN-02 | Phase 4 | Pending |
| ASPN-03 | Phase 4 | Pending |
| ASPN-04 | Phase 4 | Pending |
| ASPN-05 | Phase 4 | Pending |
| ASPN-06 | Phase 4 | Pending |
| ASPN-07 | Phase 4 | Pending |
| EFCO-01 | Phase 5 | Pending |
| EFCO-02 | Phase 5 | Pending |
| EFCO-03 | Phase 5 | Pending |
| EFCO-04 | Phase 5 | Pending |
| EFCO-05 | Phase 5 | Pending |
| EFCO-06 | Phase 5 | Pending |
| EFCO-07 | Phase 5 | Pending |
| ALGO-01 | Phase 6 | Pending |
| ALGO-02 | Phase 6 | Pending |
| ALGO-03 | Phase 6 | Pending |
| ALGO-04 | Phase 6 | Pending |
| ALGO-05 | Phase 6 | Pending |
| ALGO-06 | Phase 6 | Pending |
| ALGO-07 | Phase 6 | Pending |
| ALGO-08 | Phase 6 | Pending |
| AZUR-01 | Phase 7 | Pending |
| AZUR-02 | Phase 7 | Pending |
| AZUR-03 | Phase 7 | Pending |
| AZUR-04 | Phase 7 | Pending |
| AZUR-05 | Phase 7 | Pending |
| AZUR-06 | Phase 7 | Pending |
| AZUR-07 | Phase 7 | Pending |
| AZUR-08 | Phase 7 | Pending |
| SYSD-01 | Phase 8 | Pending |
| SYSD-02 | Phase 8 | Pending |
| SYSD-03 | Phase 8 | Pending |
| SYSD-04 | Phase 8 | Pending |
| SYSD-05 | Phase 8 | Pending |
| SYSD-06 | Phase 8 | Pending |
| SYSD-07 | Phase 8 | Pending |

**Coverage:**
- v1 requirements: 53 total
- Mapped to phases: 43
- Unmapped: 0 ✓

---
*Requirements defined: 2026-04-30*
*Last updated: 2026-04-30 after initial definition*
