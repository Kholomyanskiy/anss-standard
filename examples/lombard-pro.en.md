> *Translated from Russian. Original: [lombard-pro.md](lombard-pro.md)*
> *Anonymized real-world example of a full ANSS-Core specification. External reviews: 9.2–9.4/10*

# ANSS-CORE — LombardPRO
## Pawnshop Network Management System

**Template version:** 1.0 (ANSS-Core)
**Level:** CORE

---

# SECTION 0. METADATA

| Field | Value |
|---|---|
| Project | LombardPRO |
| Version | 0.2 Draft |
| Date | 2026-06-01 |
| Status | Draft — pending approval |
| Author | Artem Kholomyanskiy |
| Client | ___ (to be filled after signing) |
| Contractor | Artem Kholomyanskiy + Claude Code |
| Language | English |

**Version history:**

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-05 | First draft (lombard_FULL_TZ.md, Ukrainian) |
| 0.2 | 2026-06-01 | Reworked to ANSS-Core standard |

**Detail level:** CORE

---

# SECTION 1. CONTEXT AND ECONOMICS

## 1.1 Project Summary

**What we are building:**
An operating system for a network of 3 pawnshops — replaces Excel in employees' daily work and gives the owner a consolidated real-time view of the network via Telegram.

**Problem we are solving:**
The network manages operations manually in Excel: the owner has no consolidated network view, there is no tracking of foreclosed items in transit, and there is a real risk of staff abuse (cash theft, scale manipulation, "ghost" redemptions).

**Why now:**
The business is growing (3 branches), and manual control does not scale. Every foreclosed item represents frozen money with no tracking; the risk of losses increases every month.

**What we are NOT doing in this version:**
- Not replacing PawnExpert — it remains the primary software for pledge registration (NBU / State Financial Services)
- Not duplicating client contract management
- Not connecting to PawnExpert via API (it has none) — we read exported .xls files
- Not migrating archived Excel data — the system starts from the current moment
- Not building a mobile app (PWA/native) — mobile web only

## 1.2 Goals and Success Criteria

**Business goals:**
- The owner knows the status of every foreclosed item in real time (not once a week via Excel)
- Daily cash reconciliation is done with one button press, not manually
- Every abuse — cash theft, underweighting at purchase, "ghost" redemption — leaves a trace in the system

**KPIs — how we know it's a success:**
- The owner receives a morning summary every day at 9:00 (target: 100% of days)
- Cash reconciliation time: < 3 minutes per day (currently ~30 minutes)
- No foreclosed item "goes missing" — 100% status tracking
- Employees switch to the system and stop maintaining Excel within 2 weeks of launch

**Project Definition of Done:**
1. All 4 modules are deployed and running in production
2. All 3 branches have uploaded real .xls files and receive the morning summary
3. Employees have completed online training (up to 1 hour)
4. First 2 weeks of operation without critical bugs

## 1.3 Economics

**Development cost:**

*Amounts removed from the public version. Cost structure: development CAPEX + hosting OPEX ≈ $10/mo.*

**Operating expenses after deployment:**
- Railway (backend hosting): $0–10/month
- Supabase (PostgreSQL + Storage): $0/month (free tier)
- Netlify (frontend): $0/month
- **Total OPEX: ≈ $10/month**

**Current losses (before the system):**
- TODO: clarify with client — hours of manual work × employee rate
- TODO: estimate the cost of a single abuse incident

**ROI:** not calculated — no client data available. To be clarified during approval.

---

# SECTION 2. DOMAIN

## 2.1 Glossary

**Pledge**
Definition: an item (jewelry, electronics) handed over by a client to the pawnshop in exchange for a loan. Registered in PawnExpert.
Do NOT confuse with: "purchase" — where the client sells the item outright.
In code: table `pledges`, status `active`.

**Foreclosure (Incasso)**
Definition: the transfer of a pledge onto the pawnshop's balance when the client does not return before the contract expiry date.
Do NOT confuse with: "redemption" — when the client retrieves the item by paying the debt.
In code: status change `pledges.status`: `active` → `on_branch`. The event from which item movement tracking begins.

**Pledge Portfolio**
Definition: the calculated total of all active pledges for a branch = `SUM(loan_amount) WHERE status='active' AND branch_id=X`. Not stored as a field — computed dynamically.
Do NOT confuse with: actual cash. The portfolio represents money "in play" with clients.
In code: computed aggregate, not a column.

**Branch Wallet (P/O)**
Definition: physical cash in the branch till.
Do NOT confuse with: "Personal" — money held by the owner as an individual.
In code: `cash_wallet` with type `branch`.

**Personal Wallet**
Definition: money held by the owner as an individual. Used to redeem overdue pledges in the owner's personal name, to avoid holding gold on the legal entity's balance (tax optimization).
In code: `cash_wallet` with type `personal`.

**Purchase (Skupka)**
Definition: a transaction where the client sells an item to the pawnshop outright (not a pledge).
Do NOT confuse with: "foreclosure" (unredeemed pledge).
In code: table `purchases`.

**Frozen Funds**
Definition: the total redemption value of all foreclosed items = `SUM(redemption_amount) WHERE status IN ('on_branch','hold')`. Money is invested; revenue not yet received.
In code: computed aggregate.

**Buyer (Skupshchik)**
Definition: an external buyer of foreclosed items (Buyer A — gold, Buyer B — silver). After dispatch, payment confirmation is expected.
In code: table `buyers`.

