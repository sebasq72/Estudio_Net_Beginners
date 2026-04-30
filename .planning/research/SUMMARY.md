# Project Research Summary

**Project:** Estudio .NET Beginners -- Plan de Estudios
**Domain:** Developer Study Plan / Interview Preparation
**Researched:** 2026-04-30
**Confidence:** HIGH

## Executive Summary

This is a structured study plan to bring a returning .NET Framework developer to modern .NET interview readiness in 24-28 weeks at 1-2 hours/day. The ecosystem has shifted dramatically: .NET 10 LTS is current, C# has evolved through 7 major versions (C# 8-14), ASP.NET Core replaced the entire web stack, EF Core is a ground-up rewrite of EF6, and Azure cloud skills are now expected. The student's prior .NET Framework experience is both an asset (conceptual foundation) and a liability (outdated patterns that are now anti-patterns in interviews).

The recommended approach is a 6-phase progressive structure that respects dependency chains: Modern C# and OOP first (everything depends on this), then Design Patterns (needed to understand ASP.NET Core internals), then ASP.NET Core + EF Core/SQL Server (the interview core), with algorithms running as a parallel daily track from Phase 3 onward, and Azure + System Design as the capstone. Each phase combines theory with hands-on coding challenges, targeting a 30-40% theory / 60-70% practice split.

