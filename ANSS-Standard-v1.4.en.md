# AI-NATIVE SYSTEM SPECIFICATION STANDARD
## Personal Standard by Artem Kholomyanskiy — Version 1.4, 2026

---

## ABOUT THIS DOCUMENT

This is not just a specification template. It is a project operating system — for humans and AI agents simultaneously.

**What's inside:** system requirements, architectural decisions, economic justification, agent operating rules, invariants that must never be violated.

**Based on:** GOST 34.602, IEEE 830, ISO/IEC 29148, ISO/IEC 25010, ISO/IEC 42001/42005, EU AI Act, GDPR, OWASP, C4 Model, Shape Up, ADR, SDD methodology.

---

## LAYER MARKING

Each section is marked with one of three layers:

```
[D] Domain Specification    — WHAT we build. For the client, analyst, PM.
                              Business goals, users, requirements, compliance.

[E] Engineering Specification — HOW we build it. For developer, architect.
                              Architecture, stack, infrastructure, security.

[A] Agent Specification     — HOW THE AGENT implements it. For AI agents only.
                              Configuration, rules, context, invariants.
```

**Canonical agent reading order** *(single source of truth for the entire standard)*:

```
1. Invariants              (Section 2.5)
2. Principles              (Section 2.6)
3. Domain Glossary         (Section 2.1)
4. Architecture            (Section 5)
5. Current task specification
6. Remaining [E] and [D] sections — as needed
```

All other mentions of reading order in this document refer to this block.

---

## AGENT INSTRUCTIONS

```
You have received a specification writing standard. Follow the steps strictly in order.

════════════════════════════════════════════════════
STEP 0: CLARIFICATION
════════════════════════════════════════════════════

If the project description is incomplete — ask these questions BEFORE starting:

1. What does the system do and for whom? (one or two sentences)
2. Who is developing it? (AI agents / humans / both)
3. Development time estimate? (days / weeks / months+)
4. Are there personal user data involved?
5. Internal tool or public service?
6. Are there payment operations or financial logic?
7. Does the system use LLM/AI as part of the product?
8. Is there existing code that must not be broken?

Wait for answers. Only then — Step 1.

════════════════════════════════════════════════════
STEP 1: PROJECT CLASSIFICATION
════════════════════════════════════════════════════

TYPE:
□ Micro-automation (bot, script, integration)
□ Web application / SaaS
□ Mobile application
□ API / Backend service
□ AI system / LLM product
□ Enterprise system
□ Platform / Marketplace
□ Data system / Analytics
□ Change to an existing system (→ use CHANGE SPEC)

SCALE:
□ Micro  (up to 2 weeks)
□ Small  (2–8 weeks)
□ Medium (2–6 months)
□ Large  (more than 6 months)

TEAM:
□ AI agents only
□ Humans only
□ Mixed (humans + agents)

CRITICALITY:
□ Low      — error = inconvenience
□ Medium   — error = money/time loss
□ High     — error = legal/financial consequences
□ Critical — error = people safety / critical infrastructure

DATA:
□ No personal data → 10.1 N/A
□ Personal data EU/UK → 10.1 REQUIRED
□ Payment data → PCI DSS REQUIRED
□ Medical data (US) → HIPAA REQUIRED

════════════════════════════════════════════════════
STEP 2: DETAIL LEVEL RECOMMENDATION
════════════════════════════════════════════════════

CORE       → Micro or Small + Low/Medium criticality
           Only ⚡ required sections

EXTENDED   → Medium OR High criticality
           ⚡ required + applicable 📋 sections

ENTERPRISE → Large OR Critical OR regulated industry
           All applicable sections

Suggest a level. Wait for confirmation.

════════════════════════════════════════════════════
STEP 3: TAILORED CHECKLIST
════════════════════════════════════════════════════

Generate a list of only the needed subsections:
✅ [Number] [Name] — REQUIRED
📋 [Number] [Name] — OPTIONAL (applicable)
⬜ [Number] [Name] — N/A: [one-line reason]

Present the list. Wait for confirmation.

════════════════════════════════════════════════════
STEP 4: WRITING THE SPECIFICATION
════════════════════════════════════════════════════

RULES:
- Explicit > Implicit: write everything important explicitly
- Constraints BEFORE functionality
- Acceptance Criteria: specific, verifiable, unambiguous
- "p95 < 200ms at 100 RPS" — correct
- "the system must work fast" — incorrect

UNCERTAINTY RULE:
If something is unclear — STOP AND ASK.
Do not guess. Do not fill gaps with assumptions.

════════════════════════════════════════════════════
STEP 5: AGENT REVIEW — REQUIRED BEFORE HANDOFF
════════════════════════════════════════════════════

Before writing any code the agent must:
1. Find contradictions between sections
2. Find gaps (situations not described)
3. Verify completeness of Acceptance Criteria
4. Verify presence of edge cases for each requirement
5. Check for conflicts with INVARIANTS (Section 2.5)

If > 3 problems found — stop and report BEFORE writing code.

════════════════════════════════════════════════════
STEP 6: SPEC SELF-REVIEW (Checklist 15.7)
════════════════════════════════════════════════════

Go through the quality checklist in section 15.7.
If > 3 items not completed — revise the spec.
```

---

# [D] SECTION 0. METADATA

**Applicability:** ✅ REQUIRED for all

## 0.1 Title Page
- System / project name
- Document version (starting 0.1 Draft)
- Date created and last updated
- Status: Draft / Review / Approved / Deprecated
- Author(s) and roles
- Client / Product Owner
- Executor(s)
- Contact details of parties

| Version | Date | What changed | Author |
|---|---|---|---|
| 0.1 | | First draft | |

## 0.2 Detail Level ✅
```
Selected level: [CORE / EXTENDED / ENTERPRISE]
Rationale: ___

CORE       → sections: 0, 1.1-1.3, 2.1-2.5, 3.1-3.4, 4.1-4.2,
           5.2, 7.1-7.2, 11.2, 14, 15
EXTENDED   → CORE + all applicable 📋 sections
ENTERPRISE → all applicable sections
```