**Pledge Rate**
Definition: the daily interest rate (%) charged on the pledge amount from the date of issuance. Sourced from PawnExpert.
In code: `pledges.interest_rate`.

## 2.2 Users

**Role: Owner**
- Who: the network owner, 1 person
- What they do in the system: receives the morning summary, confirms the routing of each foreclosed item, confirms payments from buyers, views analytics in the web dashboard
- What they value: fast decision-making, control
- Expected count: 1
- Usage frequency: daily (Telegram), weekly (dashboard)
- Channels: Telegram bot + web dashboard (desktop)

**Role: Employee (Cashier)**
- Who: branch employee, 2 people per branch × 3 branches = 6 people
- What they do in the system: records cash transactions, registers foreclosures, purchases, sales, opens/closes shifts
- What they value: simplicity, fast input from mobile
- Expected count: 6
- Usage frequency: daily
- Sees: only their own branch

**Role: Administrator**
- Who: the owner or a technical person (same as owner at launch)
- What they do in the system: adds/deactivates employees, configures reference data (branches, buyers, price ranges, bonus rates), PawnExpert column mapping
- Expected count: 1
- Usage frequency: monthly

## 2.3 Constraints and Assumptions

**Technical constraints (cannot change):**
- PawnExpert has no API — integration only via .xls export
- Telegram API is used as a transport layer — dependent on its availability
- Free tiers of Supabase/Railway — no guaranteed SLA from the provider

**Business constraints:**
- Budget: removed from public version
- Deadline: none hard
- Team: Artem Kholomyanskiy + Claude Code

**Assumptions:**
- The client has stable internet in each branch
- Employees use a smartphone with Chrome/Safari daily
- The client will provide a real .xls file from PawnExpert before Phase 1 starts (critical prerequisite)
- Number of branches — 3 (expansion = Change Request)

## 2.4 Technology Stack

| Layer | Technology | Version |
|---|---|---|
| Backend | Python + FastAPI | Python 3.11+ |
| Frontend | React (or Vanilla JS) | React 18 |
| Database | PostgreSQL | Supabase managed |
| Photo storage | Supabase Storage | — |
| ORM | SQLAlchemy or asyncpg | — |
| Telegram bot | python-telegram-bot | 20.x |
| .xls parser | openpyxl / xlrd | — |
| Tests | pytest | — |
| Backend deploy | Railway | — |
| Frontend deploy | Netlify | — |

**External dependencies:**

| Service | Purpose | Fallback if unavailable |
|---|---|---|
| Telegram API | Owner notifications, employee photo uploads | Web dashboard (data is saved, notification sent later) |
| Supabase | DB + Storage | Railway backup pg_dump |
| PawnExpert | Source of pledge data | Employee enters manually (field `source='manual'`) |

## 2.5 INVARIANTS ✅

```
INV-001: Photo is mandatory for foreclosure
Cannot: save a foreclosure record without an attached photo of the item.
Reason: the photo is the only proof that the item exists and was accepted in the required condition.
Check: form/bot cannot be submitted without a photo file_id. Backend rejects requests without photo_url.

INV-002: Pledge uniqueness
Cannot: create two pledges records with the same contract_num + branch_id.
Reason: duplication = double accounting, distortion of the Portfolio.
Check: UNIQUE constraint on (contract_num, branch_id) in the DB.

INV-003: Operations cannot be deleted
Cannot: delete any recorded operation (cash, foreclosure, purchase, sale, shift).
Reason: the audit log must be complete and immutable.
Check: no DELETE endpoints for operations. Only the flag is_active=false (soft delete) with a record of who deactivated it and why.

INV-004: Item routing confirmed by owner only
Cannot: change the status of a foreclosed item to a routing destination (→ buyer / → personal account / → [City] / → shop) without the owner pressing a button in Telegram.
Reason: abuse prevention — employees do not decide where an item goes.
Check: changing status to a routing status requires owner_approved=true in the pledges table.

INV-005: Purchase price within range
Cannot: complete a metal purchase at a price per gram outside the configured range without owner confirmation.
Reason: protection against employees underpricing (pocketing the difference).
Check: backend verifies price_per_gram ∈ [min_price, max_price] from the reference table. If not — request is blocked until owner_approval_token is received.
```

## 2.6 ARCHITECTURAL PRINCIPLES ✅

```
AP-001: Simple > Clever
  — Vanilla JS instead of complex React if it keeps deployment simpler

AP-002: Monolith First
  — One FastAPI service, do not split into microservices

AP-003: Buy Before Build
  — Supabase, Railway, Netlify — do not run your own PostgreSQL/S3

AP-004: Human Override Always Available
  — The owner can always manually change a status via the admin panel

AP-005: No Vendor Lock-In Without Justification
  — Data in PostgreSQL (standard SQL), photos in Supabase Storage with migration capability

AP-006: Fail Loud
  — .xls parse errors — explicit message with details, not silent skipping

AP-007: Data Outlives Code
  — Soft delete everywhere. Nothing is lost permanently.

AP-008: YAGNI
  — Do not build for a hypothetical 100 branches — the system is for 3.

AP-009: Mobile First
  — The employee web app is designed for phones (Chrome/Safari).
  — Desktop is secondary for employees, primary for the owner's dashboard.

AP-010: One Source of Truth per Entity
  — The pledge portfolio is computed from pledges, not stored separately.
  — Item status — one field pledges.status, not duplicated.
```

