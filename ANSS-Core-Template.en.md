# ANSS-CORE TEMPLATE
## AI-Native System Specification — Working Template

**Template version:** 1.0
**Level:** CORE (80% of projects)
**Volume:** 15–20 pages for a filled document

> This is a working template for daily use.
> The full standard and rationale for each section — in ANSS Standard v1.3.
> Fill in fields in [brackets]. Delete italic hints after filling.

---

## HOW TO USE THIS TEMPLATE

**For humans:** fill in sections 0–2, then work with an agent on sections 3–15.

**For agents:**
```
1. Read the entire document
2. Re-read the GLOSSARY (section 2.1) — this is the language of the project
3. Memorize the INVARIANTS (section 2.5) — they must never be violated
4. Read the PRINCIPLES (section 2.6) — your compass under uncertainty
5. Perform Agent Review (section 14) BEFORE writing code
6. When in doubt — STOP AND ASK
```

---

# SECTION 0. METADATA

| Field | Value |
|---|---|
| Project | [name] |
| Version | 0.1 Draft |
| Date | [YYYY-MM-DD] |
| Status | Draft |
| Author | Artem Khalomyansky |
| Client | [name / company] |
| Executor | [name / agent] |

**Version history:**

| Version | Date | Change |
|---|---|---|
| 0.1 | [date] | First draft |

**Detail level:** CORE
*(if needed, expand to EXTENDED or ENTERPRISE — see ANSS Standard v1.3)*

---

# SECTION 1. CONTEXT AND ECONOMICS

## 1.1 Project Essence

**What we are building:**
[One or two sentences. What the system does and for whom.]

**Problem we are solving:**
[What is painful for the user or business right now.]

**Why now:**
[Why it is important to solve this now. Cost of inaction.]

**What we are NOT doing in this version:**
- [Explicit list of exclusions — required]
- [Explicit list of exclusions]

## 1.2 Goals and Success Criteria

**Business goals:**
- [Goal 1 — measurable, with a deadline]
- [Goal 2]

**KPIs — how we will know success:**
- [KPI 1]: [target value]
- [KPI 2]: [target value]

**Project Definition of Done:**
[What must be completed for the project to be considered finished]

## 1.3 Economics

*Fill in if the project is an automation or commercial product.*
*Skip for internal tools without ROI justification.*

**Current costs (before):**
- Man-hours for process: ___ h/mo × $___/h = $___/mo
- Cost of errors: $___/mo
- **Total losses:** $___/mo

**After implementation:**
- Time savings: ___ h/mo
- Error reduction: ___%
- **Monthly benefit:** $___/mo

**Costs:**
- Development (CAPEX): $___
- LLM inference (if applicable): $___/mo
- Infrastructure: $___/mo
- **Total OPEX:** $___/mo

**ROI:** payback period ___ months

---

# SECTION 2. DOMAIN

## 2.1 Glossary

*Agent reads this section FIRST. All project-specific terms go here.*

**[Term 1]**
Definition: [exact meaning in the context of the project]
Do not confuse with: [similar term with a different meaning]
In code: [variable / table name]

**[Term 2]**
Definition: [...]
Do not confuse with: [...]
In code: [...]

*Add terms as you write the specification.*

## 2.2 Users

*For each role — a separate block:*

**Role: [name]**
- Who they are: [description]
- What they do in the system: [main actions]
- What they value: [speed / accuracy / simplicity]
- Expected count: [number]
- Usage frequency: [daily / weekly / monthly]

## 2.3 Constraints and Assumptions

**Technical constraints (cannot be changed):**
- [What is fixed]

**Business constraints:**
- Budget: $___
- Deadline: [date]
- Team: [composition]

**Assumptions:**
- [What is taken as given without verification]

## 2.4 Technology Stack

| Layer | Technology | Version |
|---|---|---|
| Backend | [language + framework] | |
| Frontend | [if applicable] | |
| Database | | |
| Cache | [if applicable] | |
| ORM | | |
| Tests | | |
| Deploy | | |

**External dependencies:**

| Service | Purpose | Fallback if unavailable |
|---|---|---|
| [service] | [what for] | [what we do] |

## 2.5 INVARIANTS ✅

*Absolute constraints. Must never be violated under any circumstances.*
*The agent stops if the task requires violating an invariant.*