## 0.3 Document Purpose ✅
- What this spec governs
- What is explicitly NOT in scope
- Related documents
- Who reads which layers: [D] client/PM, [E] developer/architect, [A] AI agent

## 0.4 Glossary and Conventions ✅
- **SHALL** — mandatory
- **SHOULD** — recommended
- **MAY** — optional
- **INVARIANT** — must never be violated under any circumstances
- Reference to GLOSSARY.md

---

# [D] SECTION 1. CONTEXT AND BUSINESS JUSTIFICATION

**Applicability:**
- ✅ REQUIRED: 1.1, 1.2, 1.3 — for all
- 📋 OPTIONAL: 1.4 — Medium+, multiple stakeholders
- 📋 OPTIONAL: 1.5 — commercial products, automations
- ⬜ N/A: 1.5 — internal tools without ROI justification

## 1.1 Business Context ✅
- Description of the organization and industry
- Market context (competitors, trends) — for commercial products
- Reasons for creating the system
- Strategic significance

## 1.2 Problem and Opportunity ✅
- Current state (AS-IS): how things work now, what is painful
- Target state (TO-BE): what will change after implementation
- Cost of inaction: what happens if nothing is done
- Alternatives considered and why they were rejected

## 1.3 Goals and Success Criteria ✅
- Business goals (measurable, with deadline)
- System goals (what it must deliver)
- KPIs: how we will know success has been achieved
- Project-level Definition of Done

## 1.4 Stakeholders 📋
*Applicable: Medium+, external client*

| Role | Who | Interests | Influence | Communication |
|---|---|---|---|---|
| Product Owner | | | | |
| Users | | | | |
| Executors | | | | |
| Regulators | | | | |

## 1.5 Economics and Justification 📋
*Applicable: commercial products, automations, investment decisions*

### Current Costs (before automation)
- Man-hours for process: X hours/month × $Y/hour = $Z/month
- Cost of errors: X incidents × $Y loss = $Z/month
- Cost of delays: X days × $Y/day = $Z/month
- **Total current losses:** $___/month

### After Implementation
- Expected time savings: X hours/month
- Error reduction: by X%
- Process acceleration: X times faster
- Possible FTE reduction: X positions
- **Total monthly benefit:** $___/month

### Development and Ownership Costs
- CAPEX (development): $___
- Monthly OPEX:
  - Infrastructure: $___/month
  - LLM inference: X tokens/request × Y requests/day × $Z = $___/month
  - Support: $___/month
  - **Total OPEX:** $___/month

### ROI
- Payback period: ___ months
- ROI over 12 months: ___%
- Break-even point: ___

### Unit Economics (for SaaS/product)
- CAC (customer acquisition cost): $___
- LTV (lifetime value): $___
- LTV/CAC: ___ (norm > 3)
- Payback period: ___ months

---

# [D] SECTION 2. DOMAIN

**Applicability:**
- ✅ REQUIRED: 2.1, 2.3, 2.4, 2.5 — for all
- 📋 OPTIONAL: 2.2 — automation of existing processes
- ⬜ N/A: 2.2 — greenfield without existing processes

## 2.1 Domain Glossary ✅

*⚠️ Agent reads the glossary third — after Invariants (2.5) and Principles (2.6), before all other sections. See the canonical reading order.*

*Format for each entry:*
```markdown
## [Term]
Definition: [exact meaning in the context of the project]
Context: [where and how it is used]
Do not confuse with: [similar terms with different meanings]
In code: [variable / table / class name]
```

## 2.2 Business Processes 📋
*Applicable: automation of existing processes*

- Current processes (AS-IS) with pain points
- Target processes (TO-BE)
- Step-by-step description or BPMN diagram
- Responsible owner for each process

## 2.3 System Users ✅

*For each role:*
```
Role:                [name]
Who they are:        [description]
Actions in system:   [what they do]
What they value:     [speed / accuracy / simplicity / ...]
Pain points:         [what is hard now]
Frequency:           [daily / weekly / monthly]
Technical level:     [low / medium / high]
Expected count:      [expected number]
```

## 2.4 Constraints and Assumptions ✅
- Technical constraints (cannot be changed: legacy, platforms)
- Business constraints (budget, deadlines, team)
- Regulatory constraints
- Explicit assumptions
- **What we explicitly are NOT doing in this version** *(required)*

## 2.5 SYSTEM INVARIANTS ✅ [A]

*This is the most important section for agents.*
*Invariants are absolute constraints. Must never be violated under any circumstances.*
*The agent stops if the task requires violating an invariant.*

*Format — four fields, uniform across the entire standard:*

```
INV-001: [Name]
Cannot:  [specific forbidden action]
Reason:  [why this is an invariant]
Check:   [verifiable condition — how to confirm the invariant is upheld]

INV-002: [Name]
...
```

*Example invariants:*
```
INV-001: Atomicity of Financial Operations
Cannot:  change balance outside a transaction; partial execution is not allowed
Reason:  balance desynchronization = direct financial loss
Check:   all balance operations use SELECT FOR UPDATE + transactions

INV-002: API Backward Compatibility
Cannot:  remove or rename fields in public API responses
Reason:  existing clients would break without warning
Check:   API contract does not break existing clients

INV-003: Audit Log Immutability
Cannot:  modify or delete audit_log records; append only
Reason:  the audit log is legal evidence and a compliance requirement
Check:   no UPDATE/DELETE on the audit_log table

INV-004: User Data Preservation
Cannot:  delete user data without soft-delete and confirmation
Reason:  recovery from accidental deletion must be possible
Check:   all delete operations use deleted_at, not physical deletion
```

## 2.6 ARCHITECTURAL PRINCIPLES [A] ✅

*Principles are not invariants. An invariant can never be violated.*
*A principle is guidance for decision-making when requirements are insufficient.*
*The agent refers to principles at any point of uncertainty.*

