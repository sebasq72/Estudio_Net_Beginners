# Phase 1: Modern C# Foundations — Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-05-01
**Phase:** 01-modern-c-foundations
**Areas discussed:** Formato de ejercicios, Profundidad por tema, Target .NET version, Fuente de challenges

---

## Formato de ejercicios

| Option | Description | Selected |
|--------|-------------|----------|
| Markdown + snippets | Teoría en Markdown con bloques de código C# inline. Simple, portable, sin setup extra. | ✓ |
| Proyecto C# ejecutable | Proyecto .sln con código ejecutable y tests xUnit. Más realista pero requiere setup. | |
| Combinación | Teoría en Markdown + proyecto C# separado con challenges. | |

**User's choice:** Markdown + snippets

---

### Cómo se plantean los challenges

| Option | Description | Selected |
|--------|-------------|----------|
| Enunciado + solución juntos | Challenge + solución comentada/colapsada en el mismo archivo. | ✓ |
| Enunciado solo | Solo enunciado, sin solución de referencia. | |
| Enunciado + solución en archivo separado | challenge.md + solution.md separados. | |

**User's choice:** Enunciado + solución juntos (en el mismo archivo)

---

### Cantidad de challenges por plan

| Option | Description | Selected |
|--------|-------------|----------|
| 3-5 challenges por plan | Manejable en ~1.5 hrs. Uno por sub-tema principal. | ✓ |
| 1-2 challenges por plan | Pocos, más elaborados. Más tiempo de teoría. | |
| 6+ challenges por plan | Alta intensidad práctica. Puede tomar 2+ hrs. | |

**User's choice:** 3-5 challenges por plan

---

## Profundidad por tema

| Option | Description | Selected |
|--------|-------------|----------|
| Jerarquizado por entrevistas | HIGHEST para async/await, LINQ, pattern matching. Awareness para C# 13-14. | ✓ |
| Cobertura uniforme | Todos los features con el mismo nivel de detalle. | |
| Solo must-know | Cubrir solo los de alta frecuencia, saltar los bajos. | |

**User's choice:** Jerarquizado por entrevistas

---

### Profundidad de async/await

| Option | Description | Selected |
|--------|-------------|----------|
| State machine + práctica | Modelo mental de state machine, trampas comunes, challenges. | ✓ |
| Solo uso correcto | Aprender a escribir async/await sin profundizar en el mecanismo interno. | |
| Profundidad total | State machine, thread pool, SynchronizationContext, IAsyncEnumerable, Channels. | |

**User's choice:** State machine + práctica (nivel entrevista sólido)

---

### Cobertura de C# 13-14

| Option | Description | Selected |
|--------|-------------|----------|
| Awareness solamente | ¿Qué es? ¿Cuándo usarlo? Sin challenge profundo. | ✓ |
| Mismo nivel que C# 8-12 | Explicación + challenge de cada feature nuevo. | |
| Omitirlos en Phase 1 | Dejar C# 13-14 para una sesión aparte. | |

**User's choice:** Awareness solamente — suficiente para demostrar que se sigue el ecosistema

---

## Target .NET version

| Option | Description | Selected |
|--------|-------------|----------|
| .NET 8 LTS | Más común en producción actual. EOL Nov 2026. | |
| .NET 10 LTS (current) | LTS actual. Empresas modernas lo adoptarán. Requerido para C# 14. | ✓ |
| Ambos — .NET 8 base + notas .NET 10 | Ejercicios en .NET 8 con notas de cambios en .NET 10. | |

**User's choice:** .NET 10 LTS
**Notes:** Resuelve la inconsistencia entre STATE.md (.NET 8) y CLAUDE.md/STACK.md (.NET 10). El target oficial queda en .NET 10.

---

## Fuente de challenges

| Option | Description | Selected |
|--------|-------------|----------|
| Originales diseñados para el plan | Challenges propios, sin dependencia de plataformas externas. | ✓ |
| Curados con links externos | Links a LeetCode/Exercism. Requiere cuenta. | |
| Combinación | Challenges originales + links a plataformas para práctica extra. | |

**User's choice:** Originales

---

### Estilo de challenges

| Option | Description | Selected |
|--------|-------------|----------|
| Técnicos/abstractos | Enfocados en el concepto exacto. Estilo entrevista técnica .NET. | |
| Contexto real / domain-driven | Enunciados con negocio ficticio. Más entretenidos pero verbosos. | |
| Mezcla 50/50 | ~50% técnicos, ~50% con contexto de negocio. | ✓ |

**User's choice:** Mezcla 50/50 — variedad que mantiene el interés

---

### Dificultad de challenges

| Option | Description | Selected |
|--------|-------------|----------|
| Progresivos dentro del plan | Del más simple (confirma teoría) al más difícil (nivel entrevista). | ✓ |
| Nivel entrevista uniforme | Todos al mismo nivel. Más difícil pero más realista. | |
| Solo confirmación de conceptos | Challenges simples de verificación. La práctica intensa es para LeetCode. | |

**User's choice:** Progresivos dentro del plan

---

## Claude's Discretion

- Estructura interna de cada archivo Markdown
- Sub-temas específicos y su orden dentro de cada plan (guiado por jerarquía de frecuencia)
- Longitud de las explicaciones teóricas (conciso y orientado a práctica)

## Deferred Ideas

- Links opcionales a LeetCode/Exercism como "Práctica Extra" — descartado para Phase 1, posible en fases futuras
- Proyecto C# ejecutable — descartado para Phase 1; posible reconsiderar para Phase 6 (Algoritmos)
- Notas de compatibilidad .NET 8 — posible adición futura si el usuario detecta que empresas preguntan sobre .NET 8 específicamente
