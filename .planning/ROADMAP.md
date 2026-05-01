# Roadmap: Estudio .NET Beginners — Plan de Estudios

## Overview

Plan de estudios progresivo de 8 fases que lleva al estudiante desde C# moderno hasta preparacion completa para entrevistas .NET generalistas. Cada fase construye sobre la anterior siguiendo la cadena de dependencias: C# -> OOP/SOLID -> Design Patterns -> ASP.NET Core -> EF Core -> Azure -> System Design. Los algoritmos corren como track paralelo durante las fases 4-5. Dedicacion estimada: 1-2 horas diarias, ~24-28 semanas totales.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Modern C# Foundations** - Dominar C# 8-14 features y el puente de .NET Framework a .NET moderno
- [ ] **Phase 2: OOP + SOLID + Clean Code** - Aplicar los 4 pilares de OOP y los 5 principios SOLID con fluidez
- [ ] **Phase 3: Design Patterns** - Implementar y justificar patrones creacionales, estructurales y de comportamiento
- [ ] **Phase 4: ASP.NET Core + Testing** - Construir una REST API completa con autenticacion, testing y buenas practicas
- [ ] **Phase 5: EF Core + SQL Server** - Dominar acceso a datos con EF Core, T-SQL y patrones de datos
- [ ] **Phase 6: Algoritmos + Big O** - Resolver problemas LeetCode con analisis de complejidad (track paralelo con fases 4-5)
- [ ] **Phase 7: Azure AZ-900 + AZ-204** - Comprender y aplicar servicios Azure desde fundamentos hasta nivel developer
- [ ] **Phase 8: System Design + Interview Capstone** - Disenar sistemas, completar portfolio project y practicar mock interviews

## Phase Details

### Phase 1: Modern C# Foundations
**Goal**: El estudiante escribe C# moderno idomiatico y comprende las diferencias fundamentales entre .NET Framework y .NET moderno
**Depends on**: Nothing (first phase)
**Requirements**: CSHA-01, CSHA-02, CSHA-03, CSHA-04, CSHA-05, CSHA-06
**Success Criteria** (what must be TRUE):
  1. El estudiante refactoriza codigo .NET Framework a .NET moderno identificando diferencias en hosting, DI, config y serialization
  2. El estudiante escribe codigo usando nullable refs, records, pattern matching, primary constructors y collection expressions sin referencia
  3. El estudiante implementa flujos async/await correctos y detecta/corrige trampas comunes (async void, .Result deadlocks, Task.Run mal uso)
  4. El estudiante escribe queries LINQ complejas con deferred execution y distingue IQueryable vs IEnumerable
  5. El estudiante usa generics, delegates, events y Func/Action/Predicate en ejercicios practicos
**Plans**: 3 plans

Plans:
- [x] 01-01: C# moderno (features C# 8-14) y puente Framework-a-moderno
- [x] 01-02: Async/await profundo y trampas comunes
- [ ] 01-03: LINQ, generics, delegates y programacion funcional en C#

### Phase 2: OOP + SOLID + Clean Code
**Goal**: El estudiante explica y aplica OOP y SOLID con confianza, escribiendo codigo limpio y justificando decisiones de diseno
**Depends on**: Phase 1
**Requirements**: OOP-01, OOP-02, OOP-03, OOP-04
**Success Criteria** (what must be TRUE):
  1. El estudiante implementa los 4 pilares de OOP en C# y puede explicar cada uno verbalmente con ejemplos
  2. El estudiante aplica cada principio SOLID en codigo y articula trade-offs de cada uno
  3. El estudiante decide correctamente entre clase abstracta e interfaz, incluyendo default implementations de C# 8
  4. El estudiante refactoriza codigo sucio aplicando nombres descriptivos, metodos pequenos y eliminacion de duplicacion
**Plans**: TBD

Plans:
- [ ] 02-01: Los 4 pilares de OOP con coding challenges
- [ ] 02-02: SOLID principios uno a uno con trade-offs
- [ ] 02-03: Clean code, abstract vs interface, y coding challenges integrados