---

# SECTION 3. FUNCTIONAL REQUIREMENTS

## 3.1 Feature Overview

**Functional modules:**
- **Module 1 — Telegram bot for the owner**: Must Have
- **Module 2 — Web application for employees**: Must Have
- **Module 3 — Admin panel**: Must Have (simplified)
- **Module 4 — Web dashboard for the owner**: Should Have

**Implementation phases:**
- Phase 1 (2–3 weeks): Telegram bot MVP — morning summary, alerts, basic commands
- Phase 2 (1–2 weeks): Foreclosure in bot + item tracking + buyer confirmation
- Phase 3 (3–4 weeks): Full web application for employees (all tabs)
- Phase 4 (2–3 weeks): Web dashboard for the owner (analytics)

## 3.2 User Stories

```
US-001: Morning Summary
As the owner
I want to receive one Telegram message with a summary across all 3 branches every day at 9:00
So that I can start the day with the full picture without calling employees

Acceptance Criteria:
- [ ] Message arrives every day at 9:00 ±1 minute
- [ ] Contains: branch till (P/O), Portfolio, grams by assay, tech items on balance,
      overdue positions, frozen funds — for each branch
- [ ] Buttons for drill-down by each branch
- [ ] If no data (file not uploaded) — explicit warning, not empty fields

Edge Cases:
- If Railway/Supabase are unavailable at 9:00 → bot retries after 5 min, maximum 3 attempts
- If .xls has not been uploaded today → summary notes "data from yesterday"
```

```
US-002: Foreclosure with Photo
As an employee
I want to register a foreclosed item via the Telegram bot by sending a photo
So that the owner immediately receives confirmation with a photo and selects the routing

Acceptance Criteria:
- [ ] Employee enters /incasso <contract_number>, bot finds the record in pledges
- [ ] If record not found — bot asks to enter data manually
- [ ] Bot requests a photo, does not accept text instead of a photo
- [ ] After photo — records the foreclosure, changes status active → on_branch
- [ ] Owner receives message with photo and routing buttons within 30 seconds
- [ ] Employee sees confirmation that the operation is saved

Edge Cases:
- If employee sends a document instead of a photo → bot rejects: "Send a photo, not a file"
- If Telegram is unavailable → operation is saved, notification is sent when restored
```

```
US-003: Owner Confirms Routing
As the owner
I want to route a foreclosed item with a single button press in Telegram
So that the employee knows where to send it and everything is recorded

Acceptance Criteria:
- [ ] Buttons: [Buyer A] [Personal Account] [City] [To shop] [Hold] [Cancel]
- [ ] After pressing — item status changes, employee receives a notification
- [ ] Pressing again on an already-confirmed item → message "Routing already set"
- [ ] Changing routing after confirmation — only via admin panel with a log entry

Edge Cases:
- Owner presses button twice (double tap) → idempotency, status changes only once
```

```
US-004: Daily Cash Reconciliation
As an employee
I want to enter the actual till balance at end of day
So that the system immediately shows the discrepancy with the calculated balance

Acceptance Criteria:
- [ ] Input field for actual P/O balance
- [ ] System shows: calculated balance, actual balance, discrepancy
- [ ] If discrepancy ≠ 0 — field highlighted red, mandatory comment required
- [ ] Discrepancy data appears in the owner's morning summary the following day

Edge Cases:
- Employee has not reconciled by 23:59 → system marks the day as "reconciliation not done", alert to owner
```

```
US-005: Metal Purchase with Price Check
As an employee
I want to record a gold/silver purchase with a photo of the scale
So that all transaction parameters are recorded and the price is verified by the system

Acceptance Criteria:
- [ ] Form accepts: client, assay, gross weight, net weight, price per gram, scale photo
- [ ] Amount to pay = net weight × price per gram (auto-calculated)
- [ ] If price per gram is out of range → form is blocked, notification sent to owner
- [ ] Employee bonus is calculated automatically from the table
- [ ] Without a photo — form cannot be submitted (INV-001 applies to purchases too)

Edge Cases:
- Price on the range boundary → passes without confirmation
- Client not found in database → form allows creating a new one
```

## 3.3 Detailed Module Requirements

```
MODULE: Telegram Bot (Module 1)
Purpose: notifications to owner and employees, routing and payment confirmations
Related invariants: INV-001, INV-004

User Flow (morning summary):
1. Cron at 9:00 → aggregates data across all branches from PostgreSQL
2. Composes one message with summary + inline branch buttons
3. Sends to owner in Telegram

User Flow (foreclosure):
1. Employee sends /incasso 526105
2. Bot searches contract_num=526105 in pledges for the employee's branch_id
3. If found — shows data, requests photo
4. Employee sends photo → bot uploads to Supabase Storage
5. Record updated: status active→on_branch, photo_url populated
6. Owner receives notification with photo + routing buttons

Business rules:
BR-001: One bot — two roles
  Telegram ID → determines role (owner / staff + branch_id)
  New user receives an activation code from the administrator

BR-002: Alerts
  - 48 hours before pledge expiry: alert "Expires day after tomorrow"
  - 24 hours before expiry: alert "Expires tomorrow"
  - Tech item on balance > 30 days: alert "Sitting too long"
  - Buyer has not confirmed payment > 3 days: alert (TODO: clarify threshold with client)

Bot commands:
/report — summary right now
/overdue — all overdue items with redemption amounts
/track — where each foreclosed item is
/gold — grams by assay and branch
/tech — tech items on balance
/frozen — frozen funds
/payments — expected payments from buyers
/shop — what is in the shop
/costs — month's expenses
/staff — current month's payroll
/month — month-end summary

Error handling:
- Telegram unavailable → retry × 3 with 5-minute interval → log error
- contract_num not found → offer to enter data manually
- .xls not uploaded → explicit message with instructions

Acceptance Criteria:
- [ ] Morning summary arrives at 9:00 ±1 min on 100% of working days
- [ ] Foreclosure is recorded in ≤ 30 sec from photo send to owner notification
- [ ] All 11 commands work
- [ ] Role is correctly determined by Telegram ID
```

