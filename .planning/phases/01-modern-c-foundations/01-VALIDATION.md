---
phase: 1
slug: modern-c-foundations
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-05-01
---

# Phase 1 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual content review — output is educational Markdown |
| **Config file** | none |
| **Quick run command** | `ls content/phase-01/` (verify files exist) |
| **Full suite command** | Manual checklist review per plan |
| **Estimated runtime** | ~2 minutes manual review |

---

## Sampling Rate

- **After every task commit:** Verify Markdown file exists and has expected sections
- **After every plan wave:** Full content checklist review
- **Before `/gsd-verify-work`:** All 3 plan Markdown files complete and self-contained
- **Max feedback latency:** N/A (educational content, no automated tests)

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| 1-01-01 | 01 | 1 | CSHA-01 | — | N/A | manual | `ls content/phase-01/01-01-*.md` | ❌ W0 | ⬜ pending |
| 1-01-02 | 01 | 1 | CSHA-02 | — | N/A | manual | `ls content/phase-01/01-01-*.md` | ❌ W0 | ⬜ pending |
| 1-02-01 | 02 | 1 | CSHA-03 | — | N/A | manual | `ls content/phase-01/01-02-*.md` | ❌ W0 | ⬜ pending |
| 1-03-01 | 03 | 1 | CSHA-04 | — | N/A | manual | `ls content/phase-01/01-03-*.md` | ❌ W0 | ⬜ pending |
| 1-03-02 | 03 | 1 | CSHA-05 | — | N/A | manual | `ls content/phase-01/01-03-*.md` | ❌ W0 | ⬜ pending |
| 1-03-03 | 03 | 1 | CSHA-06 | — | N/A | manual | `ls content/phase-01/01-03-*.md` | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `content/phase-01/` directory exists
- [ ] Content file naming convention established (`01-01-csharp-moderno.md`, etc.)

*Existing infrastructure: none — Wave 0 creates the content directory.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Teoria clara y correcta | CSHA-01, CSHA-02 | Educational content — no automated correctness check | Read section headers and verify coverage matches CONTEXT.md decisions |
| Challenges son resolubles | All CSHA | Pedagogical judgment required | Attempt each challenge and verify soluciones son correctas y completas |
| Formato `<details><summary>` | CSHA-01 to CSHA-06 | Markdown rendering | Open in VS Code Preview or GitHub and verify collapsible sections work |
| Tiempo estimado ~1.5h | CSHA-01 to CSHA-06 | Requires human time estimation | Count challenges and theory depth; adjust if > 600 lines per plan |

---

## Validation Sign-Off

- [ ] All 3 plan Markdown files created: 01-01, 01-02, 01-03
- [ ] Each file has: Teoria, Challenges, Soluciones (collapsible)
- [ ] All CSHA-01 to CSHA-06 requirements addressed across the 3 plans
- [ ] Estimated session time ~1.5h per plan file
- [ ] No external dependencies (self-contained Markdown)
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
