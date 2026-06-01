# AI-Native System Specification Standard (ANSS)

**by Artem Kholomyanskiy — Version 1.3 (2026)**

> A specification standard for systems built with AI agents.  
> Not just a template — an operating system for a project that humans and agents read simultaneously.

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Version](https://img.shields.io/badge/version-1.3-blue.svg)](https://github.com/Kholomyanskiy/anss-standard/releases)

[Русский](README.ru.md) | English

---

## The Problem

Classic specs are written for humans. When an AI agent reads them, it guesses, hallucinates, and breaks invariants.

ANSS gives agents the same precision it gives developers.

---

## Three Layers

Every section in ANSS is marked with a layer:

```
[D] Domain        — WHAT to build   → Product Owner, PM
[E] Engineering   — HOW to build    → Developer, Architect
[A] Agent         — HOW agent does it → AI agents (read this first)
```

---

## How It Works

```
You write:                    Agent reads:
──────────                    ────────────
[D] Context (1.1)             INVARIANTS first   ← cannot be violated
[D] Glossary (2.1)            PRINCIPLES second  ← compass for uncertainty
[A] Invariants (2.5)    →     GLOSSARY third     ← language of the project
[A] Principles (2.6)          Everything else
[D] User Stories (3.x)               ↓
[E] Architecture (5.x)         Agent Review      ← spec review before code
                                     ↓
                               Implementation ✓
```

---

## Three Levels

```
┌─────────────────────────────────────────────────────┐
│  CORE        15–20 pages   80% of projects           │
│              Bots, SaaS, APIs, automations           │
├─────────────────────────────────────────────────────┤
│  EXTENDED    40–60 pages   Security, Compliance,     │
│              Infra, detailed testing                 │
├─────────────────────────────────────────────────────┤
│  ENTERPRISE  Full standard Banks, enterprise,        │
│              AI platforms, regulated industries      │
└─────────────────────────────────────────────────────┘
```

**Start with Core.** Upgrade only when you actually need it.

---

## Key Concepts

**Invariants** — absolute constraints agents can never violate:
```
INV-001: No external npm packages
Cannot: add require() of npm modules
Reason: app must run without npm install
Check: no node_modules imports in server.js
```

**Architectural Principles** — compass when requirements are unclear:
```
AP-001: Simple > Clever       AP-006: Fail Loud
AP-002: Monolith First        AP-007: Data Outlives Code
AP-003: Buy Before Build      AP-008: Boring Technology Wins
AP-004: Human Override Always AP-009: YAGNI
AP-005: No Vendor Lock-In     AP-010: Reversibility Over Efficiency
```

**Agent Review** — mandatory before writing code:
```
Agent finds: contradictions · missing edge cases · AC gaps · invariant conflicts
Rule: if > 3 problems found → stop and ask, do not guess.
```

**Change Specification** — separate format for modifying existing systems:
```
Current State → Desired State → What NOT to change → Impact → Rollback
```

---

## Quick Start

**5 minutes minimum viable spec:**

1. Fill **1.1** — what and for whom (2 sentences)
2. Fill **2.1** — domain glossary (5–10 terms)
3. Write **2.5** — 3–5 invariants
4. Write **3.2** — user stories with Acceptance Criteria
5. Fix **2.4** — tech stack

Then hand the spec to an agent and ask it to run **Agent Review (Section 14)** before coding.

---

## Files

| File | Description |
|---|---|
| `ANSS-Standard-v1.3.en.md` | Full standard — all 17 sections |
| `ANSS-Core-Template.en.md` | Working template — start here |
| `examples/` | Filled real-world examples |

---

## Examples

| Project | Type | Level |
|---|---|---|
| [Solo Founder OS](examples/solo-founder-os.md) | Local web app, Node.js, AI agents | Core |
| [LombardPro](examples/lombard-pro.md) | SaaS, multi-branch pawnshop management | Core |

---

## License

[CC BY-NC-SA 4.0](LICENSE) — free for personal and internal commercial use, attribution required.

Commercial redistribution → contact [kholomyanskiy@gmail.com](mailto:kholomyanskiy@gmail.com)

**Author:** Artem Kholomyanskiy — AI automation, EVAI Consulting  
[LinkedIn](https://linkedin.com/in/kholomyanskiy) · [GitHub](https://github.com/Kholomyanskiy)
