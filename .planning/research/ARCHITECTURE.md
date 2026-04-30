# Learning Architecture: .NET Framework to Modern .NET Interview Readiness

**Domain:** Developer Study Plan / Learning Path Design
**Researched:** 2026-04-30
**Target Learner:** Developer with .NET Framework experience, 1-2 hrs/day
**Goal:** Modern .NET interview readiness + Azure awareness
**Confidence:** HIGH

## Dependency Graph (What Requires What)

```
C# Modern Features (C# 8-14)
    |
    v
OOP Deep Dive + SOLID ────────→ Design Patterns (refactoring.guru)
    |                                    |
    v                                    v
.NET 8 Fundamentals              Refactoring Patterns
    |
    ┌────────────────┐
    |                |
    v                v
ASP.NET Core     EF Core + SQL   Algorithms & Big O
    |                |             (PARALLEL TRACK)
    └────────┬───────┘
             |
             v
     Integration (API + DB + Auth)
             |
             v
     Azure AZ-900 (Cloud Concepts)
             |
             v
     Azure AZ-204 (Developer Skills)
             |
             v
     System Design + Interview Capstone
```

**CRITICAL:** AZ-204 retires July 31, 2026. The developer skills content remains interview-critical regardless of cert status. Monitor for the replacement certification.

## Recommended 6-Phase Structure (~24-28 weeks at 1-2 hrs/day)

### Phase 1: Modern C# + OOP Foundations (Weeks 1-4, ~28-56 hrs)

**Rationale:** The student knows .NET Framework C#. This bridges to modern C# (8-14) while deepening OOP. Everything else depends on this.

**Topics in order:**
1. C# 8-11 features — nullable ref types, records, pattern matching, top-level statements (5 days)
2. C# 12-13 features — primary constructors, collection expressions (3 days)
3. OOP deep dive — encapsulation, inheritance, polymorphism, abstraction (5 days)
4. SOLID principles — one per day with coding challenges (5 days)
5. .NET Framework → Modern .NET translation — hosting model, DI, config, serialization (5 days)
6. Coding challenges applying SOLID (remaining days)

**Theory/Practice split:** 40% theory, 60% coding challenges
**Interview integration:** OOP + SOLID are the #1 most-asked topic. Practice verbal explanations from day 1.
**Checkpoint:** Can the student refactor .NET Framework-style code into modern C# following SOLID?

---

### Phase 2: Design Patterns via refactoring.guru (Weeks 5-9, ~35-70 hrs)

**Rationale:** Patterns require solid OOP. They feed directly into interview discussions AND are needed before ASP.NET Core (middleware = Chain of Responsibility, DI = Factory, etc.).

**Creational Patterns (Week 5):** Factory Method, Abstract Factory, Builder, Singleton, Prototype
**Structural Patterns (Weeks 6-7):** Adapter, Decorator, Facade, Proxy, Composite, Bridge, Flyweight
**Behavioral Patterns (Weeks 8-9):** Observer, Strategy, Command, Chain of Responsibility, Template Method, Iterator, Mediator, State, Memento

**Theory/Practice split:** 50% reading refactoring.guru, 50% implementing in C#
**Interview integration:** "Tell me about a design pattern you've used and why" is standard.
**Checkpoint:** Can the student identify which pattern to use given a scenario? Implement Builder and Strategy from scratch?

---

### Phase 3: Modern .NET Platform + ASP.NET Core (Weeks 10-15, ~42-84 hrs)

**Rationale:** With C# and patterns in place, the student can understand WHY ASP.NET Core is structured the way it is.

**Week 10 — .NET 8 Platform Fundamentals:**
- .NET CLI, project structure, SDK-style csproj
- Built-in Dependency Injection (connects to Singleton/Factory patterns)
- Configuration system (appsettings.json, IOptions)
- Logging (ILogger, structured logging)
- Middleware pipeline concept

**Weeks 11-12 — ASP.NET Core Web API:**
- Minimal APIs (new paradigm, interview-relevant since .NET 6)
- Controller-based APIs (still dominant in enterprise)
- Model binding, validation, filters
- JWT Authentication/Authorization
- OpenAPI/Swagger

**Weeks 13-14 — ASP.NET Core Advanced:**
- Custom middleware (Chain of Responsibility connection)
- Global error handling
- Background services (IHostedService, BackgroundService)
- Caching (in-memory, distributed)
- Health checks

**Week 15 — Testing:**
- xUnit fundamentals
- Unit testing with Moq/NSubstitute
- Integration testing with WebApplicationFactory
- Test patterns (Arrange-Act-Assert)

**Theory/Practice split:** 30% theory, 70% building
**Checkpoint:** Can the student build a REST API with authentication, validation, error handling, and unit tests?

---

### Phase 4: EF Core + SQL Server + Data Patterns (Weeks 16-19, ~28-56 hrs)

**Rationale:** Data access builds on DI and platform knowledge. EF Core is the default .NET ORM. Interviewers always ask about this.

**Week 16 — EF Core Fundamentals:**
- DbContext, entities, migrations CLI
- Code-First approach
- Fluent API vs Data Annotations
- Relationships (1:1, 1:N, N:N)