```
MODULE: Employee Web Application (Module 2)
Purpose: operational accounting (till, foreclosure, purchase, shop, shift)
Related invariants: INV-001, INV-002, INV-003, INV-005

Tabs:
1. Today — chronology of the day's operations, active alerts, quick action buttons
2. Foreclosure — register a foreclosed item (duplicate via web, parallel to bot)
3. Pledges — list of all branch items with filters and status change
4. Till — enter transactions (income/expense/transfer), end-of-day reconciliation
5. Purchase — record purchased items (metal, tech)
6. Shop — counter sales
7. My Shift — start/stop working time, current month's shift history

Business rules:
BR-003: Branch isolation
  Employee sees only data for their branch_id. API checks branch_id from JWT.

BR-004: Redemption amount
  redemption_amount = loan_amount + (loan_amount × interest_rate × days_on_balance)
  Computed dynamically, not stored.

BR-005: Pledge amount limit
  TODO: loan_amount ≤ X% of appraised value (clarify with client — question #1 from old spec)
  If exceeded → alert to owner, operation is not blocked but flag exceeded_limit=true.

BR-006: Employee bonus for gold purchase
  Up to 5 g net weight: 2% of purchase amount
  5.01–10 g: 3%
  10.01–20 g: 4%
  Over 20 g: 5%
  For silver: fixed % — TODO: clarify with client (question #2)

BR-007: Bonus for shop tech sale
  15% of net profit (sale price − cost price)

BR-008: Till entry for foreclosure
  System automatically offers to create a ledger entry: Personal → P/O for the pledge amount,
  closing the gap between the calculated and actual till balance.

Input data: form data, .xls files from PawnExpert
Output data: records in PostgreSQL, events for the Telegram bot

Error handling:
- Photo missing during foreclosure/purchase → form cannot be submitted (see INV-001)
- Price per gram out of range → form blocked, notification to owner
- Network loss during form submission → show error, form data is preserved

Acceptance Criteria:
- [ ] Page loads in < 2 sec on mobile
- [ ] All 7 tabs work on Android Chrome 90+ and iOS Safari 14+
- [ ] Form without photo cannot be submitted
- [ ] Employee sees only their branch's data
- [ ] Operations cannot be deleted
```

```
MODULE: PawnExpert .xls Upload and Parsing (backend component)
Purpose: import current pledges from PawnExpert into PostgreSQL
Related invariants: INV-002

User Flow:
1. Employee clicks "Upload PawnExpert file" in the dashboard
2. Selects .xls file from computer (format: <branch>_YYYY_MM_DD.xls)
3. POST /api/upload-pawnexpert — file sent to FastAPI
4. FastAPI parses via openpyxl/xlrd using column mapping from the reference table
5. For each row: upsert by (contract_num, branch_id) — create or update
6. File is not saved after parsing
7. Response: {"processed": 47, "created": 12, "updated": 35, "skipped": 0}

Business rules:
BR-009: Data source priorities
  source='pawnexpert': status, photo, comment — not overwritten if already populated manually
  source='manual': on next .xls parse — system supplements with data from PawnExpert,
  but does NOT overwrite status, photo, comment

BR-010: Column mapping
  Mapping (column name in .xls → DB field) is configured in the admin panel.
  Critical prerequisite: client provides a real .xls BEFORE development starts.

Acceptance Criteria:
- [ ] File up to 20 MB parsed in < 30 sec
- [ ] Duplicates by (contract_num, branch_id) are not created
- [ ] Unknown columns → explicit error listing unrecognized fields
- [ ] source='manual' data is not overwritten on next import
```

## 3.4 Business Rules Registry

| ID | Rule | Exceptions | Priority | Invariant |
|---|---|---|---|---|
| BR-001 | One bot — two roles by Telegram ID | — | High | — |
| BR-002 | Alerts by deadline and tech items | Threshold in days is configurable | High | — |
| BR-003 | Branch isolation | Admin sees everything | High | — |
| BR-004 | Redemption amount — dynamic calculation | — | High | — |
| BR-005 | Pledge amount limit vs appraisal | TODO: % to clarify | Medium | — |
| BR-006 | Gold purchase bonus by weight | For silver fixed % (TODO) | Medium | — |
| BR-007 | Tech sale bonus 15% of profit | — | Medium | — |
| BR-008 | Till entry for foreclosure | — | High | — |
| BR-009 | Data source priorities | — | High | INV-002 |
| BR-010 | PawnExpert column mapping in reference table | — | High | — |