```
AP-001: Simple > Clever
A simple solution that works beats a clever one that is hard to maintain.
When choosing between elegant and understandable — choose understandable.

AP-002: Monolith First
Start with a monolith. Microservices only when the monolith became a real problem.
Do not design for scale that doesn't exist yet.

AP-003: Buy Before Build
Check existing solutions first. Build only what the market doesn't have
or what is critical for product differentiation.

AP-004: Human Override Always Available
In any AI system, the human must be able to cancel an agent's decision.
No autonomous irreversible actions without human confirmation.

AP-005: No Vendor Lock-In Without Justification
Dependency on a specific provider only with explicit business justification.
LLM, cloud, services — through adapters if replacement is realistic.

AP-006: Fail Loud
An explicit error is better than silent incorrect operation.
The agent reports a problem immediately — does not suppress or work around it.

AP-007: Data Outlives Code
Code can be rewritten. Data cannot be lost.
Any decision affecting data requires special care.

AP-008: Boring Technology Wins
A proven stack beats something new and shiny.
Introduce new technology only if it solves a specific problem.

AP-009: YAGNI (You Aren't Gonna Need It)
Do not add functionality "for the future" without an explicit current need.
The agent does not implement anything not in the specification.

AP-010: Reversibility Over Efficiency
All else being equal — choose the solution that is easier to undo.
Irreversible decisions require additional confirmation.
```

*How to use:*
*When an agent faces a choice not described in the spec —*
*it checks the principles and makes a decision aligned with them.*
*If principles conflict — it stops and asks.*

*Principles are project-specific: remove inapplicable ones, add your own.*

---

# [D] SECTION 3. FUNCTIONAL REQUIREMENTS

**Applicability:** ✅ REQUIRED for all

## 3.1 Functionality Overview ✅
- Map of functional blocks
- Prioritization:
  - **MoSCoW** — most projects
  - **RICE** — product-led teams
  - **WSJF** — SAFe / large teams
- Implementation phases (if incremental)

## 3.2 User Stories ✅

```
ID: US-001
As [role]
I want [specific action]
So that [measurable result]

Acceptance Criteria:
- [ ] [Specific verifiable criterion]
- [ ] [Specific verifiable criterion]

Alternative scenarios:
- If [condition] → [system behavior]
```

## 3.3 Detailed Functional Requirements ✅

```
Module: [name]
Purpose: [one sentence]
Business value: [why it matters]

User Flow:
1. [User action]
2. [System response]
3. ...

Business Rules:
BR-001: [rule]
  Exceptions: [when it does not apply]
  Edge cases: [behavior at the boundary]
  Related invariants: [INV-XXX if applicable]

Input: [what it accepts]
Output: [what it returns]
Error handling: [what happens for each error]
Edge Cases: [boundary conditions → expected behavior]
```

## 3.4 Business Rules Register ✅

| ID | Rule | Exceptions | Priority | Module | Invariant |
|---|---|---|---|---|---|
| BR-001 | | | High/Med/Low | | INV-XXX |

## 3.5 Reporting Requirements 📋
*Applicable: analytics, dashboards*

- Reports: what, for whom, how often
- Export formats
- Data freshness

---

# [D+E] SECTION 4. NON-FUNCTIONAL REQUIREMENTS

**Applicability:**
- ✅ REQUIRED: 4.1, 4.2 — for all except Micro
- 📋 OPTIONAL: others — by type and scale

## 4.1 Performance ✅

| Metric | Normal | Peak Load |
|---|---|---|
| Response time p50 | < ms | < ms |
| Response time p95 | < ms | < ms |
| Response time p99 | < ms | < ms |
| Throughput | RPS | RPS |

- Behavior under 2x / 10x peak load

## 4.2 Reliability and Availability ✅

| Metric | Value |
|---|---|
| SLA (uptime) | 99.___% |
| RTO | min |
| RPO | min |

- Behavior under partial failure

## 4.3 Scalability 📋
*Applicable: SaaS, growing audience*

- Load growth: 6 months / 1 year / 3 years
- Bottlenecks to account for in advance

## 4.4 Observability — Requirements 📋
*Applicable: production Medium+. Tools — in section 8.6.*

- Metrics: golden signals (latency, traffic, errors, saturation)
- Logging: structured JSON, correlation ID required
- What is never logged: passwords, tokens, PII, payment data
- SLI/SLO/Error Budget:
  - SLI: what we measure
  - SLO: target value
  - Error Budget: 100% - SLO

## 4.5 Performance Budget 📋
*Applicable: frontend, AI systems*

**Frontend:**
- Lighthouse Performance > X
- LCP < Xs, CLS < X, INP < Xms
- Bundle size < XKB

**AI inference:**
- LLM response time p95 < Xs
- Tokens per request: no more than X
- Cost per request: no more than $X
- Monthly budget: no more than $X

## 4.6 Maintainability 📋
*Applicable: long-lived systems > 6 months*

- Test coverage: unit X%, integration X%
- API backward compatibility: semver policy

## 4.7 Usability 📋
- WCAG 2.1/2.2: A / AA / AAA
- Mobile version
- Localization: languages

---

# [E] SECTION 5. ARCHITECTURE AND TECHNOLOGY STACK

**Applicability:**
- ✅ REQUIRED: 5.1-5.5 — all except Micro
- Micro: only 5.2
- 📋 OPTIONAL: 5.6 — external integrations
- 📋 OPTIONAL: 5.7 — non-trivial architectural characteristics

## 5.1 Architectural Overview ✅
- Type: monolith / modular monolith / microservices / serverless / hybrid
- C4 Level 1 (System Context) + Level 2 (Container)
- Key architectural principles
- Priority of characteristics: reliability / performance / extensibility / simplicity

## 5.2 Technology Stack ✅

| Layer | Technology | Version | Rationale |
|---|---|---|---|
| Backend | | | |
| Frontend | | | |
| Primary DB | | | |
| Cache DB | | | |
| Queue | | | |
| ORM | | | |
| Auth | | | |
| Tests | | | |
| Linter | | | |

**External dependencies:**

| Service | Purpose | Criticality | Fallback |
|---|---|---|---|
| | | High/Med/Low | |

