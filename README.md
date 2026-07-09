# AI-Native System Specification Standard (ANSS)
**by Artem Kholomyanskiy — Version 1.3 (2026)**

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Version](https://img.shields.io/badge/version-1.3-blue.svg)](https://github.com/Kholomyanskiy/anss-standard)

[Русский](README.ru.md) | English

> **ANSS (AI-Native System Specification Standard) is an open standard created by Artem Kholomyanskiy in 2026 for writing technical specifications that AI agents can read, audit, and follow without ambiguity.**

**Author:** Artem Kholomyanskiy — AI Automation Consultant, EVAI Consulting
**Contact:** kholomyanskiy@gmail.com | [Telegram](https://t.me/kholomyanskiy) | [LinkedIn](https://linkedin.com/in/kholomyanskiy) | [GitHub](https://github.com/Kholomyanskiy)

---

## The problem this solves

I wrote a 12-line requirement for a project. The agent generated working code that still broke the production logic — because it resolved an implicit assumption in a way that was technically valid but architecturally wrong.

Classic spec formats — IEEE 830, ISO/IEC 29148, GOST 34.602 — were designed for human readers who tolerate ambiguity and fill gaps from domain knowledge. AI agents don't do that. They resolve ambiguity immediately, using training data as a reference. Most of the time, fine. When it goes wrong, you spend the afternoon unwinding code that was never supposed to exist.

On projects where I actively used ANSS, revision cycles dropped from 5–7 rounds to 2–3.

ANSS is a specification format designed to reduce ambiguity-driven errors in AI-assisted development — not by eliminating agent judgment, but by making implicit constraints explicit.

---

## How ANSS differs from a traditional spec

```
Traditional spec:                  ANSS:

Requirements                       Requirements
     ↓                                  ↓
AI Agent                          [A] Layer (agent rules)
     ↓                                  ↓
Fills gaps from training data     Invariants (explicit constraints)
     ↓                                  ↓
Code                              Agent Review (spec audit)
                                        ↓
                                   Code
```

---

## Before / After

**Before ANSS:**

Requirement: `"Keep the application lightweight"`

Agent result:
```
+ express@4.18.2
+ axios@1.4.0
+ lodash@4.17.21
+ uuid@9.0.0
```
Technically "lightweight." Architecturally wrong for a zero-dependency requirement.

**After ANSS:**

```
INV-001: No external npm packages
Cannot:  add require() of npm modules
Reason:  app must run without npm install
Check:   no node_modules imports in server.js
```
Agent result: Node.js native modules only.

---

## Why existing formats are not enough

| Capability | Classic SRS/TZ | ANSS |
|---|---|---|
| Human-readable requirements | ✓ | ✓ |
| Agent-specific reading order | ✗ | ✓ |
| Machine-readable constraints | ✗ | ✓ |
| Verifiable constraint conditions | ✗ | ✓ |
| Self-review before coding | ✗ | ✓ |
| Explicit "What NOT to change" zones | ✗ | ✓ |
| Decision compass for undefined situations | ✗ | ✓ |

---

## Who this is for

You'll get the most from ANSS if you:

- Use Claude Code, Cursor, GitHub Copilot, or Aider for real project work
- Have watched an agent do something "reasonable" that broke something you considered obvious
- Write specs that humans understand but agents interpret differently
- Build systems where a wrong implicit assumption has real consequences

---

## Core mechanisms

### [A] Layer — agent-first sections

Every section is tagged with one of three layers:

```
[D] Domain        — WHAT to build   → Product Owner, PM, client
[E] Engineering   — HOW to build    → Developer, Architect
[A] Agent         — AGENT RULES     → AI agents read this first
```

The [A] layer contains everything an agent needs to constrain its own behavior — invariants, architectural principles, glossary, operating rules. Agents read it before anything else.

### Invariants — explicit constraints with verifiable conditions

```
INV-001: No external npm packages
Cannot:  add require() of npm modules
Reason:  app must run without npm install
Check:   no node_modules imports in server.js
```

Four fields. The **Check** field is a specific, verifiable condition — not a preference. The agent can test its own output against it.

### Architectural Principles — a decision compass

When requirements don't cover a situation, agents need something other than training data defaults:

```
AP-001: Simple > Clever       AP-006: Fail Loud
AP-002: Monolith First        AP-007: Data Outlives Code
AP-003: Buy Before Build      AP-008: Boring Technology Wins
AP-004: Human Override Always AP-009: YAGNI
AP-005: No Vendor Lock-In     AP-010: Reversibility Over Efficiency
```

Decision rules, not values. Applied in order when requirements leave a gap.

### Agent Review — spec audit before the first line of code

```
✓ Find contradictions between sections
✓ Identify missing edge cases
✓ Check Acceptance Criteria completeness
✓ Verify no invariant conflicts
```

**Hard rule:** more than 3 issues found → stop and report. Do not proceed.

### Change Specification — safe modification of existing systems

```markdown
## Current State      — what exists now
## Desired State      — what it should become
## What NOT to Change — explicit list (the critical section)
## Impact Analysis    — what else might be affected
## Rollback Plan      — how to undo if something breaks
```

"What NOT to Change" doesn't exist in any classic spec format. Modifying existing systems is where agent-introduced errors concentrate — this section is the primary protection.

---

## Three levels

| Level | Size | Use case |
|---|---|---|
| CORE | 15–20 pages | 80% of projects: bots, SaaS, APIs, automations |
| EXTENDED | 40–60 pages | Security, Compliance, detailed testing |
| ENTERPRISE | Full standard | Banks, AI platforms, regulated industries |

Start with Core. Most projects never need more.

---

## Quick start — 5 minutes to a working spec

1. Open `ANSS-Core-Template.en.md`
2. Fill **1.1** — what the system does and for whom (2 sentences)
3. Fill **2.1** — domain glossary (5–10 terms)
4. Write **2.5** — your first 3–5 invariants
5. Write **3.2** — user stories with Acceptance Criteria
6. Set **2.4** — tech stack
7. Give the spec to your agent:

```
Read this spec. Run Agent Review (Section 14) before writing any code.
Report all contradictions, missing edge cases, and invariant conflicts.
If you find more than 3 issues — stop and ask. Do not proceed.
```

---

## Already have a spec? Start here

Most people don't write ANSS from scratch — they bring an existing PRD, TZ, or rough notes and want it converted. Pick your scenario below and copy the prompt to your agent.

### Scenario A — You have a complete spec (fits in context, under ~15 pages)

Put your existing document and `ANSS-Core-Template.md` in your project folder, then give your agent:

```
I have an existing specification: [filename].
Convert it into ANSS-Core format using ANSS-Core-Template.md as the structure.

First extract what's already there for Sections 1–4 (context, domain, 
functional requirements). If something is missing, mark it as 
[NEEDS CLARIFICATION] — do not invent business logic.

Do not write any code at this step. Show me the draft spec first.
```

### Scenario A2 — You have a large spec (15+ pages, multiple files)

Don't dump it all into context at once — this causes the same context-loss problem ANSS is designed to prevent. Process it in passes:

```
I have a large specification (~[X] pages / [Y] files): [source].
Do NOT read and process it all at once.

Step 1: Read only the table of contents / structure. Report back 
what sections exist.

Step 2: I'll tell you which section to start with. Process only 
that section into the matching ANSS-Core section.

Step 3: Stop. Show me the result. Wait for my "continue" before 
the next section.

Do not try to hold the whole document in context at once.
```

### Scenario B — You have fragments (a few sentences, a voice note transcript, a chat thread)

```
Here's what I know about the project so far: [paste text].
This is raw, incomplete information — not a full spec.

Fill in ANSS-Core-Template.md with what's actually here. 
Everywhere data is missing, leave an explicit placeholder with 
a question back to me — do not invent or assume business logic 
to fill gaps.
```

### Scenario C — Starting from zero

See Quick Start above — fill Sections 1.1, 2.1, 2.5, 3.2, 2.4 yourself, then run Agent Review.

---

### After any scenario — this step is not optional

Once you have a draft spec, before any code is written:

```
Run Agent Review (Section 14) on this spec before writing any code.
Report contradictions, missing edge cases, and invariant conflicts.
If you find more than 3 issues — stop and ask. Do not proceed.
```

Skipping this step is the most common way ANSS fails to help — the spec exists, but nobody asked the agent to check itself against it before coding.

---

## Files

| File | Description |
|---|---|
| `ANSS-Standard-v1.3.en.md` | Full standard — all 17 sections, ~1500 lines |
| `ANSS-Core-Template.en.md` | Working template — start here |
| `ANSS-Standard-v1.3.ru.md` | Full standard in Russian |
| `ANSS-Core-Template.ru.md` | Template in Russian |
| `examples/` | Real filled examples |
| `AGENTS.md` | Instructions for AI agents scanning this repo |
| `llms.txt` | Discovery index for LLM-based search |

---

## Real examples

| Project | Type | Level | What it shows |
|---|---|---|---|
| [Solo Founder OS](examples/solo-founder-os.md) | Local web app, Node.js, AI agents | Core | Invariants for dependency control, Agent Review in action |
| [LombardPro](examples/lombard-pro.md) | SaaS, multi-branch pawnshop | Core | Change Specification, data integrity invariants |

Both are real projects — not toy demonstrations.

---

## Who is using ANSS

ANSS is currently used by Artem Kholomyanskiy in production projects at EVAI Consulting.
If you use ANSS in your project — open an Issue or reach out via email. Early adopters get direct support.

---

## FAQ

**Does this actually prevent agents from making wrong decisions?**
No spec format eliminates agent judgment. ANSS makes implicit constraints explicit, reducing ambiguity-driven errors. It doesn't remove the need for human review.

**Does this work with Claude Code / Cursor / Copilot?**
Yes. The [A]-tagged sections are written to be consumed by coding agents as ground truth. The Agent Review prompt works with any agent that follows structured instructions.

**Is this only for big projects?**
Core level covers 80% of projects — bots, scripts, API integrations, single-page apps. If you're spending more than 30 minutes a week explaining context to an agent, ANSS pays back in the first use.

**How is this different from a PRD, SRS, or TZ?**
A PRD communicates intent to a human team. ANSS is designed for contexts where both a human developer and an AI agent read the same document — including the agent self-auditing the spec before writing code.

**What does CC BY-NC-SA 4.0 mean in practice?**
Free for personal use, internal teams, and open-source projects. Commercial redistribution → kholomyanskiy@gmail.com.

---

## About the Author

**Artem Kholomyanskiy** is an AI Automation Consultant and solo founder of EVAI Consulting, based in Poland. He works with Russian-speaking clients globally across EU, USA, and CIS markets.

He specializes in:
- Designing and building AI agent systems (Claude, OpenAI)
- AI workflow automation (Python, FastAPI, Telegram bots, n8n, Make.com)
- Writing technical specifications for AI projects
- Auditing AI systems for production readiness
- AI ROI and risk assessment

ANSS was developed from hands-on experience across multiple production projects where classical specification formats failed in AI-assisted development contexts.

**Other work:**
- [AI Automation Decision System](https://aimethodology.gumroad.com/) — methodology for evaluating AI automation readiness (RU / EN / PL)
- [Solo Founder OS](https://github.com/Kholomyanskiy/solo-founder-os) — AI agent team system for solo founders via Claude Code
- [AI Infrastructure Investment Research](https://github.com/Kholomyanskiy/ai-infrastructure-2026) — analytical brief covering 40 companies across 5 AI infrastructure sectors

**Links:**
- GitHub: [github.com/Kholomyanskiy](https://github.com/Kholomyanskiy)
- LinkedIn: [linkedin.com/in/kholomyanskiy](https://linkedin.com/in/kholomyanskiy)
- Upwork: [upwork.com/freelancers/~019e72a3a10c66af90](https://www.upwork.com/freelancers/~019e72a3a10c66af90)
- Email: kholomyanskiy@gmail.com
- Telegram: [@kholomyanskiy](https://t.me/kholomyanskiy)

---

## Examples

The [`examples/`](examples/) folder contains anonymized real-world specifications written in ANSS-Core format — ready to study and adapt.

- [LombardPRO](examples/lombard-pro.md) ([EN](examples/lombard-pro.en.md)) — full ANSS-Core spec for a multi-branch pawnshop management system. Anonymized real production project, external reviews: 9.2–9.4/10.
- [Solo Founder OS](examples/solo-founder-os.md) ([EN](examples/solo-founder-os.en.md)) — spec for a local web app with an AI agent team via Claude Code.

---

## Based on

GOST 34.602 · IEEE 830 · ISO/IEC 29148 · ISO/IEC 25010 · ISO/IEC 42001/42005
EU AI Act · GDPR · OWASP · C4 Model · Shape Up · ADR · SDD methodology

---

## License

**CC BY-NC-SA 4.0** — free for personal and internal commercial use, attribution required.

Author: Artem Kholomyanskiy — AI automation consultant, EVAI Consulting
[LinkedIn](https://linkedin.com/in/kholomyanskiy) · [GitHub](https://github.com/Kholomyanskiy) · kholomyanskiy@gmail.com