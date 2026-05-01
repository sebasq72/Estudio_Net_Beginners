# Phase 2: OOP + SOLID + Clean Code — Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-05-02
**Phase:** 02-oop-solid-clean-code
**Areas discussed:** Escenarios de challenges, Profundidad herencia C#, Trade-offs SOLID, Abstract vs Interface

---

## Escenarios de challenges

| Option | Description | Selected |
|--------|-------------|----------|
| Dominios realistas | Sistemas de pago, notificaciones, inventario — conecta con entrevistas .NET reales | |
| Clásicos de CS | Jerarquías de animales, figuras geométricas — directos para el concepto | |
| Mix 50/50 | Mantener D-10 de Phase 1: mitad técnico/abstracto, mitad contexto de negocio | ✓ |

**User's choice:** Mix 50/50 (como Phase 1)
**Notes:** Mantener consistencia con la decisión D-10 establecida en Phase 1.

---

## Profundidad herencia C#

| Option | Description | Selected |
|--------|-------------|----------|
| Concepto + trampas | virtual/override/new — diferencia entre ocultar y reemplazar. Incluye sealed. | ✓ |
| Solo concepto básico | Herencia como pilar sin profundizar en mecánicas. Más rápido pero más vulnerable en entrevistas. | |

**User's choice:** Concepto + trampas
**Notes:** Cubrir la trampa del `new` keyword (oculta vs `override` que reemplaza) como pregunta típica de entrevista.

---

## Trade-offs SOLID

| Option | Description | Selected |
|--------|-------------|----------|
| Sí, incluir anti-patterns | Cada principio termina con "cuándo va demasiado lejos" — prepara para debate en entrevistas | ✓ |
| Solo aplicación correcta | Enseñar aplicación correcta sin cubrir abusos. Más simple. | |

**User's choice:** Sí, incluir anti-patterns
**Notes:** El estudiante debe poder responder "¿cuándo NO aplicarías [principio]?" con argumento concreto.

---

## Abstract vs Interface

| Option | Description | Selected |
|--------|-------------|----------|
| Decision tree + refactoring | Árbol visual primero, luego challenge de código mal aplicado para corregir | ✓ |
| Solo decision tree | Árbol de decisión como referencia, sin challenge práctico | |
| Solo refactoring challenge | Directo al código sin guía previa — más desafiante pero sin referencia | |

**User's choice:** Decision tree + refactoring
**Notes:** Incluir C# 8 default implementations como parte del árbol de decisión.

---

## Claude's Discretion

- Dominios específicos de cada challenge (dentro del mix 50/50)
- Estructura interna de cada sección de plan
- Longitud de explicaciones teóricas
- Ejemplo concreto de anti-pattern para cada principio SOLID

## Deferred Ideas

- Explicit interface implementation profunda → fuera de scope Phase 2
- Covarianza/contravarianza genérica → posible sección "Práctica Extra" si el estudiante lo pide
- Links a LeetCode/Exercism → mismo criterio que Phase 1, challenges originales primero