---

# SECTION 4. NON-FUNCTIONAL REQUIREMENTS

## 4.1 Performance

| Metric | Target | Maximum |
|---|---|---|
| Web app main screen load | < 2 sec | 4 sec |
| API request for tab data | < 3 sec | 6 sec |
| Form save (POST) | < 2 sec | 5 sec |
| .xls file upload (10 MB) | < 15 sec | 30 sec |
| .xls parsing on backend | < 30 sec | 2 min |
| Telegram notification send | < 30 sec from event | 2 min |
| Morning summary (collect + send) | < 60 sec | 3 min |

Under 2x load (all 6 employees simultaneously): FastAPI async, no expected degradation.

## 4.2 Reliability

| Indicator | Value |
|---|---|
| Uptime — Telegram bot | 99% (~7 h/month) |
| Uptime — Web application | 99.5% (~3.5 h/month) |
| Uptime — FastAPI backend | 99% (~7 h/month) |
| Uptime — Supabase Storage | 99.9% (~45 min/month) |
| RTO | 2–4 hours (contractor response time) |
| RPO | 24 hours (daily Supabase backup) |

**Important:** the system runs on free tiers of Supabase/Railway/Netlify — provider uptime guarantees are not confirmed by SLA. The contractor's guarantee covers only their own code.

In the event of partial Telegram failure: data is saved in PostgreSQL, notifications are sent when service is restored.

## 4.3 Scalability

| Parameter | Limit | If exceeded |
|---|---|---|
| Branches | 3 | Change Request |
| Employees | 6 | Change Request |
| Records in pledges per year | ~1,500 | PostgreSQL without limits |
| Concurrent users | up to 10 | FastAPI async, no issues |
| .xls file size | up to 20 MB | Rejected with error message |

---

# SECTION 5. ARCHITECTURE

## 5.1 Overview

**Type:** Modular monolith (single FastAPI service)

**Diagram (C4 Level 1):**
```
PawnExpert (PC at branch)
    │ .xls export (daily)
    ▼
[Web App — Netlify]          [Telegram API]
    │ HTTP upload              │ webhook
    ▼                         ▼
[FastAPI Backend — Railway] ←→ [python-telegram-bot]
    │
    ▼
[PostgreSQL — Supabase] ←→ [Supabase Storage (photos)]
    │
    ├──▶ Web App (employee: till, foreclosure, purchase)
    └──▶ Web Dashboard (owner: analytics, reports)
```

**Application layers:**
```
API Layer (FastAPI routers)    — HTTP endpoints, auth, validation
Service Layer                  — business logic, calculations
Repository Layer               — DB queries (asyncpg/SQLAlchemy)
Telegram Layer                 — command and callback handlers
```

**Layer rules:**
- Telegram Layer uses Service Layer — does not access the DB directly
- Business logic (calculations, invariant checks) — only in Service Layer
- Direct DB queries — only through Repository Layer

## 5.2 API Contracts (key endpoints)

```
POST /api/v1/upload-pawnexpert
Auth: Bearer JWT (staff role)
Content-Type: multipart/form-data

Request: { file: .xls, branch_id: UUID }
Response 200: { "processed": N, "created": N, "updated": N, "skipped": N }
Response 400: { "error": "PARSE_ERROR", "details": "Unknown columns: [...]" }
Response 413: { "error": "FILE_TOO_LARGE", "max_mb": 20 }

---

POST /api/v1/pledges/{id}/incasso
Auth: Bearer JWT (staff role)
Request: { photo_url: string, notes?: string }
Response 200: { "pledge_id": UUID, "status": "on_branch", "owner_notified": true }
Response 404: { "error": "NOT_FOUND" }
Response 409: { "error": "CONFLICT", "message": "Pledge already incasso'd" }

---

POST /api/v1/pledges/{id}/route
Auth: Bearer JWT (owner role)
Request: { destination: "buyer_a|personal|city|shop|hold" }
Response 200: { "pledge_id": UUID, "status": "routed", "destination": "..." }
Response 403: { "error": "FORBIDDEN" } // owner only

---

GET /api/v1/reports/morning-summary
Auth: Bearer JWT (owner role)
Response 200: { branches: [{ branch_id, name, cash_po, portfolio, gold_grams, ... }] }

---

POST /api/v1/purchases
Auth: Bearer JWT (staff role)
Request: { type: "gold|silver|tech|other", client_id?, assay?: "585|750|...",
           gross_weight?: float, net_weight?: float, price_per_gram?: float,
           photo_url: string, ... }
Response 200: { "purchase_id": UUID, "bonus_amount": float }
Response 422: { "error": "PRICE_OUT_OF_RANGE", "min": X, "max": Y, "given": Z }
```

## 5.3 Data Schema (key entities)