### Phase 3: Design Patterns
**Goal**: El estudiante implementa los patrones de diseno principales en C# y sabe cuando usarlos (y cuando no)
**Depends on**: Phase 2
**Requirements**: DPAT-01, DPAT-02, DPAT-03, DPAT-04, DPAT-05, DPAT-06
**Success Criteria** (what must be TRUE):
  1. El estudiante implementa desde cero los 5 patrones creacionales principales (Factory Method, Abstract Factory, Builder, Singleton, Prototype)
  2. El estudiante implementa los patrones estructurales clave (Adapter, Decorator, Facade, Proxy, Composite) y explica cuando aplicar cada uno
  3. El estudiante implementa los patrones de comportamiento principales (Observer, Strategy, Command, Chain of Responsibility, Template Method, Iterator, Mediator)
  4. El estudiante identifica el patron correcto dado un escenario de entrevista y justifica la eleccion, incluyendo cuando NO usar un patron
  5. El estudiante conecta patrones con su uso real en ASP.NET Core (Builder, Chain of Responsibility, Factory, Singleton via DI)
**Plans**: TBD

Plans:
- [ ] 03-01: Patrones creacionales (Factory Method, Abstract Factory, Builder, Singleton, Prototype)
- [ ] 03-02: Patrones estructurales (Adapter, Decorator, Facade, Proxy, Composite)
- [ ] 03-03: Patrones de comportamiento (Observer, Strategy, Command, CoR, Template Method, Iterator, Mediator)
- [ ] 03-04: Seleccion de patrones, anti-patterns y conexion con ASP.NET Core

### Phase 4: ASP.NET Core + Testing
**Goal**: El estudiante construye una REST API completa con autenticacion, middleware, error handling y tests automatizados
**Depends on**: Phase 3
**Requirements**: ASPN-01, ASPN-02, ASPN-03, ASPN-04, ASPN-05, ASPN-06, ASPN-07
**Success Criteria** (what must be TRUE):
  1. El estudiante configura un proyecto ASP.NET Core 8 desde cero con DI, logging e IOptions pattern
  2. El estudiante crea endpoints funcionales con Minimal APIs y Controllers, y explica cuando usar cada approach
  3. El estudiante implementa JWT authentication con authorization policies que protegen endpoints correctamente
  4. El estudiante crea middleware personalizado y configura global error handling, health checks y background services
  5. El estudiante escribe unit tests (xUnit + Moq, AAA pattern) e integration tests (WebApplicationFactory) que verifican el comportamiento de la API
**Plans**: TBD

Plans:
- [ ] 04-01: Proyecto ASP.NET Core 8 — DI, config, logging, pipeline
- [ ] 04-02: REST API con Minimal APIs y Controllers
- [ ] 04-03: JWT auth, middleware personalizado, error handling y servicios
- [ ] 04-04: Testing con xUnit, Moq y WebApplicationFactory

### Phase 5: EF Core + SQL Server
**Goal**: El estudiante domina acceso a datos con EF Core y SQL Server, desde modelado hasta optimizacion de queries
**Depends on**: Phase 4
**Requirements**: EFCO-01, EFCO-02, EFCO-03, EFCO-04, EFCO-05, EFCO-06, EFCO-07
**Success Criteria** (what must be TRUE):
  1. El estudiante crea un DbContext Code-First con relaciones via Fluent API y ejecuta migrations via CLI
  2. El estudiante escribe queries EF Core eficientes usando Include, AsNoTracking y compiled queries, e identifica y soluciona N+1
  3. El estudiante escribe T-SQL con JOINs, GROUP BY, subqueries, CTEs y entiende indexing basico y query execution plans
  4. El estudiante implementa el patron Repository y debate sus trade-offs con EF Core con argumentos concretos
  5. El estudiante conecta la API de ASP.NET Core (de Phase 4) con una base de datos real via EF Core
**Plans**: TBD

Plans:
- [ ] 05-01: EF Core fundamentals — DbContext, Code-First, Fluent API, migrations
- [ ] 05-02: Queries eficientes, N+1, AsNoTracking y optimizacion
- [ ] 05-03: T-SQL, indexing y query execution plans
- [ ] 05-04: Repository pattern, integracion API + DB