## 5.3 Data Models ✅
- ERD (conceptual model)
- Logical model: tables, fields, types, constraints
- Indexes with justification
- Expected volume and growth

## 5.4 API and Interfaces ✅
- Standard: REST / GraphQL / gRPC / WebSocket
- Versioning strategy
- Authentication: JWT / OAuth2 / API Key
- Unified error format:
  ```json
  { "error": "ERROR_CODE", "message": "human-readable", "details": {} }
  ```
- Pagination: cursor / offset
- Rate limiting policy

## 5.5 Architectural Decisions (ADR + Decision Log) ✅

*ADR — architectural decisions (why the architecture was chosen):*
```
ADR-001: [name]
Date: YYYY-MM-DD
Status: Accepted / Superseded

Context: [what required a decision]
Options: [A, B, C]
Decision: [what was chosen and why]
Trade-offs: [what we accept]
For agents: [operational implications]
```

*Decision Log — operational decisions (why a specific choice was made):*
```
DEC-001: [name]
Date: YYYY-MM-DD

Question: [what was being decided]
Decision: [what was chosen]
Reason: [why exactly this]
Alternatives rejected: [what and why not]
```

*Decision Log examples:*
```
DEC-001: Redis over Memcached
Reason: need persistent keys and pub/sub for notifications.
Rejected: Memcached — no persistence, no pub/sub.

DEC-002: Monolith over microservices
Reason: 2-person team, no point in distributed overhead.
Rejected: microservices — excessive for current scale.
```

## 5.6 Integrations 📋
*Applicable: external APIs, event streaming*

| Integration | Direction | Protocol | Auth | Criticality | Fallback |
|---|---|---|---|---|---|
| | In/Out | | | | |

- Circuit breaker conditions
- Retry policy: N attempts, exponential backoff

## 5.7 Architecture Fitness Functions 📋
*Applicable: Medium+ systems where verifiability of architectural characteristics matters*

*Fitness Functions — a way to verify architecture, not just describe it.*
*Not "the system should be scalable" but "handles 1000 RPS without degradation".*

```
FF-001: Performance under load
Verification: load test 1000 RPS for 10 minutes
Criterion: p95 < 200ms, error rate < 0.1%
When: before every release

FF-002: LLM dependency isolation
Verification: replacing LLM provider requires changes in at most 2 modules
Criterion: grep -r "openai" | grep -v "adapter" | wc -l == 0
When: when adding a new LLM call

FF-003: Build speed
Verification: full CI pipeline duration
Criterion: < 10 minutes
When: on every commit to main

FF-004: Test coverage
Verification: coverage report
Criterion: unit > 80%, integration > 60%
When: on every PR

FF-005: Adding a new integration
Verification: new payment provider requires changes only in the adapter layer
Criterion: no changes in the business logic layer
When: when adding an integration
```

---

# [A] SECTION 6. LLM AND AI COMPONENT REQUIREMENTS

**Applicability:**
- ✅ REQUIRED: 6.1-6.7 — core AI functionality
- 📋 OPTIONAL: 6.1-6.5 — AI as a supporting tool
- ⬜ N/A: entire section — no AI/LLM

## 6.1 AI Component Description ✅
- What LLM/AI solves in the system
- Justification: why AI and not a classical algorithm
- EU AI Act classification: Unacceptable / High / Limited / Minimal risk
- Risks of using AI

## 6.2 Model and Provider Selection 📋

| Parameter | Value |
|---|---|
| Model | |
| Provider | |
| Context window | |
| Fallback model | |
| Fallback provider | |

- Selection rationale
- Update policy when new versions are released

## 6.3 Agent Configuration and Prompts ✅
- System prompts: version, stored in Git
- Versioning: semver (MAJOR.MINOR.PATCH)
- Output determinism for agentic tasks: **structured outputs / JSON schema — the primary mechanism**; temperature 0.0–0.3 where the parameter is supported (some newer models ignore or reject it)
- **Temperature for creative tasks: 0.7–1.0** (where supported)
- AGENTS.md / CLAUDE.md — required artifact
- Forbidden Patterns for agents

## 6.4 Context Management ✅
- Order of injection into context *(matches the canonical reading order)*:
  1. System prompt / rules
  2. Invariants (Section 2.5)
  3. Principles (Section 2.6)
  4. Domain glossary (Section 2.1)
  5. Relevant task context
  6. History (if needed)
  7. Current request
- Strategy when context is exhausted
- Persistent memory (if needed): Mem0 / Zep / Letta

## 6.5 Multi-Agent Architecture 📋
- Pattern: Orchestrator / Pipeline / Router / Parallel
- Team composition and roles
- Communication protocols
- Isolation and boundaries of responsibility

## 6.6 Guardrails and AI Safety ✅
- Absolute prohibitions for agents
- Input validation: what is rejected
- Output validation: what is checked before showing to the user
- Prompt injection protection
- Data leakage prevention

## 6.7 AI Output Quality and Evaluation ✅

| Metric | Description | Target |
|---|---|---|
| Faithfulness | Answer based on sources | > X% |
| Groundedness | Every fact confirmed | > X% |
| Correctness | Correct answer | > X% |
| Safety score | No harmful content | > X% |
| Latency p95 | Response time | < Xs |
| Cost per query | Request cost | < $X |

- Eval framework: tool
- Human-in-the-loop: where confirmation is needed

## 6.8 AI Monitoring in Production 📋
- Drift detection: data and output quality
- Human override rate: % of AI decisions reversed
- A/B testing of models and prompts

## 6.9 Inference Cost 📋
- Estimate: tokens/request × DAU × 30 = monthly spend
- Per-user limits
- Budget alerts
- Optimization: prompt caching, batching, right-sizing

## 6.10 Knowledge Management (RAG) 📋
- Knowledge sources and owners
- Update and quality verification process
- Knowledge base versioning

## 6.11 Prompt Versioning 📋
- Prompts in Git as code
- Semver: MAJOR (> 20% behavior change) / MINOR (new, compatible) / PATCH (fix)
- Eval on every change
- Rollback procedure

