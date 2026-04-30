# Feature Landscape: .NET Developer Study Topics

**Domain:** .NET Developer Interview Preparation
**Researched:** 2026-04-30
**Confidence:** HIGH

## Table Stakes (Must Know — Fail Without These)

### Tier 1: Core C# (45-65 hours)
| Topic | Study Time | Interview Frequency | Difficulty |
|-------|-----------|-------------------|------------|
| C# fundamentals (types, collections, LINQ) | 10-15 hrs | EVERY interview | Low-Medium |
| Modern C# features (C# 8-14) | 8-12 hrs | HIGH | Medium |
| async/await deep dive | 8-10 hrs | HIGHEST — #1 most asked | High |
| Nullable reference types | 3-5 hrs | HIGH | Medium |
| Records, pattern matching | 5-8 hrs | HIGH | Medium |
| Exception handling patterns | 3-5 hrs | MEDIUM | Low |
| Generics and constraints | 4-5 hrs | HIGH | Medium |
| Delegates, events, lambdas | 4-5 hrs | HIGH | Medium |

### Tier 2: OOP & Design (27-38 hours)
| Topic | Study Time | Interview Frequency | Difficulty |
|-------|-----------|-------------------|------------|
| OOP principles (4 pillars) | 5-8 hrs | EVERY interview | Medium |
| SOLID principles | 8-10 hrs | EVERY interview | Medium-High |
| Design patterns (top 10) | 10-15 hrs | HIGH | Medium-High |
| Dependency Injection | 4-5 hrs | EVERY interview | Medium |

### Tier 3: ASP.NET Core (30-41 hours)
| Topic | Study Time | Interview Frequency | Difficulty |
|-------|-----------|-------------------|------------|
| Minimal APIs + Controllers | 8-10 hrs | EVERY .NET interview | Medium |
| Middleware pipeline | 5-7 hrs | HIGH | Medium |
| Authentication/Authorization (JWT) | 5-7 hrs | HIGH | High |
| Configuration & Options pattern | 3-4 hrs | MEDIUM | Low |
| Error handling & logging | 3-4 hrs | MEDIUM | Low |
| IHttpClientFactory | 3-4 hrs | MEDIUM | Medium |
| Background services | 3-5 hrs | MEDIUM | Medium |

### Tier 4: Data Access (27-38 hours)
| Topic | Study Time | Interview Frequency | Difficulty |
|-------|-----------|-------------------|------------|
| EF Core fundamentals | 8-10 hrs | HIGH | Medium |
| LINQ to EF (IQueryable) | 5-7 hrs | HIGH | Medium-High |
| SQL Server (T-SQL) | 8-10 hrs | HIGH | Medium |
| N+1 problem & optimization | 3-5 hrs | HIGH — common trap | Medium |
| Migrations workflow | 3-5 hrs | MEDIUM | Low |

### Tier 5: Algorithms & DS (37-52 hours)
| Topic | Study Time | Interview Frequency | Difficulty |
|-------|-----------|-------------------|------------|
| Big O notation | 5-7 hrs | EVERY coding interview | Medium |
| Arrays, strings, hash tables | 8-12 hrs | HIGH | Medium |
| Linked lists, stacks, queues | 5-7 hrs | HIGH | Medium |
| Trees, graphs (BFS/DFS) | 8-10 hrs | MEDIUM-HIGH | High |
| Sorting & searching | 5-7 hrs | MEDIUM | Medium |
| Dynamic programming intro | 6-9 hrs | MEDIUM (senior) | High |

### Tier 6: Testing (14-20 hours)
| Topic | Study Time | Interview Frequency | Difficulty |
|-------|-----------|-------------------|------------|
| xUnit fundamentals | 4-5 hrs | MEDIUM-HIGH | Low |
| Mocking (Moq/NSubstitute) | 4-5 hrs | MEDIUM | Medium |
| Integration testing | 3-5 hrs | MEDIUM | Medium |
| Test patterns (AAA, naming) | 3-5 hrs | MEDIUM | Low |

**Total table stakes: ~180-254 hours (~13-16 weeks at 1.5 hrs/day)**

## Differentiators (Set You Apart)

| Topic | Study Time | Interview Impact | When to Study |
|-------|-----------|-----------------|---------------|
| Clean Architecture | 8-12 hrs | HIGH for senior | After ASP.NET Core |
| Azure AZ-900 concepts | 15-20 hrs | MEDIUM-HIGH | After core topics |
| Azure AZ-204 developer | 25-35 hrs | HIGH for cloud roles | After AZ-900 |
| Docker basics | 5-8 hrs | MEDIUM | After ASP.NET Core |
| System Design | 15-20 hrs | HIGH for senior | After all core |
| CQRS + MediatR | 5-8 hrs | MEDIUM | After EF Core |
| gRPC | 3-5 hrs | LOW-MEDIUM | Optional |
| SignalR | 3-5 hrs | LOW-MEDIUM | Optional |

## Anti-Features (Do NOT Study for Generalista Interviews)

| Topic | Why Skip |
|-------|----------|
| WPF / WinForms | Desktop-only roles, not generalista |
| Xamarin / .NET MAUI | Mobile-specific |
| F# / VB.NET | Niche languages |
| WCF | Replaced by gRPC, dead tech |
| Web Forms | No modern equivalent |
| Blazor (deep) | Not yet standard in interviews; awareness only |
| Kubernetes (deep) | DevOps role territory |
| Microservices (deep) | Over-engineering for study; know concepts only |

## Dependencies Between Topics

```
C# Fundamentals
    ├── Modern C# (C# 8-14)
    │   └── Records, Pattern Matching
    ├── async/await
    │   └── IHttpClientFactory, Background Services
    ├── LINQ
    │   └── EF Core (IQueryable)
    ├── OOP
    │   ├── SOLID
    │   │   ├── Design Patterns
    │   │   └── Dependency Injection
    │   │       └── ASP.NET Core
    │   │           ├── Middleware
    │   │           ├── Auth (JWT)
    │   │           └── Testing (WebApplicationFactory)
    │   └── EF Core
    │       └── SQL Server
    └── Algorithms & DS (independent track)
        └── Big O
```

## Key Insight for Returning .NET Framework Developers

The biggest knowledge gap is **ASP.NET Core**: middleware pipeline, built-in DI container, Minimal APIs, and modern authentication are entirely new concepts from the .NET Framework world. This deserves dedicated study time.

**async/await is the single most-asked topic.** Deep understanding (not just "add async/await keywords") is critical for passing technical screens.
