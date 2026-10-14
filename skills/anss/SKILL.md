---
name: anss
description: Write, convert, or review a technical specification in ANSS format for development with AI agents. Use when the user asks to write a spec, convert an existing spec/PRD/TZ or rough notes into ANSS, or check a spec before coding.
---

# ANSS — specification skill

This skill produces and reviews specifications. It does not write code.
Structure: [core-template.md](core-template.md) in this folder (ANSS-Core Template). Section numbers below
refer to it.

Rules for every mode:
- Do not invent business logic. Mark every gap `[NEEDS CLARIFICATION]` and ask.
- When something is unclear — stop and ask. Do not fill gaps with assumptions.

Pick the mode from the request.

## Mode 1 — New specification

1. Copy the structure of `core-template.md`.
2. Fill, in this order, only from what the user gave:
   - **1.1** — what the system does and for whom (2 sentences)
   - **2.1** — domain glossary (5–10 terms)
   - **2.5** — first 3–5 invariants, four fields: Name / Cannot / Reason / Check
   - **3.2** — user stories with Acceptance Criteria
   - **2.4** — tech stack
3. Everything missing — `[NEEDS CLARIFICATION]` with a question to the user.
4. Show the draft. Then run Mode 3 on it.

## Mode 2 — Convert an existing spec

Pick the scenario by the source:

**A — complete spec, fits in context (under ~15 pages).**
Extract what already exists for Sections 1–4 (context, domain, functional requirements)
into the template structure. Missing — `[NEEDS CLARIFICATION]`. Show the draft.

**A2 — large spec (15+ pages, multiple files).** Do not read it all at once.
1. Read only the table of contents / structure. Report which sections exist.
2. The user names the section to start with. Convert only that section into the
   matching ANSS-Core section.
3. Stop. Show the result. Wait for "continue" before the next section.

**B — fragments (a few sentences, voice note transcript, chat thread).**
Fill the template with what is actually there. Everywhere data is missing, leave an
explicit placeholder with a question back to the user.

After any scenario — run Mode 3. This step is not optional.

## Mode 3 — Review a specification (Agent Review, Section 14)

Read the whole spec, then find:
1. Contradictions between sections
2. Situations not described but that will definitely arise
3. Edge cases missing from the specification
4. Missing or unverifiable Acceptance Criteria
5. Conflicts with invariants (Section 2.5)
6. Conflicts with principles (Section 2.6)

Do not propose solutions — only list problems.

Report format:
- **Contradictions:** …
- **Missing edge cases / undescribed situations:** …
- **Invariant and principle conflicts:** …
- **Acceptance Criteria completeness:** …
- **Questions before start:** …

More than 3 problems — state that the spec is not ready for code and wait for answers.

---
Based on ANSS v1.4 (CC BY 4.0): https://github.com/Kholomyanskiy/anss-standard