```
INV-001: [Name]
Cannot: [what is forbidden]
Reason: [why this is an invariant]
Verification: [how to confirm it is upheld]

INV-002: [Name]
Cannot: [...]
Reason: [...]
Verification: [...]
```

*Minimum 3–5 invariants for any project.*
*Typical: operation atomicity, API backward compatibility,*
*data preservation, audit log immutability, authorization.*

## 2.6 ARCHITECTURAL PRINCIPLES ✅

*Decision-making guidance under uncertainty.*
*Select applicable ones from the standard, add project-specific ones.*

```
AP-001: Simple > Clever
AP-002: Monolith First [remove if already microservices]
AP-003: Buy Before Build
AP-004: Human Override Always Available [required for AI systems]
AP-005: No Vendor Lock-In Without Justification
AP-006: Fail Loud
AP-007: Data Outlives Code
AP-008: Boring Technology Wins
AP-009: YAGNI
AP-010: Reversibility Over Efficiency

[Add project-specific principles:]
AP-011: [...]
```

---

# SECTION 3. FUNCTIONAL REQUIREMENTS

## 3.1 Functionality Overview

**Functional blocks:**
- [Block 1]: Must Have
- [Block 2]: Must Have
- [Block 3]: Should Have
- [Block 4]: Could Have

**Phases (if incremental):**
- Phase 1 (MVP): [what is included]
- Phase 2: [what is included]

## 3.2 User Stories

*For each key feature:*

```
US-001: [Name]
As [role]
I want [specific action]
So that [measurable result]

Acceptance Criteria:
- [ ] [Specific verifiable criterion — not subjective]
- [ ] [Specific verifiable criterion]

Edge Cases:
- If [condition] → [system behavior]
- If [condition] → [system behavior]
```

## 3.3 Detailed Requirements by Module

*For each module:*

```
MODULE: [Name]
Purpose: [one sentence]
Related invariants: [INV-XXX]

User Flow:
1. [User action]
2. [System response]
3. [Next step]

Business Rules:
BR-001: [rule]
  Exceptions: [when it does not apply]
  Edge case: [behavior at the boundary]

Input: [what it accepts + types + constraints]
Output: [what it returns + format]

Error Handling:
- [Situation] → [What the system does] → [What the user sees]
- [Situation] → [...]

Acceptance Criteria:
- [ ] [...]
- [ ] [...]
```

## 3.4 Business Rules Register

| ID | Rule | Exceptions | Priority | Invariant |
|---|---|---|---|---|
| BR-001 | | | High | INV-XXX |

---

# SECTION 4. NON-FUNCTIONAL REQUIREMENTS

## 4.1 Performance

| Metric | Normal | Peak |
|---|---|---|
| Response time p95 | < ___ms | < ___ms |
| Throughput | ___ RPS | ___ RPS |

*Under 2x load: [system behavior]*

## 4.2 Reliability

| Metric | Value |
|---|---|
| Uptime SLA | 99.___% |
| RTO | ___ min |
| RPO | ___ min |

*Under partial failure: [graceful degradation / full unavailability]*

## 4.3 Performance Budget (for AI systems)

- LLM response time p95: < ___s
- Tokens per request: ≤ ___
- Cost per request: ≤ $___
- Monthly inference budget: ≤ $___

---

# SECTION 5. ARCHITECTURE

## 5.1 Overview

**Type:** [monolith / modular monolith / microservices / serverless]

**Diagram (C4 Level 1):**
```
[Draw ASCII or describe component connections in words]

[Client] → [API] → [Backend] → [DB]
                              ↓
                      [External Services]
```

**Application layers:**
```
[Layer 1] — [responsibility]
[Layer 2] — [responsibility]
[Layer 3] — [responsibility]
```

**Layer rules (for agents):**
- [Layer 1] has no knowledge of [Layer 3]
- Business logic only in [...]
- Direct DB queries only through [...]

## 5.2 API Contracts (key endpoints)

```
[METHOD] /api/v[N]/[resource]
Auth: [Bearer token / API Key / public]

Request:
{
  "[field]": "[type]",  // required — [description]
  "[field]": "[type]"   // optional — [description]
}

Response 200:
{ "[field]": "[value]" }

Response 400: { "error": "VALIDATION_ERROR", "fields": {} }
Response 401: { "error": "UNAUTHORIZED" }
Response 403: { "error": "FORBIDDEN" }
Response 404: { "error": "NOT_FOUND" }
Response 409: { "error": "CONFLICT", "message": "..." }
```

