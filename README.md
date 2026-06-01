# AI-Native System Specification Standard (ANSS)

**by Artem Kholomyanskiy — Version 1.3 (2026)**

> A modern specification standard for building systems with AI agents. The evolution of classic TDD/SRS for the age of multi-agent development.

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Version](https://img.shields.io/badge/version-1.3-blue.svg)](https://github.com/Kholomyanskiy/anss-standard/releases)

---

## What is ANSS?

ANSS is not just a TZ/SRS template. It is an **operating system for a project** — for humans and AI agents simultaneously.

Most specifications are written for humans. ANSS is designed so that **AI agents can read it and work predictably**.

Key innovations:
- **INVARIANTS** — absolute constraints that agents can never violate
- **Architectural Principles** — a compass for agents when requirements are unclear
- **Agent Review** — mandatory review of the specification before writing code
- **Change Specification** — a separate format for modifying existing systems
- **Living Specification** — the spec evolves with the code, not separately

---

## Repository Structure

```
anss-standard/
├── README.md                          # This file (English)
├── README.ru.md                       # Russian version
├── LICENSE                            # CC BY-NC-SA 4.0
│
├── ANSS-Standard-v1.3.en.md          # Full standard (English)
├── ANSS-Standard-v1.3.ru.md          # Full standard (Russian)
│
├── ANSS-Core-Template.en.md          # Working template — Core (English)
├── ANSS-Core-Template.ru.md          # Working template — Core (Russian)
│
└── examples/                         # Filled examples (coming soon)
    └── example-saas-project.md
```

---

## Three Levels

| Level | Pages | For |
|---|---|---|
| **ANSS-Core** | 15–20 | 80% of projects: bots, SaaS, APIs, automations |
| **ANSS-Extended** | 40–60 | Security, Infra, Compliance, Testing in detail |
| **ANSS-Enterprise** | Full standard | Banks, enterprise, AI platforms, large SaaS |

---

## How to Use

### For humans
1. Open `ANSS-Core-Template` for your language
2. Fill in sections 0–2 (context, glossary, invariants, principles)
3. Work with an AI agent on sections 3–14
4. The agent performs **Agent Review** (section 14) before writing code

### For AI agents
```
Priority order for reading:
1. INVARIANTS (Section 2.5) — never violate these
2. ARCHITECTURAL PRINCIPLES (Section 2.6) — compass for uncertainty
3. GLOSSARY (Section 2.1) — the language of the project
4. All other sections

Rule: if something is unclear — STOP AND ASK. Do not guess.
```

### Quick Start (5 minutes)
Fill in the minimum required:
- What are we building and for whom (1.1)
- Problem and goals (1.2)
- 3–5 invariants (2.5)
- 3–5 user stories with Acceptance Criteria (3.2)
- Tech stack (2.4)

---

## Key Concepts

### Invariants
Absolute constraints that cannot be violated under any circumstances.

```
INV-001: Atomicity of financial operations
Cannot change balance outside a transaction. Partial execution is unacceptable.

INV-002: API backward compatibility
Cannot remove or rename fields in public API responses.

INV-003: Immutability of audit log
audit_log records cannot be modified or deleted. Append only.
```

### Architectural Principles
A guide for making decisions when requirements are insufficient.

```
AP-001: Simple > Clever
AP-002: Monolith First
AP-003: Buy Before Build
AP-004: Human Override Always Available
AP-005: No Vendor Lock-In Without Justification
AP-006: Fail Loud
AP-007: Data Outlives Code
AP-008: Boring Technology Wins
AP-009: YAGNI
AP-010: Reversibility Over Efficiency
```

### Agent Review
Mandatory step before writing code. The agent reviews the specification — not the code.

```
Before starting implementation, the agent must find:
1. Contradictions between sections
2. Unspecified situations that will definitely arise
3. Missing edge cases
4. Missing Acceptance Criteria
5. Conflicts with invariants
6. YAGNI violations

If > 3 problems found → stop and report BEFORE writing code.
```

---

## Based on

- GOST 34.602 (Russian standard for automated systems)
- IEEE 830 / ISO/IEC 29148 (Software requirements)
- ISO/IEC 25010 (Software quality)
- ISO/IEC 42001 / 42005 (AI management systems)
- EU AI Act
- GDPR / OWASP / C4 Model
- Shape Up, ADR, SDD methodology

---

## Versioning

| Version | Date | What changed |
|---|---|---|
| 1.3 | 2026-06 | Invariants, Architectural Principles, Agent Review, Change Spec, Decision Log, Fitness Functions, Economics |
| 1.2 | 2026-06 | Three-layer structure [D/E/A], Tailored Checklist, DR, FinOps |
| 1.1 | 2026-06 | Observability, Performance Budget, Supply Chain Security |
| 1.0 | 2026-06 | Initial release |

---

## License

This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png

**You may:**
- Use freely for personal and non-commercial purposes
- Share and adapt with attribution
- Use within companies for internal projects

**You may not:**
- Sell or include in commercial products without a separate license
- Remove author attribution

**Commercial license:** contact kholomyanskiy@gmail.com

---

## Author

**Artem Kholomyanskiy** — AI automation specialist, founder of EVAI Consulting.

Specializes in AI-First methodology, specification-driven development, and building autonomous AI agent systems.

- LinkedIn: [linkedin.com/in/kholomyanskiy](https://linkedin.com/in/kholomyanskiy)
- GitHub: [github.com/Kholomyanskiy](https://github.com/Kholomyanskiy)

---

*Next planned revision: in 6 months*
*Standard actively used in production projects*

