# Technology Stack: .NET Developer Study Plan

**Domain:** .NET Developer Interview Preparation
**Researched:** 2026-04-30
**Confidence:** HIGH

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
1. **C# modern** (C# 10-14 features)
2. **ASP.NET Core** (Web API, Minimal APIs)
3. **Entity Framework Core** (Code-First, Migrations)
4. **SQL Server** (T-SQL, query optimization)
5. **xUnit + Moq** (testing)
6. **Azure basics** (App Service, Functions, Storage)
7. **Git** (branching, PRs)
8. **Docker basics** (containerization)
9. **Clean Architecture** (project structure)
10. **async/await** (deep understanding)

## Emerging (Awareness Level)
- **Microsoft.Extensions.AI** — First-class AI building blocks for LLMs/embeddings
- **.NET Aspire** — Cloud-native app development framework
- **Blazor** — Full-stack C# web framework (optional for generalista)