---

# [E] SECTION 7. SECURITY

**Applicability:**
- ✅ REQUIRED: 7.1-7.5 — all production systems with external users
- 📋 OPTIONAL: 7.6-7.10 — by type
- ⬜ N/A: 7.8 Pentest — Micro/Small internal tools
- ⬜ N/A: 7.10 — no AI components

## 7.1 Authentication and Authorization ✅
- Methods: password / OAuth2 / SSO / MFA / Passkeys
- Model: RBAC / ABAC / ACL
- Permissions matrix (role × operation × resource)
- Sessions: TTL, invalidation, refresh token rotation

## 7.2 Data Protection ✅
- Encryption at rest: algorithm, key management
- Encryption in transit: TLS 1.3 required
- Classification: Public / Internal / Confidential / Secret
- Masking of sensitive data
- Retention and deletion policy

## 7.3 Application Security ✅

*OWASP Top 10 — verify each item (see owasp.org for the current version):*

| Vulnerability | How it is addressed |
|---|---|
| A01 Broken Access Control | ownership check on every endpoint |
| A02 Cryptographic Failures | see 7.2 |
| A03 Injection | parameterized queries, escaping |
| A04 Insecure Design | threat modeling |
| A05 Security Misconfiguration | hardening checklist |
| A06 Vulnerable Components | dependency scanning |
| A07 Auth Failures | see 7.1 |
| A08 Software Integrity | SBOM, artifact signing |
| A09 Logging Failures | see 7.4 |
| A10 SSRF | outbound request whitelist |

- Server-side validation: required regardless of frontend
- CSRF: SameSite=Strict or tokens
- Rate limiting and brute force protection

## 7.4 Audit and Logging ✅

```json
{
  "timestamp": "ISO8601",
  "event_type": "string",
  "user_id": "uuid",
  "resource": "string",
  "action": "string",
  "result": "success|failure",
  "ip": "string",
  "correlation_id": "uuid"
}
```

- Retention: minimum X months
- Protection: append-only, no UPDATE/DELETE
- What is never logged: passwords, tokens, PII, payment data

## 7.5 Secrets Management ✅
- Storage: Vault / AWS Secrets Manager / equivalent
- Never: in code, in config, in env without encrypted storage
- Rotation policy
- Access: least privilege

## 7.6 Network Security 📋
- Segmentation (VPC, subnets)
- WAF: detection / prevention mode
- DDoS protection

## 7.7 Vulnerability Management 📋
- Dependency scanning: tool
- Patch SLA: Critical 24h / High 7d / Medium 30d / Low next release

## 7.8 Penetration Testing 📋
*Applicable: public services, financial/medical data, Large+*
- Frequency + scope + remediation procedure

## 7.9 Supply Chain Security 📋
*Applicable: Medium+ with CI/CD*
- SBOM: format SPDX / CycloneDX
- SLSA level (1-4)
- Whitelist of allowed licenses

## 7.10 AI Security 📋
*Applicable: systems with LLM/AI*
- Prompt injection: input sanitization
- Jailbreak mitigation: guardrails, output filtering
- Data leakage: what never enters a prompt
- Backdoor detection: relevant for self-hosted + fine-tuning; ⬜ N/A for cloud APIs

---

# [E] SECTION 8. INFRASTRUCTURE

**Applicability:**
- ✅ REQUIRED: 8.1-8.5 — all production systems
- 📋 OPTIONAL: 8.6-8.10 — by scale
- ⬜ N/A: entire section — prototypes without deployment

## 8.1 Environments ✅

| Environment | Purpose | Data | Access |
|---|---|---|---|
| Development | development | synthetic | team |
| Staging | testing | anonymized | team + QA |
| Production | production | real | restricted |

## 8.2 Cloud and Hosting ✅
- Provider + justification
- Region(s): considering data jurisdiction (GDPR!)
- Multi-AZ requirements

## 8.3 Containerization 📋
- Docker: base images, update policy
- Orchestration: Kubernetes / ECS
- Resource limits CPU/RAM

## 8.4 CI/CD ✅
- Pipeline: branches → review → tests → deploy
- Checks: lint, test, security scan, SBOM
- Deploy strategy: Blue/Green / Canary / Rolling
- Rollback: triggers and procedure

## 8.5 Backup and DR ✅

| Data type | Backup | Retention | RTO | RPO |
|---|---|---|---|---|
| DB | | | | |
| Files | | | | |

- DR strategy: Single AZ / Multi-AZ / Multi-Region
- DR testing: frequency

## 8.6 Monitoring — Tools 📋
*Requirements in 4.4. Here — specific tools.*

| Task | Tool |
|---|---|
| Metrics | |
| Logs | |
| Tracing | |
| Alerts | |
| Dashboards | |

- On-call matrix and escalation policy

## 8.7 Infrastructure as Code 📋
- Terraform / Pulumi / CloudFormation
- State management: remote state, locking

## 8.8 FinOps 📋
*Applicable: cloud with significant costs, AI products*
- Tagging strategy (required for cost allocation)
- Budget alerts: 50% / 80% / 100%
- AI optimization: prompt caching, batching, right-sizing

## 8.9 Progressive Delivery 📋
*Applicable: Large with high-frequency deployments*
- Feature flags: tool and policy
- Canary: traffic %, metrics, automatic rollback

## 8.10 Disaster Recovery 📋
*Applicable: High/Critical*
- Strategy: Pilot Light / Warm Standby / Active-Active
- Failover: automatic / manual
- DR drill: frequency and success criteria

---

# [E] SECTION 9. DATA

**Applicability:**
- ✅ REQUIRED: 9.1, 9.3 — non-trivial data work
- 📋 OPTIONAL: others — by scale
- ⬜ N/A: entire section — simple CRUD

## 9.1 Data Governance ✅
- Data owner for each type
- Classification: Public / Internal / Confidential / Secret
- Data lineage: source → transformations → storage → deletion
- Quality metrics: completeness, accuracy, timeliness