```sql
-- Branches
CREATE TABLE branches (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name        TEXT NOT NULL,           -- 'Domashny', 'Bobrynets', 'Korabeliv'
  address     TEXT,
  is_active   BOOLEAN DEFAULT true,
  created_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Pledges (main table)
CREATE TABLE pledges (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  contract_num    TEXT NOT NULL,
  branch_id       UUID REFERENCES branches(id),
  client_name     TEXT,
  item_name       TEXT NOT NULL,
  item_type       TEXT NOT NULL,       -- 'gold','silver','tech','other'
  assay           TEXT,                -- '585','750','925' etc.
  gross_weight    NUMERIC,
  net_weight      NUMERIC,
  loan_amount     NUMERIC NOT NULL,
  interest_rate   NUMERIC NOT NULL,    -- % per day
  pledge_date     DATE NOT NULL,
  incasso_date    DATE,
  estimated_value NUMERIC,
  status          TEXT NOT NULL,       -- 'active','on_branch','hold','routed_buyer',
                                       -- 'routed_personal','routed_city','in_shop',
                                       -- 'redeemed','repledge','sold'
  photo_url       TEXT,
  source          TEXT DEFAULT 'pawnexpert',  -- 'pawnexpert','manual'
  owner_approved  BOOLEAN DEFAULT false,
  destination     TEXT,
  exceeded_limit  BOOLEAN DEFAULT false,
  is_active       BOOLEAN DEFAULT true,
  created_at      TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at      TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  UNIQUE (contract_num, branch_id)     -- INV-002
);

-- Cash operations
CREATE TABLE cash_operations (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  branch_id     UUID REFERENCES branches(id),
  staff_id      UUID REFERENCES staff(id),
  op_type       TEXT NOT NULL,    -- 'income','expense','transfer','wallet_move','incasso_coverage'
  wallet_from   TEXT,             -- 'branch_po','personal_home','portfolio'
  wallet_to     TEXT,
  amount        NUMERIC NOT NULL,
  category      TEXT,
  notes         TEXT,
  operation_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  is_active     BOOLEAN DEFAULT true  -- soft delete only (INV-003)
);

-- Purchases
CREATE TABLE purchases (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  branch_id     UUID REFERENCES branches(id),
  staff_id      UUID REFERENCES staff(id),
  item_type     TEXT NOT NULL,
  client_id     UUID,
  assay         TEXT,
  gross_weight  NUMERIC,
  net_weight    NUMERIC,
  price_per_gram NUMERIC,
  total_amount  NUMERIC NOT NULL,
  bonus_amount  NUMERIC,
  photo_url     TEXT NOT NULL,    -- mandatory (INV-001 applies)
  pledge_id     UUID REFERENCES pledges(id),  -- if from a foreclosed pledge
  is_active     BOOLEAN DEFAULT true,
  created_at    TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Employee shifts
CREATE TABLE shifts (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  staff_id    UUID REFERENCES staff(id),
  branch_id   UUID REFERENCES branches(id),
  started_at  TIMESTAMP WITH TIME ZONE NOT NULL,
  ended_at    TIMESTAMP WITH TIME ZONE,
  duration_h  NUMERIC,            -- calculated on close
  hourly_rate NUMERIC NOT NULL,
  base_salary NUMERIC,            -- calculated on close
  is_active   BOOLEAN DEFAULT true,
  created_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## 5.4 ADR + Decision Log

```
ADR-001: One bot instead of two
Context: notifications needed for both owner and employees.
Decision: one bot, role by Telegram ID.
Reason: simpler to manage, one token, one deployment.
Trade-offs: slightly more complex role detection logic.
For agents: always check telegram_id before showing the menu.

ADR-002: Supabase Storage instead of Google Drive
Context: need to store foreclosure photos.
Decision: Supabase Storage, private bucket.
Reason: no dependency on a Google account, single platform with PostgreSQL,
  signed URL generated by backend (TTL 1 hour).
Trade-offs: photo migration will require a script if we leave Supabase.