## 5.3 Data Schema (key entities)

```sql
-- [Entity name]
CREATE TABLE [table_name] (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  [column]    [TYPE] NOT NULL,   -- [description]
  created_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## 5.4 ADR + Decision Log

**ADR (architectural decisions):**
```
ADR-001: [Name]
Context: [what required a decision]
Decision: [what was chosen]
Reason: [why]
Trade-offs: [what we accept]
For agents: [operational implications]
```

**Decision Log (operational decisions):**
```
DEC-001: [Name]
Question: [what was being decided]
Decision: [what was chosen]
Reason: [why]
Rejected: [what and why not]
```

---

# SECTION 6. SECURITY

## 6.1 Authentication and Authorization

- Method: [password + JWT / OAuth2 / SSO / API Key]
- Authorization: [RBAC / ABAC]
- Token TTL: [time]
- MFA: [yes / no / optional]

**Permissions matrix (role × operation):**

| Operation | [Role 1] | [Role 2] | [Role 3] |
|---|---|---|---|
| [action] | ✓ | ✗ | ✗ |
| [action] | ✓ | ✓ | ✗ |

## 6.2 Data Protection

- Encryption at rest: [algorithm]
- Encryption in transit: TLS 1.3
- Sensitive data: [list]
- Never logged: passwords, tokens, [add more]

## 6.3 Secrets Management

- Storage: [Vault / AWS Secrets Manager / .env in encrypted storage]
- In code or config: NEVER

## 6.4 Compliance

- Personal data: [yes / no → GDPR applicable / N/A]
- Payment data: [yes / no → PCI DSS / N/A]
- Medical data: [yes / no → HIPAA / N/A]
- AI system: [yes / no → EU AI Act classification / N/A]

---

# SECTION 7. INFRASTRUCTURE

## 7.1 Environments

| Environment | Data | Access |
|---|---|---|
| Development | synthetic | team |
| Staging | anonymized | team + QA |
| Production | real | restricted |

## 7.2 Deployment

- Provider: [AWS / GCP / Azure / VPS / on-premise]
- Region: [considering data jurisdiction]
- Strategy: [Blue/Green / Canary / Rolling]
- Rollback: [trigger and procedure]

## 7.3 Monitoring (minimum)

- Uptime monitor: [tool]
- Logs: [tool]
- Alerts: [notification channel]
- On-call: [who]

## 7.4 Backup

- DB: every ___ hours, retention ___ days
- Recovery verification: once every ___ months

---

# SECTION 8. TESTING

## 8.1 Strategy

| Level | Tool | Coverage |
|---|---|---|
| Unit | | ___% |
| Integration | | ___% |
| E2E | | ___ scenarios |

## 8.2 Key Test Cases

```
TEST-001: [Name]
Given:     [precondition]
When:      [action]
Then:      [expected result]
Invariant: [INV-XXX if verified]
```

*Must cover:*
- Happy path for each module
- Each error code from API Contracts
- Boundary values
- Verification of each invariant

## 8.3 Smoke Tests (after each deploy)

```
□ [Critical endpoint 1] → HTTP 200
□ [Critical endpoint 2] → HTTP 200
□ Main user flow passes
□ No critical errors in logs
```

---

# SECTION 9. PROJECT MANAGEMENT

## 9.1 Decomposition

| Module | Depends on | Who | Estimate |
|---|---|---|---|
| [module 1] | — | [agent/human] | ___ |
| [module 2] | [module 1] | | ___ |

**Critical path:** [sequence of blocking tasks]

## 9.2 Risks

| Risk | Probability | Impact | Mitigation |
|---|---|---|---|
| [risk 1] | H/M/L | H/M/L | [what we do] |
| [risk 2] | | | |

## 9.3 Explicit Out of Scope

- [What we are not doing now — must be written explicitly]
- [What we are not doing now]

---

# SECTION 10. CHANGE SPECIFICATION

*Use this template when modifying an existing system.*
*Fill in before starting the change.*

```
CHANGE: [Name of change]
Date: YYYY-MM-DD
Affected invariants: [INV-XXX]