## 9.2 Data Contracts 📋
*Applicable: microservices, multiple teams*
- Schema Registry: tool
- Schema versioning
- Breaking change procedure

## 9.3 Storage and Archiving ✅

| Data type | Retention | Archiving | Deletion | Jurisdiction |
|---|---|---|---|---|
| | | | | |

## 9.4 Data Migration 📋
- Strategy: Big Bang / Incremental / Parallel Run
- Validation and rollback plan

## 9.5 Synthetic Data 📋
*Applicable: AI systems, privacy-sensitive*
- Requirements for generation and alignment with real distributions

---

# [D] SECTION 10. COMPLIANCE

**Automatic applicability determination:**
```
Personal data EU/UK → 10.1 REQUIRED
Payment cards       → 10.2 PCI DSS REQUIRED
Medical data (US)   → 10.2 HIPAA REQUIRED
System uses AI      → 10.3 REQUIRED
Open source deps    → 10.4 REQUIRED
```

## 10.1 Personal Data Protection ✅/⬜

*REQUIRED: personal data of EU, UK, CA and other jurisdiction residents*

- Legal bases: consent / legitimate interest / contract / legal obligation
- Categories of personal data
- DPIA: required or not
- Data subject rights:

| Right | Article | How implemented | Deadline |
|---|---|---|---|
| Access | Art. 15 | | 30 days |
| Rectification | Art. 16 | | 30 days |
| Erasure | Art. 17 | | 30 days |
| Portability | Art. 20 | | 30 days |
| Objection | Art. 21 | | without delay |

- Consent: mechanism, timestamp, withdrawal
- Third-party transfers: legal basis, DPA
- Retention by data type
- Breach notification: 72-hour window
- Applicable laws: GDPR / UK GDPR / CCPA / PDPA / others

## 10.2 Industry Standards 📋
*(fill in applicable ones)*

**PCI DSS** — if payment cards:
compliance level (1-4), scope, key requirements

**HIPAA** — if US medical data:
PHI categories, BAA with contractors, Technical Safeguards

**SOC 2** — if required by enterprise client:
Trust Service Criteria, audit scope

## 10.3 AI Regulations 📋
*Applicable: systems with AI/LLM*
- EU AI Act: risk classification and required measures
- Transparency: user knows they are interacting with AI
- ISO/IEC 42001: for organizations using AI

## 10.4 Licensing and IP 📋
- Registry of open source components and licenses
- License compatibility
- Rights to data for AI training
- External API ToS: what can be done with output

## 10.5 Accessibility 📋
*REQUIRED: government systems, public systems in EU/US*
- WCAG 2.1/2.2: A / AA / AAA

## 10.6 Legal Requirements 📋
- Applicable law and jurisdiction
- Contract requirements
- Documentation retention

## 10.7 Sustainability 📋
*Applicable: Large+, government projects, ESG reporting*
- Carbon footprint assessment
- AI: prefer efficient models at equal quality

## 10.8 DSA / DMA 📋
*Applicable: platforms > 45M users in EU*
- VLOP / VLSE classification and applicable requirements

---

# [E] SECTION 11. TESTING AND QUALITY

**Applicability:** ✅ REQUIRED for all

## 11.1 Testing Strategy ✅

| Level | Tool | Coverage | Who | When |
|---|---|---|---|---|
| Unit | | X% | developer | when writing |
| Integration | | X% | developer | when writing |
| E2E | | X scenarios | QA | before release |
| Performance | | NFR from 4.1 | | before release |
| Security | | OWASP | | at launch + periodically |

## 11.2 Acceptance Criteria ✅

```
✅ Correct:
"POST /login with valid credentials → HTTP 200 + JWT in < 200ms"
"After 5 attempts → HTTP 429, block for 15 min"

❌ Incorrect:
"Authorization works correctly"
"System is protected against brute force"
```

## 11.3 Test Cases ✅

*Must cover:*
- Happy path for each US
- Negative scenarios (each error code)
- Edge cases and boundary values
- Concurrent requests (financial operations)
- Invariant verification (INV-XXX)

```
TEST-001: [name]
Given:     [precondition]
When:      [action]
Then:      [expected result]
DB:        [what to check in the database]
Invariant: [INV-XXX if verified]
```

## 11.4 AI Testing 📋
- Eval framework and metrics (see 6.7)
- Regression suite on every prompt change
- Degradation criterion: at what threshold we block deployment

## 11.5 Regression Testing ✅
- Smoke test suite (< 5 minutes)
- What to verify after each change
- Invariants included in smoke tests

## 11.6 UAT 📋
- Participants, success criteria, issue procedure

---

# [E] SECTION 12. INTERFACES

## 12.1 User Interface 📋
- Design system / component library
- States: Loading / Empty / Error / Success — for each component
- Validation: rules + when to show (on blur / on submit)
- Responsiveness: breakpoints

## 12.2 API ✅ (if applicable)
- OpenAPI 3.x — required, living document
- Unified error format (see 5.4)
- All HTTP codes with descriptions
- Versioning strategy

## 12.3 Integration Interfaces 📋
- Inbound / outbound: format, protocol, auth
- Webhooks: HMAC signature, retry policy

---

# [E] SECTION 13. DEPLOYMENT AND HANDOFF

## 13.1 Deployment Strategy ✅
- Stages: Alpha → Beta → GA
- Transition criteria
- Rollback triggers

## 13.2 User Documentation 📋
- Formats: documentation / video / training

## 13.3 Technical Documentation ✅
- Code: documentation standards
- OpenAPI: current (auto-generated)
- Runbook: typical operations and incidents
- ADR + Decision Log — living documents
- Living Specification (SDD) — living documents

## 13.4 Client Handoff 📋
- Source code and rights
- Warranty period: terms, scope, SLA

---

# [D] SECTION 14. PROJECT MANAGEMENT

**Applicability:** ✅ REQUIRED for all

## 14.1 Decomposition ✅
- Modules / epics / phases
- Dependencies (what blocks what)
- Critical path
- What is parallel