### Phase 6: Algoritmos + Big O
**Goal**: El estudiante resuelve problemas de algoritmos en C# con analisis de complejidad, alcanzando consistencia en LeetCode Easy y capacidad en Medium
**Depends on**: Phase 1 (C# fluency). NOTA: Esta fase corre en PARALELO con las fases 4-5 como track diario de 30 min
**Requirements**: ALGO-01, ALGO-02, ALGO-03, ALGO-04, ALGO-05, ALGO-06, ALGO-07, ALGO-08
**Success Criteria** (what must be TRUE):
  1. El estudiante analiza la complejidad temporal y espacial de cualquier algoritmo usando notacion Big O
  2. El estudiante resuelve problemas de arrays/strings usando two pointers y sliding window en C#
  3. El estudiante implementa y usa linked lists, stacks, queues, Dictionary y HashSet para optimizar soluciones
  4. El estudiante realiza BFS y DFS en arboles y grafos, e implementa binary search y sus variantes
  5. El estudiante resuelve problemas basicos de dynamic programming y completa LeetCode Easy de forma consistente y Medium ocasionalmente
**Plans**: TBD

Plans:
- [ ] 06-01: Big O notation y problemas de arrays/strings (two pointers, sliding window)
- [ ] 06-02: Linked lists, stacks, queues y hash tables
- [ ] 06-03: Arboles, grafos (BFS/DFS) y binary search
- [ ] 06-04: Dynamic programming y practica LeetCode integrada

### Phase 7: Azure AZ-900 + AZ-204
**Goal**: El estudiante comprende conceptos cloud y aplica servicios Azure clave a nivel developer
**Depends on**: Phase 5
**Requirements**: AZUR-01, AZUR-02, AZUR-03, AZUR-04, AZUR-05, AZUR-06, AZUR-07, AZUR-08
**Success Criteria** (what must be TRUE):
  1. El estudiante explica IaaS, PaaS, SaaS y modelos de responsabilidad compartida con ejemplos Azure
  2. El estudiante conoce los servicios core de Azure y aprueba el examen AZ-900
  3. El estudiante despliega una app ASP.NET Core en App Service e implementa Azure Functions (HTTP y Timer triggers)
  4. El estudiante usa Azure Blob Storage y Service Bus desde C# y configura Key Vault con Managed Identity
  5. El estudiante conoce Cosmos DB, Container Apps y Application Insights a nivel conceptual para discusion en entrevistas
**Plans**: TBD

Plans:
- [ ] 07-01: Cloud concepts y servicios core Azure (AZ-900 prep)
- [ ] 07-02: Examen AZ-900 y transicion a nivel developer
- [ ] 07-03: App Service, Azure Functions y deployment
- [ ] 07-04: Storage, Service Bus, Key Vault y Managed Identity
- [ ] 07-05: Cosmos DB, Container Apps, Application Insights (conceptual)

### Phase 8: System Design + Interview Capstone
**Goal**: El estudiante disena sistemas, completa un portfolio project y demuestra interview readiness bajo presion de tiempo
**Depends on**: Phase 5, Phase 6, Phase 7
**Requirements**: SYSD-01, SYSD-02, SYSD-03, SYSD-04, SYSD-05, SYSD-06, SYSD-07
**Success Criteria** (what must be TRUE):
  1. El estudiante aplica un framework estructurado para system design: requirements, componentes, data flow, trade-offs
  2. El estudiante disena en pizarra sistemas basicos (URL shortener, notification service, chat app) y discute trade-offs de monolito vs microservicios
  3. El estudiante conoce patrones de escalabilidad (caching, load balancing, message queues, CDN) y los conecta con servicios Azure
  4. El estudiante tiene un portfolio project funcional (REST API + DB + Azure deploy) listo para presentar en entrevistas
  5. El estudiante completa al menos 2 mock interviews, explica codigo bajo presion de tiempo y prepara respuestas para preguntas trampa .NET
**Plans**: TBD

Plans:
- [ ] 08-01: Framework de system design y patrones de escalabilidad
- [ ] 08-02: Ejercicios de diseno (URL shortener, notification service, chat app)
- [ ] 08-03: Portfolio project (REST API + DB + Azure deploy)
- [ ] 08-04: Mock interviews y preguntas trampa .NET

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8
Note: Phase 6 (Algoritmos) runs as a parallel daily track alongside Phases 4-5.

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Modern C# Foundations | 0/3 | Not started | - |
| 2. OOP + SOLID + Clean Code | 0/3 | Not started | - |
| 3. Design Patterns | 0/4 | Not started | - |
| 4. ASP.NET Core + Testing | 0/4 | Not started | - |
| 5. EF Core + SQL Server | 0/4 | Not started | - |
| 6. Algoritmos + Big O | 0/4 | Not started | - |
| 7. Azure AZ-900 + AZ-204 | 0/5 | Not started | - |
| 8. System Design + Interview Capstone | 0/4 | Not started | - |
