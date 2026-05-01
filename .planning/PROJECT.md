# Estudio .NET Beginners — Plan de Estudios

## What This Is

Plan de estudios estructurado para retomar el camino como desarrollador .NET, partiendo desde fundamentos hasta nivel de entrevista. Combina teoria clara con practica tipo coding challenges, cubriendo C#, OOP, design patterns, algoritmos, .NET moderno, Entity Framework, SQL Server y Azure. Dirigido a un desarrollador con experiencia previa en .NET Framework que quiere actualizarse al ecosistema moderno.

## Core Value

Preparar al estudiante para pasar entrevistas tecnicas de roles .NET generalistas, con fundamentos solidos y practica aplicada en cada tema.

## Requirements

### Validated

- [x] C# 8-14 features con tabla de traduccion .NET Framework → moderno — Validated in Phase 01: Modern C# Foundations (CSHA-01, CSHA-02)
- [x] Async/await profundo: state machine, Task vs ValueTask, CancellationToken, trampas — Validated in Phase 01: Modern C# Foundations (CSHA-03, CSHA-04)
- [x] LINQ, generics, delegates y programacion funcional en C# — Validated in Phase 01: Modern C# Foundations (CSHA-05, CSHA-06)
- [x] Formato: teoria en espanol + snippets compilables + 5 challenges progresivos con soluciones colapsables por tema — Validated in Phase 01

### Active

- [ ] Modulo de fundamentos C# y OOP (clases, herencia, polimorfismo, interfaces, SOLID)
- [ ] Modulo de .NET moderno (.NET 8/9, minimal APIs, dependency injection, middleware)
- [ ] Modulo de design patterns (creational, structural, behavioral — fuente: refactoring.guru + otras)
- [ ] Modulo de logica de programacion y algoritmos esenciales (sorting, searching, trees, graphs, dynamic programming)
- [ ] Modulo de Big O notation y analisis de complejidad
- [ ] Modulo de mejores practicas (clean code, SOLID, testing, code review patterns)
- [ ] Modulo de ASP.NET Core (Web API, MVC, autenticacion, autorizacion)
- [ ] Modulo de Entity Framework Core y SQL Server (CRUD, migrations, queries, optimizacion)
- [ ] Modulo de Azure fundamentos (AZ-900: conceptos cloud, servicios principales)
- [ ] Modulo de Azure developer (AZ-204: App Services, Functions, Storage, CosmosDB, Service Bus)
- [ ] Seccion de preparacion de entrevistas (preguntas frecuentes, mock interviews, coding challenges estilo LeetCode)
- [ ] Cada modulo: teoria concisa + coding challenges practicos
- [ ] Material en formato mixto: explicaciones en espanol, terminos tecnicos y recursos en ingles
- [ ] Estructura progresiva: 1-2 horas diarias de dedicacion

### Out of Scope

- Frontend frameworks (React, Angular, Blazor) — enfoque es generalista backend-heavy, no fullstack
- DevOps/CI-CD pipelines — solo conceptos Azure, no infraestructura completa
- Certificaciones formales — el objetivo es pasar entrevistas, no certificarse (aunque el contenido se alinea con AZ-900/AZ-204)
- Mobile development (Xamarin/MAUI) — fuera del scope generalista

## Context

- Desarrollador con experiencia previa en .NET Framework clasico, necesita puente hacia .NET moderno
- Formato preferido: coding challenges (estilo LeetCode/HackerRank) para la parte practica
- Disponibilidad: 1-2 horas diarias
- Idioma: mixto (explicaciones en espanol, terminos y recursos en ingles)
- Enfoque principal: preparacion para entrevistas tecnicas .NET generalistas
- Incluir SQL Server y Entity Framework Core con profundidad
- Azure progresivo: fundamentos (AZ-900) → developer (AZ-204)
- Design patterns usando refactoring.guru como fuente principal + otras fuentes complementarias

## Constraints

- **Tiempo**: 1-2 horas diarias — cada modulo debe ser digerible en sesiones cortas
- **Formato**: Teoria + practica en cada sesion, no bloques largos solo de teoria
- **Nivel**: No asumir cero conocimiento de programacion, pero si refrescar bases desde el inicio
- **Fuentes**: Buscar contenido actualizado 2024-2025, no material obsoleto de .NET Framework

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Coding challenges como practica principal | El usuario prefiere estilo LeetCode/HackerRank, ademas prepara directamente para entrevistas tecnicas | — Pending |
| Azure progresivo (AZ-900 → AZ-204) | Cubrir ambos niveles permite prepararse para entrevistas con distintos niveles de exigencia cloud | — Pending |
| Idioma mixto | Maximiza comprension (espanol) mientras familiariza con terminologia real de entrevistas (ingles) | — Pending |
| EF Core + SQL Server con profundidad | Tema frecuente en entrevistas .NET, el usuario quiere cubrirlo a fondo | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-30 after initialization*