ADR-003: Portfolio as a computed aggregate
Context: need to display "Pledge Portfolio" in real time.
Decision: calculate SUM(loan_amount) WHERE status='active' on each request.
Reason: eliminates desynchronization caused by errors when updating a denormalized field.
Trade-offs: additional DB load, acceptable at ~1,500 records/year.
```

```
DEC-001: PawnExpert column mapping stored in DB (not in code)
Question: how to update the mapping when PawnExpert is updated?
Decision: column_mappings table, editable via admin panel.
Reason: no deployment needed when the .xls structure changes.
Rejected: hardcoding in code — requires deployment on any change.
```

---

# SECTION 6. SECURITY

## 6.1 Authentication and Authorization

- Method: login/password + JWT (web application); Telegram ID (bot)
- Authorization: RBAC (owner / staff / admin)
- Token TTL: 8 hours (working day)
- MFA: none (MVP)

**Permission matrix:**

| Operation | Owner | Employee | Administrator |
|---|---|---|---|
| View all branches | ✓ | ✗ | ✓ |
| View own branch | ✓ | ✓ | ✓ |
| Record operations | ✓ | ✓ (own) | ✓ |
| Confirm item routing | ✓ | ✗ | ✗ |
| Deactivate operations | ✗ | ✗ | ✓ |
| Configure reference data | ✗ | ✗ | ✓ |
| Upload .xls | ✓ | ✓ (own) | ✓ |

## 6.2 Data Protection

- Encryption in transit: TLS 1.3 (Supabase, Railway, Netlify — all by default)
- Encryption at rest: Supabase managed encryption
- Sensitive data: client passport data is NOT stored (it stays in PawnExpert)
- Never logged: JWT tokens, employee passwords

## 6.3 Secret Management

- Storage: Railway environment variables (backend) and Netlify (frontend)
- In code: NEVER
- Secrets: DATABASE_URL, SUPABASE_KEY, TELEGRAM_BOT_TOKEN, JWT_SECRET

## 6.4 Compliance

- Personal data: minimal (employee names, client names in purchases). GDPR nominally applicable — system is in Ukraine, data is not EU.
- Payment data: none → PCI DSS N/A
- AI system: none → EU AI Act N/A
- Regulatory data (NBU/State Financial Services): remains in PawnExpert, not in our system

---

# SECTION 7. INFRASTRUCTURE

## 7.1 Environments

| Environment | Data | Access |
|---|---|---|
| Development | synthetic (generated) | contractor |
| Production | real | employees + owner |

*No staging environment (MVP budget). Testing on dev data.*

## 7.2 Deployment

- Backend: Railway (Git push → auto-deploy)
- Frontend: Netlify (Git push → auto-deploy)
- DB: Supabase managed (SQL migrations via scripts)
- Region: EU (Supabase EU Frankfurt or EU West)
- Rollback: previous Railway deployment available in 1 click

## 7.3 Monitoring

- Uptime: UptimeRobot (free, monitors /health endpoint)
- Logs: Railway built-in logger
- Alerts: email/Telegram on uptime failure
- On-call: Artem Kholomyanskiy

## 7.4 Backup

| Type | Frequency | Retention | Responsible |
|---|---|---|---|
| Supabase automatic | Daily | 7 days | Supabase |
| Manual .xlsx from dashboard | Weekly | No limit | Administrator |
| Full pg_dump | Monthly | No limit | Contractor |

---

# SECTION 8. TESTING

## 8.1 Strategy

| Level | Tool | Coverage |
|---|---|---|
| Unit (service layer) | pytest | 80%+ for business logic |
| Integration (API) | pytest + httpx | key endpoints |
| E2E | manual (no Playwright budget) | critical user flows |

## 8.2 Key Test Cases

```
TEST-001: Foreclosure — duplicate rejected
Given:    pledge contract_num=526105, branch_id=X already exists with status on_branch
When:     POST /api/v1/pledges/incasso with contract_num=526105, branch_id=X
Then:     HTTP 409 CONFLICT
Invariant: INV-002

TEST-002: Routing for owner only
Given:    employee JWT token
When:     POST /api/v1/pledges/{id}/route
Then:     HTTP 403 FORBIDDEN
Invariant: INV-004

TEST-003: Purchase price out of range
Given:    Gold 585°, range [2800, 3600] UAH/g
When:     POST /api/v1/purchases with price_per_gram=4000
Then:     HTTP 422 PRICE_OUT_OF_RANGE
Invariant: INV-005

TEST-004: Portfolio recalculates correctly
Given:    3 active pledges with loan_amount 10000, 5000, 3000 = 18000
When:     one transitions to status on_branch
Then:     GET morning-summary returns portfolio=13000

TEST-005: PawnExpert column mapping
Given:    mapping configured, .xls with 47 rows
When:     POST /api/upload-pawnexpert
Then:     {"processed": 47, "created": 47, "updated": 0, "skipped": 0}

TEST-006: Unique key on repeated .xls upload
Given:    the same 47 records already in DB
When:     POST /api/upload-pawnexpert with the same file
Then:     {"processed": 47, "created": 0, "updated": 47, "skipped": 0}
Invariant: INV-002
```

## 8.3 Smoke Tests (after each deployment)

```
□ GET /health → HTTP 200
□ Telegram bot responds to /report
□ Employee web app loads in < 4 sec
□ POST /api/v1/upload-pawnexpert with test .xls → success
□ Morning summary generated without errors in logs
```

---

# SECTION 9. PROJECT MANAGEMENT

## 9.1 Decomposition

| Module | Depends on | Who | Estimate |
|---|---|---|---|
| DB schema + migrations | — | Artem + Claude | 2 days |
| FastAPI: auth, base | DB | Artem + Claude | 2 days |
| .xls parser + upload API | DB | Artem + Claude | 3 days |
| Telegram bot MVP (summary + alerts) | Parser | Artem + Claude | 5 days |
| Item tracking (foreclosure + routing) | Bot MVP | Artem + Claude | 4 days |
| Buyer confirmation | Tracking | Artem + Claude | 2 days |
| Web app: all tabs | API | Artem + Claude | 10 days |
| Payroll calculation + timesheet | Web | Artem + Claude | 3 days |
| Buyer report | Web | Artem + Claude | 2 days |
| Owner web dashboard | All | Artem + Claude | 7 days |

**Critical path:** DB → API auth → .xls Parser → Telegram bot → Item tracking → Web app

## 9.2 Risks

| Risk | Probability | Impact | Mitigation |
|---|---|---|---|
| Client does not provide .xls in time | M | H | Start is impossible without .xls — stipulated in contract |
| PawnExpert changes .xls format on update | M | M | Mapping in DB — no deployment required (DEC-001) |
| Railway/Supabase free tier imposes restrictions | L | H | Migrate to paid plan (~$20/month) |
| Employees do not switch to the system | M | H | Online training + 2-week support |
| Telegram API block | L | H | Fallback — web dashboard (notifications sent later) |

## 9.3 Explicitly Out of Scope

- Replacing PawnExpert or duplicating its functionality
- Mobile application (iOS/Android native)
- Integration with banking APIs
- Automatic metal price feeds (entered manually)
- Migration of archived Excel data
- Expansion to 4+ branches (requires Change Request)
- Client reminder system for pledge deadlines (not in MVP scope)

---

# SECTION 10. CHANGE SPECIFICATION

*Template to be filled for each system change after launch.*

```
CHANGE: [Change name]
Date: YYYY-MM-DD
Affected invariants: [INV-XXX]