CURRENT STATE:
[How it works right now — specifically]

TARGET STATE:
[How it should work after]

WHAT WE ARE NOT CHANGING:
- [file / module] — do not touch
- [file / module] — do not touch

IMPACT ANALYSIS:
- Affected files: [list]
- Risks: [what might break]

ROLLBACK:
- Trigger: [under what condition we roll back]
- Procedure: [specific steps]

REGRESSION CHECKS:
- [ ] [what to verify after the change]
- [ ] [what to verify after the change]
```

---

# SECTION 11. AI-FIRST (SDD)

*Fill in if development is conducted with AI agents.*

## 11.1 Agent Configuration

**Project artifacts:**
```
.claude/CLAUDE.md     — global rules
.cursorrules          — rules for Cursor
docs/specs/
  PROJECT_BRIEF.md
  GLOSSARY.md         — read first
  ARCHITECTURE.md
  AGENTS.md
  modules/            — module specifications
```

**Rules hierarchy:**
```
1. CLAUDE.md → highest priority
2. AGENTS.md → agent role
3. MODULE_SPEC → current task
4. Session prompt → operational instructions
Lower level NEVER overrides higher.
```

**Forbidden Patterns for this project:**
```
DO NOT [specific prohibition for this project]
DO NOT [specific prohibition]
DO NOT [specific prohibition]
```

## 11.2 Fail-Fast Conditions

```
AGENT STOPS when:
□ Choosing between architectural approaches
□ Conflicting requirements
□ Task outside specification
□ Violation of any invariant (Section 2.5)
□ YAGNI violation — complexity without business justification

AGENT ACTS INDEPENDENTLY when:
□ Implementation details with no external effects
□ Naming and formatting
□ Decisions following from existing patterns
```

## 11.3 LLM/AI Components (if the system uses AI)

- Model: [name + provider]
- Temperature for agentic tasks: 0.0–0.3
- Temperature for creative tasks: 0.7–1.0
- AI invariants: [what the product agent never does]
- EU AI Act: [Unacceptable / High / Limited / Minimal risk]

---

# SECTION 12. SUPPORT

*Fill in for production systems.*

## 12.1 SLA

| Metric | SLO |
|---|---|
| Availability | 99.___% |
| Latency p95 | < ___ms |
| Error rate | < ___% |

## 12.2 Incidents

| Priority | Criterion | Response |
|---|---|---|
| P0 | system unavailable | ___ min |
| P1 | critical function | ___ h |
| P2 | degradation | ___ h |

---

# SECTION 13. LIVING SPEC

*Rules for keeping the document current.*

```
□ Specification is updated in the same iteration as the code
□ Code change without spec update = technical debt
□ ADR is updated with every architectural decision
□ Decision Log is updated with every non-trivial choice
□ Invariants and principles — only a human may change them
```

---

# SECTION 14. AGENT REVIEW — REQUIRED BEFORE CODE

*The agent performs this section BEFORE writing any code.*

```
PROMPT FOR AGENT:
"Read this specification and find:
1. Contradictions between sections
2. Situations not described but that will definitely arise
3. Edge cases missing from the specification
4. Missing Acceptance Criteria
5. Conflicts with invariants from Section 2.5
6. Conflicts with principles from Section 2.6

Do NOT propose solutions — only list problems.
If you found > 3 problems — stop and wait for answers."
```

**Agent Review result:**
*(filled in by the agent after reading the spec)*

Problems found:
1. [...]
2. [...]

Questions before start:
1. [...]
2. [...]

---

# SPECIFICATION READINESS CHECKLIST

*Check before handing to an agent for implementation.*

```
REQUIRED
□ Invariants written (minimum 3)
□ Architectural principles selected
□ What is NOT in scope — written explicitly
□ Each requirement has Acceptance Criteria
□ Acceptance Criteria are specific and verifiable
□ Edge cases described for each module
□ API Contracts include all error codes
□ Technology stack is fixed
□ Forbidden Patterns written

QUALITY
□ No contradictions between sections
□ All terms in glossary
□ Agent Review performed (Section 14)
□ Decision Log filled for non-trivial decisions
```

---

*ANSS-Core Template v1.0 — Artem Khalomyansky — 2026*
*Template based on ANSS Standard v1.3*
*For extended requirements use ANSS-Extended or ANSS-Enterprise*