**Week 17 — EF Core Intermediate:**
- LINQ queries (IQueryable vs IEnumerable)
- Loading: eager (Include), lazy (opt-in), explicit
- Change tracking, AsNoTracking
- Raw SQL and stored procedures
- Performance: N+1 problem, query analysis

**Week 18 — SQL Server:**
- T-SQL (SELECT, JOIN, GROUP BY, subqueries)
- Indexing strategy basics
- Stored procedures and views
- Transaction isolation levels
- Query execution plans

**Week 19 — Data Architecture Patterns:**
- Repository pattern (debate: useful vs overhead with EF Core)
- Unit of Work pattern
- CQRS concept (read/write separation)
- Migration strategies
- Integration: connect API from Phase 3 to real database

**Theory/Practice split:** 40% theory, 60% hands-on SQL + EF Core
**Checkpoint:** Design normalized DB, write efficient EF Core queries, explain N+1, discuss Repository tradeoffs.

---

### Phase 5: Algorithms & Big O — PARALLEL TRACK (Weeks 10-19, 30 min/day)

**IMPORTANT: This runs in parallel with Phases 3-4, NOT sequentially.**

**Rationale:** Algorithms are independent of .NET knowledge. Daily spaced practice beats block study for interview readiness.

**Schedule:** First 30 minutes of each daily session = algorithms. Then switch to main module.

**Big O Notation** (Week 10): Time complexity O(1) through O(n²), space complexity, analyzing loops/recursion
**Arrays & Strings** (Weeks 11-12): Two pointers, sliding window, 3-4 LeetCode Easy/week
**Linked Lists, Stacks, Queues** (Week 13): Implementations + classic problems
**Hash Tables & Sets** (Week 14): Dictionary, HashSet, two-sum pattern
**Trees & Graphs** (Weeks 15-16): Binary trees, BST, BFS/DFS
**Sorting & Searching** (Week 17): Binary search variations, merge sort/quicksort concepts
**Dynamic Programming intro** (Weeks 18-19): Memoization vs tabulation, classic problems

**Theory/Practice split:** 20% concept, 80% problem solving in C#
**Checkpoint:** Solve LeetCode Easy consistently, Medium occasionally. Explain time/space complexity of solutions.

---

### Phase 6: Azure + System Design + Interview Capstone (Weeks 20-28, ~56-84 hrs)

**Azure AZ-900 Prep (Weeks 20-22):**
- Cloud concepts (IaaS, PaaS, SaaS)
- Core services (App Service, Functions, Storage, SQL Database)
- Networking, security, governance
- Cost management, SLAs, practice exams
- **Take AZ-900 exam at end of Week 22**

**Azure Developer Skills — AZ-204 content (Weeks 23-26):**
- App Service deployment
- Azure Functions
- Azure Storage (Blob, Queue, Table)
- Cosmos DB basics
- Key Vault, Managed Identity
- Service Bus / Event Grid
- Container Apps basics
- Application Insights

**System Design for Interviews (Weeks 27-28):**
- Structured approach: requirements → design → components → tradeoffs
- Practice: URL shortener, notification system, chat app
- Scalability patterns: caching, load balancing, message queues
- .NET-specific: Clean Architecture, Vertical Slice, CQRS
- Microservices vs monolith discussion

**Checkpoint:** Pass AZ-900 exam. Whiteboard a system design. Discuss Azure service tradeoffs.

---

## Theory/Practice Interleave Schedule

| Phase | First 30 min | Middle 30-60 min | Last 15-30 min |
|-------|-------------|-----------------|-----------------|
| 1-2 | Read/watch concept | Implement coding challenge | Review + write notes |
| 3-4 | Algorithms (parallel) | Build project feature | Review + commit code |
| 5-6 | Azure hands-on lab | System design practice | Mock interview Q&A |

## Milestone Checkpoints

| Milestone | Week | Criteria | If Not Met |
|-----------|------|----------|------------|
| Modern C# Fluent | 4 | Write idiomatic C# 12 code, explain SOLID | Extend 1 week — do not skip |
| Pattern Confident | 9 | Implement 6+ patterns, explain tradeoffs | Extend 1 week, focus top 6 |
| API Builder | 15 | REST API with auth, tests, clean code | Extend 2 weeks — critical |
| Data Expert | 19 | EF Core + raw SQL + explain N+1 | Extend 1 week |
| AZ-900 Certified | 22 | Pass AZ-900 exam | Study 1 more week, retake |
| Interview Ready | 28 | Mock interview score 7/10+ | Continue practice |

## Scalability Options

| Scenario | Adjustment |
|----------|-----------|
| More than 2 hrs/day | Compress to 20 weeks, add more challenges |
| Less than 1 hr/day | Extend to 36 weeks, top 8 design patterns only |
| Already knows modern C# | Skip Phase 1, start Phase 2, save 4 weeks |
| No Azure cert needed | Replace Azure portion with deeper system design |

## Key Architectural Decisions

1. **Target .NET 8 LTS** for exercises (more likely in production, interviews expect LTS)
2. **refactoring.guru as canonical patterns source** (C# code examples, visual diagrams)
3. **Parallel algorithms track** (spaced practice > block study)
4. **One progressive project** across Phases 3-4 (REST API + database = portfolio piece)
5. **Spanish explanations, English technical terms** (explanations in Spanish, code/APIs in English)
