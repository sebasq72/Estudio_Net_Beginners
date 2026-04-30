<!-- GSD:project-start source:PROJECT.md -->
## Project

**Estudio .NET Beginners — Plan de Estudios**

Plan de estudios estructurado para retomar el camino como desarrollador .NET, partiendo desde fundamentos hasta nivel de entrevista. Combina teoria clara con practica tipo coding challenges, cubriendo C#, OOP, design patterns, algoritmos, .NET moderno, Entity Framework, SQL Server y Azure. Dirigido a un desarrollador con experiencia previa en .NET Framework que quiere actualizarse al ecosistema moderno.

**Core Value:** Preparar al estudiante para pasar entrevistas tecnicas de roles .NET generalistas, con fundamentos solidos y practica aplicada en cada tema.

### Constraints

- **Tiempo**: 1-2 horas diarias — cada modulo debe ser digerible en sesiones cortas
- **Formato**: Teoria + practica en cada sesion, no bloques largos solo de teoria
- **Nivel**: No asumir cero conocimiento de programacion, pero si refrescar bases desde el inicio
- **Fuentes**: Buscar contenido actualizado 2024-2025, no material obsoleto de .NET Framework
<!-- GSD:project-end -->

<!-- GSD:stack-start source:research/STACK.md -->
## Technology Stack

## Current .NET Ecosystem (2026)
### Runtime & Language
| Technology | Version | Status | Notes |
|-----------|---------|--------|-------|
| .NET | 10.0 (LTS) | Current stable | Target for study. .NET 8 LTS still in production (EOL Nov 2026) |
| C# | 14 | Current | Field-backed properties, extension blocks, null-conditional assignment |
| .NET 9 | STS | Skip | Short-term support, not recommended for study |
### IDEs & Tools
| Tool | Version | Role |
|------|---------|------|
| Visual Studio 2022 | v17.14 | Primary IDE — Copilot Agent Mode GA, LINQ debugging, MCP support |
| VS Code + C# Dev Kit | Latest | Cross-platform alternative |
| JetBrains Rider | 2026.1 | Alternative premium IDE |
| .NET CLI | 10.0 | Essential — `dotnet new`, `dotnet run`, `dotnet test` |
### Frameworks
| Framework | Version | Use Case |
|-----------|---------|----------|
| ASP.NET Core | 10.0 | Web APIs (Minimal APIs + Controllers), MVC |
| Entity Framework Core | 10.0 | ORM — Code-First, Migrations |
| xUnit | v3 | Testing (community standard) |
| Moq / NSubstitute | Latest | Mocking frameworks |
### Key C# 12-14 Features to Study
- **C# 12:** Primary constructors, collection expressions, alias any type
- **C# 13:** params collections, new lock type, partial properties
- **C# 14:** Field-backed properties, extension blocks, null-conditional assignment
## Testing Ecosystem
- **xUnit** — Community standard, supports Microsoft.Testing.Platform (MTP)
- **MSTest** — Microsoft's framework, also supports MTP
- **NUnit** — Mature alternative
- **TUnit** — Newcomer built entirely on MTP
## Azure Key Services for .NET Developers
| Service | Category | Interview Relevance |
|---------|----------|-------------------|
| App Service | Compute | HIGH — primary deployment target |
| Azure Functions | Serverless | HIGH — common interview topic |
| Azure SQL | Database | HIGH — primary managed DB |
| Cosmos DB | NoSQL | MEDIUM — differentiator |
| Service Bus | Messaging | MEDIUM — async communication |
| Blob Storage | Storage | HIGH — file storage patterns |
| Container Apps | Containers | MEDIUM — modern deployment |
| Key Vault | Security | HIGH — secrets management |
| Entra ID | Identity | HIGH — auth/authz |
| Application Insights | Monitoring | MEDIUM — observability |
## Learning Resources
### Free
- **Microsoft Learn** — Official paths for C#, ASP.NET Core, Azure (FREE)
- **refactoring.guru** — Design patterns with C# examples (FREE)
- **LeetCode** — Coding challenges, C# supported (FREE tier)
- **HackerRank** — SQL + C# challenges (FREE tier)
### Paid
- **Pluralsight** — Comprehensive .NET courses
- **Dometrain** — .NET-focused platform by Nick Chapsas
- **Udemy** — Budget-friendly courses
### Coding Challenge Platforms
| Platform | Strength | C# Support |
|----------|----------|------------|
| LeetCode | Algorithms, interview prep | Full |
| HackerRank | SQL + C# challenges | Full |
| Exercism | C# track, mentored | Full |
| Codewars | Community challenges | Full |
## What NOT to Study (Obsolete)
| Technology | Why Skip |
|-----------|----------|
| .NET Framework | Replaced by modern .NET |
| WCF | Replaced by gRPC |
| Web Forms | No modern equivalent |
| Xamarin | Replaced by .NET MAUI |
| EF6 | Replaced by EF Core |
| BinaryFormatter | Deprecated, security issues |
| Silverlight | Dead |
| Visual Studio for Mac | Discontinued |
## Interview Tech Stack (What Employers Expect)
## Emerging (Awareness Level)
- **Microsoft.Extensions.AI** — First-class AI building blocks for LLMs/embeddings
- **.NET Aspire** — Cloud-native app development framework
- **Blazor** — Full-stack C# web framework (optional for generalista)
<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->
## Conventions

Conventions not yet established. Will populate as patterns emerge during development.
<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->
## Architecture

Architecture not yet mapped. Follow existing patterns found in the codebase.
<!-- GSD:architecture-end -->

<!-- GSD:skills-start source:skills/ -->
## Project Skills

No project skills found. Add skills to any of: `.claude/skills/`, `.agents/skills/`, `.cursor/skills/`, or `.github/skills/` with a `SKILL.md` index file.
<!-- GSD:skills-end -->

<!-- GSD:workflow-start source:GSD defaults -->
## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:
- `/gsd-quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd-debug` for investigation and bug fixing
- `/gsd-execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->



<!-- GSD:profile-start -->
## Developer Profile

> Profile not yet configured. Run `/gsd-profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