## 14.2 Prioritization and Scope ✅
- MVP: what is included in the first working version
- What is explicitly NOT included
- Roadmap (if multi-phase)

## 14.3 Risk Register ✅

| ID | Risk | Type | Probability | Impact | Mitigation | Owner |
|---|---|---|---|---|---|---|
| R-001 | | Tech/Biz/Reg | H/M/L | H/M/L | | |

## 14.4 Assumptions and Dependencies ✅
- Assumptions the plan is based on
- External dependencies
- What is outside the team's control

## 14.5 Success Criteria ✅
- Project-level Definition of Done
- KPIs at 3/6/12 months after launch
- Final acceptance procedure

---

# [A] SECTION 15. AI-FIRST DEVELOPMENT (SDD)

**Applicability:**
- ✅ REQUIRED: all subsections — development with AI agents
- ⬜ N/A: entire section — humans only without AI tools

## 15.1 Agent Configuration ✅

**Required artifacts:**
```
docs/specs/
├── PROJECT_BRIEF.md    essence, stack, constraints
├── VISION.md           principles and priorities
├── GLOSSARY.md         terms (read first)
├── ARCHITECTURE.md     current architecture
├── AGENTS.md           agent team
└── modules/            module specifications
.claude/CLAUDE.md       global rules
.cursorrules            rules for Cursor
```

**Rules hierarchy:**
```
1. CLAUDE.md        highest priority
2. AGENTS.md        agent role
3. MODULE_SPEC      current task
4. Session prompt   operational instructions

Lower level NEVER overrides upper level.
Contradiction detected → report explicitly.
```

**Project Forbidden Patterns:**
*(fill in specifics)*

## 15.2 Context Management ✅

**Order of injection to agent** *(matches the canonical reading order)*:
1. CLAUDE.md (global rules)
2. Invariants (Section 2.5)
3. Principles (Section 2.6)
4. GLOSSARY.md
5. ARCHITECTURE.md
6. MODULE_SPEC (current task)
7. SESSION.md / current state

**Context recovery protocol after a break:**
```
1. PROJECT_BRIEF.md
2. ADR.md + Decision Log
3. PROJECT_STATUS.md
4. MODULE_SPEC of current task
5. Agent summarizes understanding → human confirms
```

## 15.3 Fail-Fast Conditions ✅

```
AGENT STOPS AND ASKS when:
□ Choosing between multiple architectural approaches
□ Conflicting requirements
□ Task outside the described specification
□ Changing a public API or database schema
□ Solution violates any INVARIANT (Section 2.5)
□ Proposed solution significantly increases complexity
  without explicit business justification (YAGNI violation)

AGENT ACTS INDEPENDENTLY when:
□ Implementation details with no external effects
□ Minor naming or formatting decisions
□ Decisions clearly following from existing patterns
```

## 15.4 Multi-Agent Development 📋

- Team: Orchestrator / Architect / Backend / Frontend / DB / QA / Reviewer
- Task Transfer Template (template for handing a task between agents)
- Interface agreement protocol before implementation
- PROJECT_STATUS.md — Orchestrator maintains, updates after each subtask

## 15.5 Living Specification ✅

- Specification is updated in the same iteration as the code
- Who updates: human (agent proposes → human accepts)
- Code change without spec update = technical debt
- Versioned in Git alongside code

## 15.6 Agent Review Specification ✅

*Required step BEFORE writing any code.*
*The agent reviews the spec — not the code.*

```
Agent checks:
□ Contradictions between spec sections
□ Gaps (situations not described that will definitely arise)
□ Edge cases missing from the specification
□ Missing Acceptance Criteria
□ Conflicts with INVARIANTS (Section 2.5)
□ Requirements that are technically unrealistic

If > 3 problems found:
→ Stop and report BEFORE writing code
→ Do not guess, do not fill gaps independently

Prompt for Agent Review:
"Read the spec and find:
1. Contradictions between requirements
2. Situations not described but that will arise
3. Missing Acceptance Criteria
4. Edge cases absent from the specification
5. Conflicts with invariants from Section 2.5
Do NOT propose solutions — only list problems."
```

## 15.7 Spec Quality Checklist ✅

*Agent goes through this before handoff. > 3 items not completed → revise.*

```
CONTEXT AND GOAL
□ Goal is written so that WHY is clear, not just WHAT
□ All domain terms are in GLOSSARY.md
□ Spec does not contradict ARCHITECTURE.md
□ System invariants explicitly listed (Section 2.5)

FUNCTIONAL REQUIREMENTS
□ Full user flow described — happy path + all deviations
□ All business rules explicit, no "obviously..." assumptions
□ System behavior described for each expected error
□ Edge cases listed with expected behavior

TECHNICAL REQUIREMENTS
□ API Contracts: all endpoints + all error codes + formats
□ Data Models described — agent makes no decisions on its own
□ NFR are specific and measurable

UI (if applicable)
□ All 4 states described: Loading / Empty / Error / Success
□ Validation: rules + when to show errors

ACCEPTANCE CRITERIA
□ Each criterion is unambiguously verifiable
□ All requirements covered by at least one criterion
□ Criteria for invariant verification are present

COMPLETENESS
□ What is NOT in scope — written explicitly
□ Dependencies between modules are explicit
□ Compliance defined (or explicitly N/A)
□ Forbidden Patterns written

QUALITY
□ No contradictions between sections
□ Agent Review performed (Section 15.6)
□ Decision Log filled for non-trivial decisions
```

---

# [E+A] SECTION 16. CHANGE SPECIFICATION

*A separate format for changes to an existing system.*
*Used instead of the standard MODULE_SPEC when the product is already in production.*

**Applicability:**
- ✅ REQUIRED: for any change to a production system
- ⬜ N/A: for new systems (use MODULE_SPEC)

## 16.1 Change Specification Template ✅