CURRENT STATE:
[how it works now]

TARGET STATE:
[how it should work after]

WHAT WE DO NOT CHANGE:
- [file/module] — do not touch

IMPACT ANALYSIS:
- Affected files: [list]
- Risks: [what might break]

ROLLBACK:
- Trigger: [under what condition]
- Procedure: [steps]

REGRESSION CHECKS:
- [ ] [what to verify]
```

---

# SECTION 11. AI-FIRST (SDD)

*Development is carried out with Claude Code as the implementing agent.*

## 11.1 Agent Configuration

**Rules hierarchy:**
```
1. CLAUDE.md (global) → highest priority
2. This document (ANSS-Core spec) → project rules
3. Session prompt → operational instructions
```

**Forbidden Patterns for this project:**
```
DO NOT store the Pledge Portfolio as a field — compute only
DO NOT perform DELETE operations — soft delete only (is_active=false)
DO NOT access the DB directly from the Telegram layer — only through Service Layer
DO NOT hardcode PawnExpert column mapping — only from the column_mappings table
DO NOT allow routing status change without owner_approved=true
```

## 11.2 Fail-Fast Conditions

```
AGENT STOPS when:
□ Choosing an architectural approach (monolith vs microservices)
□ Changing the DB schema for existing tables
□ A task outside the specification (e.g., bank integration)
□ Violating any invariant (INV-001 — INV-005)
□ Changing business rules from Section 3.4

AGENT ACTS INDEPENDENTLY when:
□ Naming variables and formatting code
□ UI component implementation details
□ Choosing between SQLAlchemy and asyncpg (follows from architecture)
□ Writing tests for already-documented ACs
```

---

# SECTION 12. SUPPORT

## 12.1 SLA (warranty period — 1 month after delivery)

| Metric | SLO |
|---|---|
| Availability | 99% |
| Critical bug (data loss) | response < 4 hours |
| Regular bug (feature not working) | response < 24 hours |
| Enhancement request | discussion within 48 hours |

## 12.2 Incidents

| Priority | Criterion | Response |
|---|---|---|
| P0 | System unavailable / data loss | 4 hours |
| P1 | Telegram bot not working / forms not saving | 8 hours |
| P2 | Individual feature working incorrectly | 24 hours |

---

# SECTION 13. LIVING SPEC

```
□ Spec is updated in the same iteration as the code
□ Code change without updating the spec = technical debt
□ ADR is updated with every architectural decision
□ Decision Log is updated for non-trivial choices
□ Invariants (INV-001–005) — changed only by Artem Kholomyanskiy
□ TODO notes in this document must be resolved before Phase 3 starts
```

---

# SECTION 14. AGENT REVIEW

**Issues found and open questions:**

1. **TODO: 3 business parameters not yet received from the client** (Section 17 of old spec):
   - BR-005: maximum pledge-to-appraisal ratio (%)
   - BR-006: bonus for silver purchase (fixed %)
   - Silver price range (min/max UAH/g)

2. **Edge case "Personal Account" not fully described:** when an item has been transferred to the owner's personal balance and the client later arrives — a `personal_return` operation is needed. The form was described in the working spec but not included in the User Stories of this version. Add US-006 in the next iteration.

3. **Payroll calculation — taxes:** the formula includes personal income tax 18%, military levy 1.5%, unified social contribution 22% as branch expenses, but it is unclear: does the system deduct them automatically from the accrued salary or only display them for planning? Clarify with client.

4. **Buyer authorization in the bot:** for payment confirmation — the buyer does not interact with the bot themselves, only the owner does. Is this correct? If the buyer also uses the bot — a third role is needed.

5. **Photo via web form vs bot:** in Phase 3 (web app) the foreclosure form exists both in the bot and in the web. Need to define: are these duplicate channels or is one of them primary? If both, ensure atomicity (cannot register the same contract_num twice).

**Questions before Phase 1 starts:**
1. Obtain the .xls file from PawnExpert before writing the parser (critical)
2. Resolve 3 TODO business parameters (see point 1 above)
3. Clarify: taxes in payroll — auto-deduct or display only?

---

# SPEC READINESS CHECKLIST

```
MANDATORY
✅ Invariants written (5 total)
✅ Architectural principles chosen
✅ What is NOT in scope — stated explicitly
✅ Each requirement has Acceptance Criteria
✅ Acceptance Criteria are specific and verifiable
✅ Edge cases described for key modules
✅ API Contracts include all error codes
✅ Technology stack fixed
✅ Forbidden Patterns written

REQUIRES FOLLOW-UP
⬜ BR-005: pledge-to-appraisal limit — awaiting client response
⬜ BR-006: silver bonus — awaiting client response
⬜ Silver price range — awaiting client response
⬜ US-006: "Return from personal account" operation (personal → client arrives)
⬜ Client — name/company not filled in
⬜ Agent Review completed ✅ — questions recorded above
```

---

*ANSS-Core v0.2 Draft — Artem Kholomyanskiy — 2026-06-01*
*Based on lombard_FULL_TZ.md v2.0 (May 2026)*
*Template: ANSS Standard v1.3*

