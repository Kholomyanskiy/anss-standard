# CLAUDE.md — ANSS project rules

This project is specified with ANSS (AI-Native System Specification Standard).
Copy this file to your project root and set the path below.

Spec: <path to your ANSS spec>

Section references: "Std" = ANSS-Standard-v1.4, "Tpl" = ANSS-Core-Template.

## Rules

1. **Reading order.** Read the [A] sections first: Invariants (2.5), Principles (2.6),
   Glossary (2.1), then Architecture, then the current task, then other sections as needed.
   *(Std: Layer Marking; Tpl: How to Use This Template)*

2. **Invariants are absolute.** Never violate an invariant. If a task requires it — stop.
   Before handing in work, verify each invariant by its Check field.
   *(Std §2.5; Tpl §2.5)*

3. **What NOT to Change.** For a change to an existing system, do not touch anything
   listed under "What We Are NOT Changing".
   *(Std §16.1; Tpl §10)*

4. **Principles under uncertainty.** When requirements do not cover a situation, decide
   by AP-001…AP-010. If principles conflict — stop and ask.
   *(Std §2.6; Tpl §2.6)*

5. **Agent Review before code.** Before writing the first line of code, review the spec:
   contradictions, undescribed situations, missing edge cases, missing Acceptance
   Criteria, conflicts with invariants and principles. Do not propose solutions — list
   problems. More than 3 problems — stop and report.
   *(Std §15.6; Tpl §14)*

6. **Do not invent business logic.** If something is unclear or missing — stop and ask.
   Mark gaps as `[NEEDS CLARIFICATION]`.
   *(Std: Agent Instructions, Step 4; Tpl: How to Use This Template; README: Scenario A)*

---
Based on ANSS v1.4 (CC BY 4.0): https://github.com/Kholomyanskiy/anss-standard