```markdown
# CHANGE_SPEC: [Change name]

ID: CHG-001
Date: YYYY-MM-DD
Priority: Critical / High / Medium / Low
Agent / Executor: [who implements]
Related modules: [list]
Related invariants: [INV-XXX affected]

---

## Current State
[How it works right now — without evaluating whether it is correct]
[Specifically: which files, which logic, which API]

## Desired State
[How it should work after the change]
[Specifically: what changes in behavior, in API, in data]

## What We Are NOT Changing
[Explicit list of what must remain untouched]
- [File / module / endpoint] — do not touch
- [File / module / endpoint] — do not touch

## Impact Analysis
- Affected components: [list of files, modules, services]
- Affected users: [who will feel the change]
- Risks: [what might break and why]
- Dependent systems: [what depends on the changed part]

## Invariant Verification
[For each affected invariant — how to confirm it is not violated]
- INV-XXX: [how we verify]

## Rollback Strategy
- Rollback trigger: [under what condition we roll back]
- Rollback procedure: [specific steps]
- Rollback time: [how long it will take]
- Post-rollback verification: [how to confirm rollback succeeded]

## Acceptance Criteria
- [ ] [New behavior works]
- [ ] [Old behavior is not broken]
- [ ] [Invariants upheld]
- [ ] [Regression tests passed]

## Regression Checks
After the change, verify:
- [ ] [Scenario 1 that might have broken]
- [ ] [Scenario 2 that might have broken]
- [ ] Full smoke test suite
```

---

# [E+A] SECTION 17. SUPPORT AND OPERATIONS

**Applicability:**
- ✅ REQUIRED: production systems with SLA
- 📋 OPTIONAL: internal tools
- ⬜ N/A: prototypes

## 17.1 SLA / SLO / SLI ✅

| Metric (SLI) | Target (SLO) | Error Budget/month |
|---|---|---|
| Availability | 99.X% | |
| Latency p95 | < Xms | |
| Error rate | < X% | |

## 17.2 Incidents ✅

| Priority | Criterion | Response | Resolution |
|---|---|---|---|
| P0 Critical | system unavailable | X min | X hours |
| P1 High | critical function | X hours | X hours |
| P2 Medium | degradation | X hours | X days |
| P3 Low | minor defect | X days | X days |

- Post-mortem: when required, template

## 17.3 Deprecation Policy 📋
- Minimum notice period: X months
- Communication: channels and formats

## 17.4 Exit Strategy 📋
*Applicable: commercial SaaS*
- Data export: formats, SLA
- Service termination: notice period
- Data deletion: procedure and confirmation

---

# APPENDICES

## A. Full Domain Glossary
*(separate file GLOSSARY.md)*

## B. Diagrams
- C4 Level 1 (System Context) + Level 2 (Container)
- ER diagram
- Sequence diagrams for key scenarios
- BPMN diagrams (if applicable)

## C. API Contracts
*(link to openapi.yaml)*

## D. Interface Mockups
*(links to Figma / Miro)*

## E. Requirements Traceability Matrix

| Requirement ID | Design | Test | Code | Invariant | Status |
|---|---|---|---|---|---|
| REQ-001 | | | | | |

## F. SDD Templates
*(link to Appendix A of "AI-First Specifications")*

## G. Approval History
*(signatures if required)*

---

# QUICK START — CORE SPECIFICATION

*For Micro and Small projects, Low/Medium criticality.*

```
0.1  Title page
0.2  Level: CORE
0.4  Glossary and conventions
1.1  Business context (brief)
1.2  Problem and goal
1.3  Success criteria
1.5  Economics (if automation)
2.1  Domain glossary
2.3  System users
2.4  Constraints and scope
2.5  System invariants (3-5 key ones)
3.1  Functional blocks
3.2  User Stories with Acceptance Criteria
3.3  Detailed requirements
4.1  Performance
4.2  Availability
5.2  Technology stack
7.1  Authentication (if applicable)
7.2  Data protection (if applicable)
11.2 Acceptance Criteria
14.1 Decomposition
14.3 Risks (top 3)
15.1 Agent configuration
15.3 Fail-Fast conditions
15.6 Agent Review (required)
15.7 Quality checklist
```

---

## APPLICABILITY TABLE BY PROJECT TYPE

| Section | Micro-bot | SaaS | Enterprise | AI product |
|---|---|---|---|---|
| 0. Metadata | ✅ | ✅ | ✅ | ✅ |
| 1. Context | ✅ | ✅ | ✅ | ✅ |
| 1.5 Economics | 📋 | ✅ | ✅ | ✅ |
| 2. Domain + Invariants | ✅ | ✅ | ✅ | ✅ |
| 3. Functionality | ✅ | ✅ | ✅ | ✅ |
| 4. NFR | 📋 | ✅ | ✅ | ✅ |
| 5. Architecture | 📋 | ✅ | ✅ | ✅ |
| 5.7 Fitness Functions | ⬜ | 📋 | ✅ | ✅ |
| 6. LLM/AI | ⬜ | 📋 | 📋 | ✅ |
| 7. Security | 📋 | ✅ | ✅ | ✅ |
| 8. Infrastructure | 📋 | ✅ | ✅ | ✅ |
| 9. Data | ⬜ | 📋 | ✅ | ✅ |
| 10. Compliance | GDPR | GDPR+ | ✅ | ✅ |
| 10.7-10.8 Sus/DSA | ⬜ | ⬜ | 📋 | 📋 |
| 11. Testing | 📋 | ✅ | ✅ | ✅ |
| 12. Interfaces | ✅ | ✅ | ✅ | ✅ |
| 13. Handoff | ⬜ | 📋 | ✅ | 📋 |
| 14. Management | ✅ | ✅ | ✅ | ✅ |
| 15. SDD/AI-First | ✅ | ✅ | ✅ | ✅ |
| 16. Change Spec | 📋 | ✅ | ✅ | ✅ |
| 17. Operations | ⬜ | ✅ | ✅ | ✅ |

---

*Version 1.4 — Artem Kholomyanskiy — 2026*
*Next scheduled revision: in 6 months*
*Based on: GOST 34, IEEE 830, ISO/IEC 29148/25010/42001/42005,*
*EU AI Act, GDPR, OWASP, C4 Model, Shape Up, ADR, SDD*