The top risks are: (1) the student defaults to .NET Framework patterns in interviews (immediate disqualifier), (2) memorizing async/await syntax without understanding the state machine underneath (the #1 most-asked interview topic), and (3) neglecting system design, which is a hard gate for mid/senior roles. Mitigation: build on .NET 8 from day one, practice "3-layer depth" for every concept (definition, mechanism, edge case), and introduce system design thinking from Phase 2 onward.

## Key Findings

### Recommended Stack

Target .NET 8 LTS for all exercises (production standard, interview expectation). Use VS 2022 or VS Code + C# Dev Kit. Study C# 12-14 features but write exercises in C# 12 (LTS-aligned). xUnit + Moq for testing. LeetCode and HackerRank for coding challenges.

**Core technologies:**
- **.NET 8 LTS / C# 12**: Runtime and language target -- LTS is what employers run in production
- **ASP.NET Core 8**: Web API framework (Minimal APIs + Controllers) -- every .NET interview tests this
- **EF Core 8 + SQL Server**: ORM + database -- standard data access stack, heavily tested in interviews
- **xUnit + Moq**: Testing framework -- community standard, expected knowledge for mid+ roles
- **Azure (App Service, Functions, Storage, Key Vault)**: Cloud platform -- differentiator, increasingly expected

**What NOT to study:** .NET Framework, WCF, Web Forms, EF6, Xamarin, Silverlight, BinaryFormatter.

### Expected Features (Study Topics)

**Must have (table stakes -- ~180-254 hours):**
- C# fundamentals + modern features (C# 8-14) -- tested in every interview
- async/await deep understanding -- the single most-asked topic
- OOP + SOLID principles -- tested in every interview
- Design patterns (top 10) -- standard interview question
- ASP.NET Core (Minimal APIs + Controllers, middleware, JWT auth) -- core of .NET interviews
- EF Core + SQL Server (CRUD, N+1, IQueryable vs IEnumerable) -- always asked
- Algorithms and Big O (arrays, trees, hash tables, sorting) -- coding interview gate
- Testing basics (xUnit, mocking, AAA pattern) -- expected for mid+ roles

**Should have (differentiators):**
- Clean Architecture -- high impact for senior roles
- Azure AZ-900 concepts -- medium-high impact
- Azure AZ-204 developer skills -- high impact for cloud roles
- Docker basics -- increasingly expected
- System Design -- hard gate for senior interviews
- CQRS + MediatR -- bonus points

**Defer (optional/awareness only):**
- gRPC, SignalR -- low interview frequency
- Blazor -- not yet standard in interviews
- .NET Aspire, Microsoft.Extensions.AI -- emerging, awareness level
- Microservices deep dive -- over-engineering for study purposes

### Architecture Approach

The study plan follows a dependency-driven progressive structure where each phase builds on the previous one. The critical insight is that algorithms should run as a parallel daily track (30 min/day) alongside the main .NET modules, since algorithms are independent of .NET knowledge and benefit from spaced repetition rather than block study. One progressive project spans Phases 3-4 (REST API + database) to serve as both learning vehicle and portfolio piece.

**Major learning components:**
1. **Foundation Layer** (C#, OOP, SOLID) -- prerequisite for everything; bridges .NET Framework gap
2. **Pattern Layer** (Design Patterns) -- connects OOP theory to ASP.NET Core internals
3. **Application Layer** (ASP.NET Core + EF Core + SQL) -- the interview core; build a real API with database
4. **Parallel Algorithms Track** -- daily spaced practice, independent of main track
5. **Cloud + Capstone** (Azure, System Design, Mock Interviews) -- differentiators and interview simulation

### Critical Pitfalls

1. **.NET Framework anti-patterns in interviews** -- Build on .NET 8 from day one; never use Global.asax, Web.config, HttpContext.Current, System.Web patterns. Address in Phase 1.
2. **Shallow async/await knowledge** -- Practice 3-layer depth: what it does, how the state machine works, what breaks (deadlocks, async void, .Result). Deliberately create and fix failure scenarios.
3. **Assuming EF6 transfers to EF Core** -- Treat EF Core as a new ORM. No lazy loading by default, no EDMX, CLI-driven migrations, AsNoTracking() matters.
4. **Writing dated C# in live coding** -- Prioritize: nullable refs, records, pattern matching, primary constructors, collection expressions. Use modern idioms from the first exercise.
5. **No timed practice** -- Use a timer from Phase 2 onward. Do 2+ mock interviews before real ones. Practice explaining while coding.

## Implications for Roadmap

Based on research, suggested 6-phase structure:

### Phase 1: Modern C# + OOP Foundations (Weeks 1-4)
**Rationale:** Everything depends on this. The student knows .NET Framework C#; this bridges to C# 8-14 and deepens OOP/SOLID. Must come first.
**Delivers:** Ability to write idiomatic modern C#, explain SOLID, refactor legacy patterns.
**Addresses:** C# fundamentals, modern features, OOP, SOLID, .NET Framework-to-modern translation.
**Avoids:** Pitfall 1 (Framework anti-patterns), Pitfall 3 (dated C# syntax).

### Phase 2: Design Patterns (Weeks 5-9)
**Rationale:** Requires solid OOP from Phase 1. Patterns are needed to understand ASP.NET Core internals (middleware, DI) and are standard interview questions.
**Delivers:** Ability to identify, implement, and discuss 15+ design patterns. Uses refactoring.guru as primary source.
**Addresses:** Design patterns (creational, structural, behavioral).
**Avoids:** Pitfall 2 (surface-level memorization) -- implement each pattern, don't just read.

### Phase 3: ASP.NET Core + .NET Platform (Weeks 10-15)
**Rationale:** With C# and patterns mastered, the student can understand WHY ASP.NET Core is structured the way it is. This is the largest knowledge gap for Framework developers.
**Delivers:** A working REST API with JWT auth, middleware, testing, error handling -- portfolio piece.
**Addresses:** Minimal APIs, Controllers, middleware, DI, JWT auth, xUnit, Moq.
**Avoids:** Pitfall 1 (Framework patterns), Pitfall 9 (skipping testing).

### Phase 4: EF Core + SQL Server (Weeks 16-19)
**Rationale:** Data access builds on DI and platform knowledge from Phase 3. Connects API to real database.
**Delivers:** Database-backed API with EF Core, SQL skills, understanding of N+1 and optimization.
**Addresses:** EF Core, LINQ/IQueryable, T-SQL, Repository pattern debate, CQRS concept.
**Avoids:** Pitfall 4 (EF6 assumptions), Pitfall 2 (shallow IQueryable understanding).

### Phase 5: Algorithms + Big O (PARALLEL, Weeks 10-19)
**Rationale:** Runs alongside Phases 3-4 as a daily 30-minute track. Independent of .NET knowledge. Spaced practice beats block study for algorithm retention.
**Delivers:** Ability to solve LeetCode Easy consistently, Medium occasionally. Big O fluency.
**Addresses:** Arrays, strings, hash tables, trees, graphs, sorting, DP intro.
**Avoids:** Pitfall 7 (non-C# style solutions) -- always solve in C# with LINQ and framework collections.

### Phase 6: Azure + System Design + Interview Capstone (Weeks 20-28)
**Rationale:** Cloud and system design are differentiators that build on all previous phases. Capstone integrates everything.
**Delivers:** AZ-900 exam readiness, AZ-204 topic coverage, system design practice, mock interview readiness.
**Addresses:** Azure services, system design, Clean Architecture, mock interviews.
**Avoids:** Pitfall 5 (neglecting system design), Pitfall 11 (no timed practice).
**Note:** AZ-204 certification retires July 31, 2026. Content remains interview-relevant regardless. Monitor for replacement.

### Phase Ordering Rationale

- Dependencies are strict: C# -> OOP/SOLID -> Patterns -> ASP.NET Core -> EF Core -> Azure
- Algorithms are the one independent track and benefit from daily spaced practice, hence parallel execution
- Azure comes last because it builds on understanding of App Services, APIs, and databases
- System Design is the capstone because it requires knowledge of all previous layers
- Testing is embedded in Phase 3 (not isolated) because it should be practiced in context

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 2 (Design Patterns):** Need to curate which patterns get full implementation vs. awareness-only. Top 10 for interviews vs. all 23 from GoF.
- **Phase 6 (Azure):** AZ-204 retirement in July 2026 -- need to check if replacement cert affects study content. Also need to curate which Azure services to lab vs. read-only.

Phases with standard patterns (skip research-phase):
- **Phase 1 (Modern C#):** Well-documented; Microsoft Learn has complete paths.
- **Phase 3 (ASP.NET Core):** Extremely well-documented; official tutorials are high quality.
- **Phase 4 (EF Core):** Well-documented; Microsoft Learn + EF Core docs are comprehensive.
- **Phase 5 (Algorithms):** Standard curriculum; LeetCode patterns are well-established.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | .NET versioning, tooling, and ecosystem are well-documented. Clear LTS strategy. |
| Features | HIGH | Interview topic frequency is well-established through community consensus and job postings. |
| Architecture | HIGH | Dependency chain is clear and well-validated. Parallel algorithms track is a proven study strategy. |
| Pitfalls | HIGH | .NET Framework-to-modern gap is extensively documented. Common interview traps are well-known. |

**Overall confidence:** HIGH

### Gaps to Address

- **AZ-204 retirement (July 2026):** Monitor Microsoft announcements for the replacement certification. Study content remains valid but exam strategy may shift.
- **C# 14 stability:** Some C# 14 features may be preview-only at time of study. Stick to C# 12 for exercises, C# 13-14 for awareness.
- **Interview format variation:** Research covers technical screens and coding challenges well, but behavioral interview prep and company-specific formats are not addressed. Consider adding a brief behavioral prep section.
- **Time estimation accuracy:** Hour estimates (180-254 hrs for table stakes) assume focused study. Actual pace depends on student's retention of prior .NET Framework knowledge -- may be faster or slower. Build in checkpoint-based flexibility.

## Sources

### Primary (HIGH confidence)
- Microsoft Learn official documentation -- .NET 8/10, ASP.NET Core, EF Core, Azure
- refactoring.guru -- Design patterns with C# examples
- Microsoft .NET blog -- C# 12-14 feature announcements, .NET versioning

### Secondary (MEDIUM confidence)
- LeetCode/HackerRank -- Algorithm problem categorization and difficulty calibration
- Community consensus (Reddit r/dotnet, Stack Overflow, .NET conf talks) -- Interview topic frequency
- Nick Chapsas / Dometrain -- Modern .NET best practices

### Tertiary (LOW confidence)
- AZ-204 retirement timeline -- Based on Microsoft announcement, but replacement cert details unknown
- C# 14 feature finalization -- Some features may change before GA

---
*Research completed: 2026-04-30*
*Ready for roadmap: yes*
