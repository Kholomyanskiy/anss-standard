> *Translated from Russian. Original: [solo-founder-os.md](solo-founder-os.md)*

# ANSS-CORE — Solo Founder OS
## Local Operating System for a Solo Founder with an AI Team

**Template version:** 1.0 (ANSS-Core)
**Level:** CORE

> **The agent reads this document as follows:**
> 1. Read the entire document in full
> 2. Re-read the GLOSSARY (Section 2.1) — this is the project's language
> 3. Memorize the INVARIANTS (Section 2.5) — violating them is never permitted
> 4. Read the PRINCIPLES (Section 2.6) — compass under uncertainty
> 5. Complete the Agent Review (Section 14) BEFORE writing any code

---

# SECTION 0. METADATA

| Field | Value |
|---|---|
| Project | Solo Founder OS |
| Version | 1.0 Draft |
| Date | 2026-06-01 |
| Status | Draft |
| Author | Artem Kholomyanskiy |
| Client | Artem Kholomyanskiy (personal tool) |
| Executor | Claude Code |
| Language | English |

**Version history:**

| Version | Date | Change |
|---|---|---|
| 0.1–0.4 | 2025–2026 | Iterative drafts of prompt format |
| 2.5 | 2026-05 | Added Smart Onboarding (prompt format) |
| 1.0 | 2026-06-01 | Reworked to ANSS-Core standard |

**Detail level:** CORE
*(Small + Low criticality — personal local tool)*

---

# SECTION 1. CONTEXT AND ECONOMICS

## 1.1 Project Summary

**What we are building:**
A local web application — an operating system for a solo founder with a personal AI team of specialized agents (CEO, backend, sales, SMM, etc.) with shared memory, planning mode, and a file system.

**Problem we are solving:**
There is no unified workspace for AI agents: every Claude chat is separate, there is no memory between sessions, no specialized roles, no "set a task → get a plan → execute" mode. Context-switching kills productivity.

**Why now:**
Claude Code is installed and authorized, Claude Pro/Max subscription is active. The tool is needed for daily work right now — not at some point in the future.

**What we are NOT doing in this version:**
- No public deployment — localhost only
- No multi-user mode
- No Fetch/Playwright/Gmail via UI (toggles exist, UI does not)
- No mobile app
- No data export/import
- No voice input
- No pinnedExamples UI
- No npm packages — only built-in Node.js modules

## 1.2 Goals and Success Criteria

**Business goals:**
- One tool replaces scattered AI chats for all work tasks
- Smart Onboarding sets up a personal team in ≤ 5 minutes
- Planning mode lets you delegate a task to the AI team with a single command

**KPIs — how we know we succeeded:**
- Onboarding complete: < 5 minutes from first launch to first project
- Server start: `node server.js` → works without `npm install`
- Sending a task to an agent: < 3 seconds to first response chunk
- Smart Onboarding analysis: 30–60 seconds, with fallback to manual mode on error

**Project Definition of Done:**
1. `node server.js` starts without errors, opens browser at localhost:3747
2. Smart Onboarding completes all 4 steps and creates a working configuration
3. Chat with an agent works in stream mode
4. Planning mode creates and executes a plan through multiple agents
5. Data is saved to `~/.solo-founder/` and restored after restart

## 1.3 Economics

**Cost:**
- Development (CAPEX): $0 — developed by the user themselves via Claude Code
- Infrastructure (OPEX): $0 — local execution
- LLM (OPEX): $0 additional — already paid Claude Pro/Max subscription

**ROI:** immediate — a daily-use tool at zero additional cost.

---

# SECTION 2. DOMAIN

## 2.1 Glossary

*The agent reads this section FIRST. All terms are defined below.*

**Solo Founder OS (SFOS)**
Definition: this entire application
NOT to be confused with: macOS, Windows (OS in the traditional sense)
In code: project name, directory `~/.solo-founder/`

**Agent**
Definition: a persona with a name, role, system prompt, and a set of skills. Executes tasks via `claude -p`.
NOT to be confused with: Claude Code (development tool), Claude AI (model)
In code: object in `agents.json`, fields: `id, name, role, systemPrompt, mcpServers, *Enabled`

**Agent registry**
Definition: `agents_registry.json` — master list of all 11 possible agents
NOT to be confused with: `agents.json` — list of currently active agents only
In code: `agents_registry.json` — read via `loadRegistry()`

**Active agents**
Definition: agents selected during onboarding that operate in the current configuration
In code: `agents.json` — read via `loadActiveAgents()`

**Boss**
Definition: mandatory orchestrator agent. Accepts tasks, plans, coordinates the team.
NOT to be confused with: the user (human)
In code: `id: "boss"`, `required: true`

**Setup (Configurator)**
Definition: mandatory HR agent. Hires/configures agents, connects MCP.
In code: `id: "setup"`, `required: true`

**Onboarding**
Definition: initial system configuration on first launch. Creates `company.md`, selects agents, creates the first project.
In code: `state.onboardingDone`, `state.onboardingMode`

**Smart Setup**
Definition: onboarding mode with AI analysis. User describes themselves → Boss + Setup generate a personal configuration.
In code: `state.onboardingMode = 'smart'`

**Manual Setup**
Definition: onboarding mode with manual agent selection from a pre-built list.
In code: `state.onboardingMode = 'manual'`

**Project**
Definition: a workspace with a name, context, tasks, and artifacts. Linked to a folder on disk.
In code: `projects/{projectId}/` — `info.json`, `context.md`, `decisions.md`, `done.md`

**Task**
Definition: a unit of work within a project. Can be a simple message or a plan with steps.
In code: `projects/{projectId}/tasks/{taskId}.json`, statuses: `draft → plan_pending → plan_running → done → failed`

**Artifact**
Definition: a file created by an agent during task execution.
In code: `projects/{projectId}/artifacts/`

**MCP (Model Context Protocol)**
Definition: protocol for extending Claude's capabilities via external tools (filesystem, gmail, fetch, browser).
In code: `--mcp-config {path}`, temporary JSON file `mcp_configs/{agentId}_mcp.json`

**preferSubscription**
Definition: flag in state.json indicating that the system runs via subscription, not via API key.
In code: `state.preferSubscription: true` → remove `ANTHROPIC_API_KEY` from the environment before launching Claude

**company.md**
Definition: a brief description of the user's activities (max 1500 characters). Added to each agent's context.
In code: `~/.solo-founder/company.md`

**Agent memory**
Definition: personal context of an agent (up to 800 characters). Added to the prompt on every invocation.
In code: `~/.solo-founder/agents_memory/{agentId}.md`

**SSE (Server-Sent Events)**
Definition: mechanism for streaming events from server to UI in real time.
In code: `GET /events` — unified event stream for the entire UI

**taskId**
Definition: unique task identifier, generated only via `generateTaskId()`.
NOT to be confused with: projectId (project UUID)
In code: format `task_{Date.now()}_{random5chars}`

## 2.2 Users

**Role: Solo Founder (single user)**
- Who it is: Artem Kholomyanskiy, AI automation specialist and consultant
- What they do in the system: assign tasks to agents, manage projects, read responses, work with plans
- What they value: fast startup, zero configuration, personal configuration
- Expected count: 1
- Usage frequency: daily
- Technical level: high

## 2.3 Constraints and Assumptions

**Technical constraints (cannot be changed):**
- Windows only (path.join, shell:true are mandatory)
- Built-in Node.js modules only — no `npm install`
- Three files: `server.js`, `index.html`, `agents_registry.json`
- Runs via Claude CLI (`claude -p`), not via API
- Port: 3747 (fixed)
- Data: `~/.solo-founder/` (fixed)

**Business constraints:**
- Claude Pro/Max subscription is already paid
- Development via Claude Code (AI agent)
- Timeline: no hard deadline

**Assumptions:**
- `claude` CLI is installed and authorized
- Node.js is installed
- User is on Windows
- MCP filesystem is available via `npx @modelcontextprotocol/server-filesystem`

**Accepted as given:**
- `preferSubscription: true` always
- Boss and Setup are always mandatory agents
- Data is stored locally, without encryption (acceptable for a personal tool)

## 2.4 Technology Stack

| Layer | Technology | Version |
|---|---|---|
| Backend | Node.js (built-in modules: http, fs, path, os, child_process) | 18+ |
| Frontend | HTML5 / CSS3 / JS ES2022+, ES Modules, no frameworks | — |
| Storage | File system (JSON + Markdown) | — |
| AI | Claude CLI (`claude -p`) via subprocess | Claude Pro/Max |
| MCP | @modelcontextprotocol/server-filesystem (via npx) | current |
| Launch | start.bat / node server.js | — |

**External dependencies:**

| Service | Purpose | Fallback if unavailable |
|---|---|---|
| Claude CLI | executing AI requests | show auth_error in UI |
| npx + MCP | filesystem tools for agents | agent works without MCP (text only) |

## 2.5 INVARIANTS ✅

*Absolute constraints. Violating them is prohibited under any circumstances.*
*The agent stops if a task requires violating an invariant.*

```
INV-001: No external dependencies
Forbidden: adding npm packages or require() of external modules
Reason: user must be able to run node server.js without npm install
Check: server.js has no require() lines other than built-in Node.js modules

INV-002: Three files
Forbidden: creating additional server-side files (utils.js, config.js, etc.)
Reason: all logic in three files — a fundamental architectural decision
Check: project contains only server.js, index.html, agents_registry.json

INV-003: Subscription-based operation
Forbidden: using ANTHROPIC_API_KEY when preferSubscription:true
Reason: subscription, not API — a fundamental user requirement
Check: spawnClaude() removes ANTHROPIC_API_KEY from env when preferSubscription:true

INV-004: Mandatory agents
Forbidden: removing boss and setup from the active agents list
Reason: the system does not function without the orchestrator and configurator
Check: finalizeOnboarding() guarantees boss and setup in the resulting list

INV-005: Memory limits
Forbidden: saving agent memory > 800 characters, project context > 1000 characters
Reason: Claude context window, prompt predictability
Check: saveAgentMemory() truncates to 800 characters before writing

INV-006: Single taskId generation
Forbidden: generating taskId in any way other than via generateTaskId()
Reason: uniqueness and predictable format for parsing
Check: `task_${Date.now()}_${Math.random().toString(36).substring(2,7)}`

INV-007: Single JSON parser
Forbidden: duplicating JSON parsing logic in different parts of the code
Reason: extractJson() — single function for both plan AND onboarding (4 strategies)
Check: no duplicate try/catch blocks for JSON.parse other than in extractJson()

INV-008: Single spawnClaude
Forbidden: creating a second way to launch the claude subprocess
Reason: single point of control for auth, timeout, preferSubscription
Check: all claude launches only via spawnClaude()
```

## 2.6 ARCHITECTURAL PRINCIPLES ✅

*Guidance for decisions under uncertainty.*

```
AP-001: Simple > Clever
A simple solution that works is better than a clever one that is hard to maintain.
When choosing between elegant and understandable — choose understandable.

AP-003: Buy Before Build
Check existing solutions first. Claude already exists — don't build an LLM runtime.

AP-004: Human Override Always Available
There is always a "skip", "cancel", or "choose manually" button.
No autonomous irreversible actions without the ability to undo.

AP-006: Fail Loud
A visible error is better than silent incorrect behavior.
Auth error → immediately in UI. Analysis failed → fallback + notification.

AP-007: Data Outlives Code
Data in ~/.solo-founder/ matters more than code. Never delete user data.

AP-009: YAGNI
Do not implement what is not in the specification.
pinnedExamples, voice input, multi-user — not in this version.

AP-011: Locality > Dependencies
Built-in Node.js modules are preferred over any npm packages.
If something can be done via fs, http, path — do it that way.

AP-012: Fallback is mandatory for AI
Every AI call that can fail must have a fallback.
Smart Onboarding failed → manual mode. Plan parsing failed → manual_chat.
```

---

# SECTION 3. FUNCTIONAL REQUIREMENTS

## 3.1 Feature Overview

**Functional blocks (MoSCoW):**

| Block | Priority |
|---|---|
| Smart Onboarding (4 steps) | Must Have |
| Manual onboarding (fallback) | Must Have |
| Chat with agent (stream) | Must Have |
| Planning mode (Boss → plan → execution) | Must Have |
| Project creation and switching | Must Have |
| Agent and project memory | Should Have |
| Artifacts (auto-detection and saving) | Should Have |
| Folder browser (GET /browse) | Should Have |
| Chat history (index.json + restore) | Should Have |
| Hiring / Dismissing agents | Could Have |
| Browser notifications | Could Have |
| Fetch/Playwright/Gmail UI (toggles) | Won't Have (MVP) |
| pinnedExamples UI | Won't Have (MVP) |
| Voice input | Won't Have (MVP) |
| Data export / import | Won't Have (MVP) |

**Phases:**
- Phase 1 (MVP): Must Have + Should Have blocks
- Phase 2: Could Have + the rest

## 3.2 User Stories

```
US-001: Smart Onboarding
As a solo founder
I want to describe myself and receive a personalized AI team
So that I don't have to configure each agent manually

Acceptance Criteria:
- [ ] Mode selection screen is shown on first launch (onboardingDone:false)
- [ ] "Smart Setup" button opens a textarea with a 0/3000 character counter
- [ ] Clicking "Analyze" sends POST /onboarding/analyze and shows a loader
- [ ] During analysis two progress bars are shown (Boss + Setup)
- [ ] After onboarding_done, agent cards are shown with editing capability
- [ ] Clicking "Accept team" completes onboarding and transitions to the main screen
- [ ] All 8 finalization steps are executed (company, agents, memory, project, state)

Edge Cases:
- If both AI calls failed → SSE onboarding_error { fallback:true } → show manual selection
- If only one failed → use the other's result + defaults
- If boss/setup are not in recommendedAgents → add them forcefully
- If the user clicked "Skip" → transition to manual onboarding
```

```
US-002: Manual onboarding
As a solo founder
I want to select agents manually from a pre-built list
So that I can start working quickly without AI analysis

Acceptance Criteria:
- [ ] Grid of 11 agents with checkboxes
- [ ] Boss and Setup — disabled, always checked
- [ ] POST /agents saves selected + mandatory agents
- [ ] First project is created with a name and working folder
- [ ] state.onboardingDone:true after completion

Edge Cases:
- If the user unchecked all boxes except mandatory ones → save only boss + setup
```

```
US-003: Chat with agent
As a solo founder
I want to send a message to an agent and receive a real-time response
So that I can work with the AI team like real specialists

Acceptance Criteria:
- [ ] Loader is shown immediately after sending (before the first chunk)
- [ ] Response streams as it is generated (agent_chunk events)
- [ ] After agent_done the loader is hidden
- [ ] History is saved in chatHistories[agentId]
- [ ] company.md and agent memory are included in the prompt
- [ ] On auth_error, instructions for re-authorization are shown

Edge Cases:
- If response is empty → show "Agent did not respond. Please try again."
- If timeout 120 sec → agent_error with a retry suggestion
- If MCP is unavailable → continue without MCP (text only)
```

```
US-004: Planning mode
As a solo founder
I want to submit a complex task and receive a plan executed by multiple agents
So that I don't have to break the task down manually

Acceptance Criteria:
- [ ] POST /chat/plan → Boss creates a plan in JSON format
- [ ] Plan is shown to the user with editing capability (↑↓ steps)
- [ ] POST /chat/plan/:taskId/start launches sequential execution
- [ ] Each step: plan_step → (execution) → plan_step_done / plan_step_error
- [ ] plan_done contains a summary from Boss
- [ ] Retry button for failed steps

Edge Cases:
- If extractPlan returned null → show retry and manual_chat buttons
- If the agent for a step is not active → plan_step_skip with explanation
- If a step failed → partial completion, remaining steps continue
```

```
US-005: Project management
As a solo founder
I want to create projects and switch between them
So that I can keep work contexts separate

Acceptance Criteria:
- [ ] POST /projects creates a project with a unique id and folder
- [ ] GET /projects returns the list of all projects
- [ ] Switching project changes state.currentProject
- [ ] Project context (context.md) is included in each agent's prompt
- [ ] Folder browser (GET /browse?dir=path) works for selecting the working folder

Edge Cases:
- If workFolder does not exist → create directory automatically
- If projectId=null → getProjectWorkFolder() returns os.homedir()
```

## 3.3 Detailed Module Requirements

```
MODULE: Smart Onboarding
Purpose: personalized AI team setup via AI analysis of the user's profile
Related invariants: INV-003, INV-004, INV-007

User Flow:
1. User opens the app for the first time (onboardingDone:false)
2. Selection screen: Smart Setup | Manual selection
3. [Smart] User enters a self-description (textarea, 0/3000 characters)
4. Clicks "Analyze" → POST /onboarding/analyze → immediate ok
5. SSE: onboarding_analyzing (boss, setup) → progress bars in UI
6. SSE: onboarding_done → UI shows agent cards with analysis results
7. User edits/confirms the team
8. POST /onboarding/finalize → company.md, agents.json, memory, project, state
9. Auto-transition to the main screen

Business Rules:
BR-001: Parallel analysis calls
  Boss and Setup are launched via Promise.allSettled — not sequentially.
  Exceptions: none — always parallel.

BR-002: Mandatory agent guarantee
  finalizeOnboarding() always adds boss and setup to the list even if the user removed them.
  Edge case: user removed boss → system adds it back silently.

BR-003: Fallback on analysis error
  If both calls failed → onboarding_error { fallback:true } → manual mode.
  If one failed → result of the other + defaults for missing data.

BR-004: Custom agents for confirmation
  systemPrompt for custom agents is generated by Claude — always show to user before saving.

Input data: interviewText (string, up to 3000 characters)
Output data: { companyMd, recommendedAgents, customAgents, agentsMemory, systemPromptPatches, projectSuggestion }

Error handling:
- JSON parsing failed → extractJson() tries 4 strategies → fallback to defaults
- Timeout 90 sec → runAgentSync reject → onboarding_error + fallback
- Auth error → broadcastAuthError() + onboarding_error

Acceptance Criteria:
- [ ] POST /onboarding/analyze returns {ok:true} immediately
- [ ] Result arrives via SSE onboarding_done
- [ ] recommendedAgents always contains boss and setup
- [ ] Interview is saved in onboarding_interview.md
- [ ] customAgents ≤ 3 items
- [ ] Prompt patches are added via \n\nADDITIONAL CONTEXT:\n
```

```
MODULE: Agent Runner
Purpose: single point for launching the Claude subprocess for all modes
Related invariants: INV-003, INV-008

Functions:
- buildPrompt(agent, userMessage, projectId, history) — constructs the full prompt
  null-safe: if projectId=null → os.homedir(), empty strings for memory
- buildMcpConfig(agent, workFolder) — temporary JSON for --mcp-config
  filesystem: always (if in mcpServers)
  fetch/browser/gmail: only if in mcpServers AND *Enabled:true
- spawnClaude(agent, prompt, projectId) — unified launcher
  preferSubscription:true → remove ANTHROPIC_API_KEY from process.env before spawn
- handleStderr(data, agentName) — returns bool isAuth
- broadcastAuthError() — unified broadcast of auth_error for both modes
- runAgentStream(agent, prompt, projectId, onChunk, onDone, onError) — for UI
  loader in UI is mandatory regardless of chunk availability (streaming is buffered)
  timeout: 120 seconds
- runAgentSync(agent, prompt, projectId, maxRetries=2) — for plan and onboarding
  exponential backoff: 2000ms * attempt
  timeout: 90 seconds

Business Rules:
BR-005: Streaming and loader
  Loader is shown immediately on send and hidden only after agent_done.
  Reason: CLI buffers output, first chunk may arrive after 5–10 seconds.

BR-006: Auth handling
  handleStderr detects auth error → isAuth:true → broadcastAuthError() → stop execution.
  No retry on auth error.

Acceptance Criteria:
- [ ] spawnClaude removes ANTHROPIC_API_KEY when preferSubscription:true
- [ ] handleStderr returns bool, does not throw exceptions
- [ ] runAgentSync makes maxRetries attempts with exponential backoff
- [ ] Loader is shown before the first chunk and hidden after agent_done
```

```
MODULE: Plan Executor
Purpose: task decomposition via Boss and sequential plan execution
Related invariants: INV-006, INV-007

Constants:
- PLAN_AGENT_IDS = ['backend','frontend','devops','integrator','analyst','sales','qa','smm','lawyer']

Functions:
- extractJson(text) — SHARED function, 4 strategies:
  1. Direct JSON.parse
  2. From markdown block ```json
  3. First JSON object in text
  4. Repair broken JSON (trailing commas, unquoted keys, single quotes)
- extractPlan(text, userMessage) — uses extractJson + text fallback
  fallback: search for agent IDs and names in text → build plan from found items
- createPlan(taskId, userMessage, projectId) — Boss generates JSON plan
- executePlan(taskId, projectId, startFromStep=0) — sequential execution
  partial completion: if a step failed → continue from next
  plan_done with summary from Boss after completion

Error handling:
- extractPlan returned null → broadcast plan_error + retry / manual_chat buttons
- Agent from plan is not active → plan_step_skip with explanation
- Step failed → plan_step_error → continue to next step

Acceptance Criteria:
- [ ] extractJson does not throw an exception for any input text
- [ ] extractPlan returns null (does not throw) if plan is not found
- [ ] All step.task values are wrapped in String() before use
- [ ] plan_done contains a summary string
```

```
MODULE: Storage
Purpose: all read/write operations on disk

Data structure:
~/.solo-founder/
  company.md                   (max 1500 characters)
  agents_registry.json         master list (11 agents)
  agents.json                  active agents
  state.json                   { onboardingDone, onboardingMode, currentProject, preferSubscription:true }
  onboarding_interview.md      interview text
  agents_memory/
    {agentId}.md               personal memory (max 800 characters)
  mcp_configs/
    {agentId}_mcp.json         temporary MCP config
  projects/
    {projectId}/
      info.json
      context.md               (max 1000 characters)
      decisions.md             (max 800 characters)
      done.md                  (max 800 characters)
      artifacts/
      tasks/
        index.json
        {taskId}.json

Functions (all implemented):
readFileSafe(filepath, fallback=''), writeFileSafe(filepath, content)
getState(), saveState(data)
getCompany(), saveCompany(c)
loadRegistry(), loadActiveAgents(), saveActiveAgents(agents)
getAgentMemory(id), saveAgentMemory(id, c)  ← truncates to 800 characters
getProjects(), createProject(name, description, workFolder)
getProjectWorkFolder(projectId)  ← null-safe → os.homedir()
getProjectMemory(projectId, file), saveProjectMemory(projectId, file, content)
getTaskIndex(projectId), addToTaskIndex(projectId, task)
saveTask(projectId, task), loadTask(projectId, taskId)
compressMemoryLocal(content, maxChars)

Acceptance Criteria:
- [ ] readFileSafe never throws an exception (returns fallback)
- [ ] saveAgentMemory truncates content to 800 characters before writing
- [ ] getProjectWorkFolder(null) returns os.homedir()
- [ ] addToTaskIndex is called on every saveTask
```

## 3.4 Business Rules Registry

| ID | Rule | Priority | Invariant |
|---|---|---|---|
| BR-001 | Boss and Setup are launched in parallel via Promise.allSettled | High | INV-004 |
| BR-002 | boss and setup are forcefully added to the final agent list | High | INV-004 |
| BR-003 | If analysis failed → onboarding_error + fallback to manual mode | High | — |
| BR-004 | System-generated custom agents are shown to the user for confirmation before saving | High | — |
| BR-005 | Loader is shown before the first chunk, hidden after agent_done | High | — |
| BR-006 | Auth error → broadcastAuthError() → stop execution without retry | High | INV-003 |
| BR-007 | Agent memory is truncated to 800 characters on save | Medium | INV-005 |
| BR-008 | taskId only via generateTaskId() | High | INV-006 |
| BR-009 | extractJson() — single function for plan AND onboarding | High | INV-007 |
| BR-010 | spawnClaude() — single function for all Claude launches | High | INV-008 |

---

# SECTION 4. NON-FUNCTIONAL REQUIREMENTS

## 4.1 Performance

| Metric | Target |
|---|---|
| First agent response chunk | < 5 sec (streaming is buffered by CLI) |
| UI load | < 1 sec |
| Storage write | < 100ms |
| Smart Onboarding analysis | 30–60 sec (two parallel Claude calls) |
| First MCP launch | 30–60 sec (npx downloads the package) |

*On first MCP launch: show "Loading tools..." — this is normal, not a bug.*

## 4.2 Reliability

| Indicator | Value |
|---|---|
| Uptime | 100% while node server.js is running (local process) |
| Data recovery | Immediate — data is on disk, read on startup |
| Data loss | 0 — writeFileSafe is synchronous, FS journal |

*On server restart: all data in ~/.solo-founder/ is preserved.*

## 4.3 AI Performance Budget

| Metric | Limit |
|---|---|
| runAgentStream timeout | 120 seconds |
| runAgentSync timeout | 90 seconds |
| runAgentSync maxRetries | 2 attempts with exponential backoff 2000ms |
| Agent memory in prompt | ≤ 800 characters (INV-005) |
| Project context in prompt | ≤ 1000 characters (INV-005) |
| company.md in prompt | ≤ 1500 characters |
| Chat history | compress when > 20 messages (compressHistory) |

---

# SECTION 5. ARCHITECTURE

## 5.1 Overview

**Type:** Monolith (3 files)

**Diagram:**
```
[Browser] ←→ [server.js :3747]
               │
               ├─ GET /events (SSE stream) → push events in real time
               │
               ├─ API endpoints (REST)
               │    └─ Storage (readFileSafe/writeFileSafe)
               │         └─ ~/.solo-founder/ (FS)
               │
               └─ Agent Runner
                    └─ child_process.spawn("claude", ["-p", ...])
                         └─ MCP: --mcp-config {tmpfile}
                              └─ filesystem, gmail, fetch (if *Enabled:true)
```

**server.js sections (strictly in this order):**
```
// === STORAGE ===           All file operations
// === AGENT RUNNER ===      Launching Claude subprocess
// === PLAN EXECUTOR ===     Plan decomposition and execution
// === SMART ONBOARDING ===  Onboarding analysis and finalization
// === API ENDPOINTS ===     HTTP handlers
// === SSE ===               Server-Sent Events
```

**Layer rules:**
- Business logic (planning, onboarding) — only in server.js
- UI (index.html) — thin client, only display and events
- Storage — only via functions from the STORAGE section, not directly via fs

## 5.2 API Contracts

**Full list of endpoints:**

```
GET  /events                  SSE event stream
GET  /state                   { onboardingDone, onboardingMode, currentProject, preferSubscription }
POST /state                   { onboardingDone?, currentProject?, ... }
GET  /company                 text/plain — contents of company.md
POST /company                 { content: string }
GET  /agents                  [ ...activeAgents ]
POST /agents                  [ ...agentsToSave ] — hire/dismiss
GET  /agents/all              [ ...registry ] — all 11 agents
GET  /agents/:id/memory       text/plain
POST /agents/:id/memory       { content: string }
GET  /projects                [ ...projects ]
POST /projects                { name, description, workFolder }
GET  /projects/:id/memory/:file   text/plain — file: context|decisions|done
POST /projects/:id/memory/:file   { content: string }
GET  /projects/:id/tasks      [ ...tasks from index.json ]
GET  /projects/:id/tasks/:taskId  task object
POST /chat                    { agentId, message, projectId, history }
POST /chat/plan               { message, projectId } → { taskId }
PUT  /chat/plan/:taskId       { plan } — update plan before launching
POST /chat/plan/:taskId/start { projectId }
POST /chat/continue/:taskId   { agentId, message, projectId }
GET  /browse?dir=path         { dirs: [], files: [] }
GET  /integrations/status     { gmail, fetch, browser: bool }
POST /integrations/toggle     { agentId, integration, enabled }
POST /onboarding/analyze      { interviewText } → { ok: true } immediately
POST /onboarding/finalize     { companyMd, selectedAgentIds, customAgents, agentsMemory,
                                systemPromptPatches, projectName, projectDescription, workFolder }
                             → { ok: true, projectId, agentsCount }
```

**Error format:**
```json
{ "error": "ERROR_CODE", "message": "human-readable" }
```

**HTTP codes:**
- 200: success
- 400: invalid input data
- 404: resource not found
- 500: internal server error

## 5.3 SSE Events

```
// Connection
connected              { message: "Connected" }

// Agent
agent_status           { agentId, status: 'thinking'|'idle' }
agent_chunk            { agentId, taskId?, chunk: string }
agent_done             { agentId, taskId?, response: string }
agent_error            { agentId, taskId?, error: string }

// Planning
plan_creating          { taskId }
plan_created           { taskId, plan: [...steps] }
plan_started           { taskId }
plan_step              { taskId, stepIndex, agentId, task }
plan_step_done         { taskId, stepIndex, response }
plan_step_error        { taskId, stepIndex, error }
plan_step_skip         { taskId, stepIndex, reason }
plan_done              { taskId, summary }
plan_error             { taskId, error }

// Authentication
auth_error             { message, instruction }

// Smart Onboarding
onboarding_analyzing   { step: 'boss'|'setup', message: string }
onboarding_done        { result: { companyMd, recommendedAgents, customAgents,
                                   projectSuggestion, agentsMemory, systemPromptPatches } }
onboarding_error       { error: string, fallback: bool }
```

## 5.4 Agents (agents_registry.json)

11 base agents. All `*Enabled: false` by default.

| ID | Name | Role | Temperature |
|---|---|---|---|
| boss | Alex | CEO · Architect | ~0.7 |
| backend | Max | Backend Developer | ~0.2 |
| frontend | Artem | Frontend Developer | ~0.4 |
| devops | Igor | DevOps Engineer | ~0.2 |
| integrator | Katya | Integrator | ~0.3 |
| analyst | Roma | Business Analyst | ~0.4 |
| sales | Lera | Sales · Outreach | ~0.8 |
| qa | Lena | QA · Reviewer | ~0.1 |
| smm | Dima | SMM · Content | ~0.9 |
| lawyer | Yulia | Lawyer | ~0.2 |
| setup | Configurator | HR · Setup | ~0.5 |

`required: true` — boss, setup (cannot be disabled)
`mcpServers` — potential integrations, activated only when `*Enabled:true`

## 5.5 ADR + Decision Log

```
ADR-001: Three-file monolith
Context: minimal deployment needed without npm install
Decision: all code in server.js + index.html + agents_registry.json
Reason: user runs node server.js without any dependencies
Trade-offs: one large file, but maximum simplicity of launch
For agents: do not create additional files (INV-002)
```

```
ADR-002: Claude CLI via subprocess, not API
Context: user pays for a subscription, does not want an API key
Decision: child_process.spawn("claude", ["-p", prompt, ...flags])
Reason: preferSubscription:true — a fundamental requirement
Trade-offs: no direct control over tokens and cost
For agents: remove ANTHROPIC_API_KEY before spawn (INV-003)
```

```
ADR-003: SSE instead of WebSocket
Context: one-way server→client event stream is needed
Decision: Server-Sent Events (GET /events)
Reason: built into the browser, no external libraries needed, sufficient for the task
Trade-offs: no bidirectional channel, server→client only
```

```
DEC-001: File system as database
Question: where to store data (SQLite vs JSON files)
Decision: JSON + Markdown files in ~/.solo-founder/
Reason: no npm dependencies, data is human-readable, easy to debug
Rejected: SQLite — requires npm package (better-sqlite3)
```

```
DEC-002: Promise.allSettled for onboarding analysis
Question: run Boss + Setup analysis sequentially or in parallel
Decision: parallel via Promise.allSettled
Reason: saves 30–60 seconds of wait time
Rejected: sequential — too slow for UX
```

---

# SECTION 6. SECURITY

## 6.1 Authentication and Authorization

- Method: none (local application, localhost only)
- No external users
- PIN in state.json — optional feature for protection against unauthorized local access (Could Have)

## 6.2 Data Protection

- Encryption at rest: none (personal tool, acceptable)
- Encryption in transit: N/A (localhost)
- Third-party personal data: none

## 6.3 Secret Management

- No API keys in code
- Claude authorized via CLI (`claude auth login`)
- ANTHROPIC_API_KEY is removed from env when preferSubscription:true (INV-003)

## 6.4 Compliance

- EU/UK personal data: N/A (personal tool of the user about themselves)
- Payment data: N/A
- AI system (EU AI Act): Minimal risk — personal tool, no public users

---

# SECTION 7. INFRASTRUCTURE

## 7.1 Environments

| Environment | Data | Access |
|---|---|---|
| Production (the only one) | real user data | user only, locally |

*No staging or development environments — personal tool.*

## 7.2 Deployment

- Provider: localhost (Windows)
- Launch: `node server.js` or `start.bat`
- No CI/CD, no auto-deploy
- "Rollback": restart the previous version of the files

## 7.3 Monitoring

- Logs: console.log in the terminal where node server.js is running
- Alerts: none (local tool)
- Health indicator: browser tab at localhost:3747

## 7.4 Backup

- Data: `~/.solo-founder/` — user is responsible for their own backup
- Recommendation: periodically copy the folder to the cloud

---

# SECTION 8. TESTING

## 8.1 Strategy

| Level | How | Criterion |
|---|---|---|
| Smoke test | Run node server.js → open browser | HTTP 200 at localhost:3747 |
| Functional | Complete Smart Onboarding manually | onboardingDone:true in state.json |
| Integration | Send a message to an agent | agent_done with non-empty response |
| Regression | Check all 30 requirements from the checklist | All items completed |

## 8.2 Key Test Cases

```
TEST-001: Launch without npm install
Given:    only node.js is installed, repo is cloned
When:     node server.js
Then:     server starts without errors, browser opens at :3747
Invariant: INV-001
```

```
TEST-002: Smart Onboarding full cycle
Given:     onboardingDone:false, claude is authorized
When:      user enters a description and clicks "Analyze"
Then:      after 30–60 sec, agent cards appear with boss and setup in the list
           after "Accept" → state.json { onboardingDone:true, onboardingMode:'smart' }
           agents.json contains boss and setup
Invariant: INV-004
```

```
TEST-003: Fallback on analysis error
Given:     POST /onboarding/analyze was sent
When:      Claude returns invalid JSON
Then:      extractJson tries 4 strategies → fallback to empty object
           UI shows manual agent selection
Invariant: INV-007
```

```
TEST-004: Chat with agent
Given:     onboarding is complete, agent is selected
When:      user sends a message
Then:      loader appears immediately
           agent_chunk events arrive via SSE
           loader disappears after agent_done
Invariant: — (BR-005)
```

```
TEST-005: Auth error
Given:     claude is logged out or not authorized
When:      user sends a message to an agent
Then:      SSE auth_error with re-authorization instructions
           execution stops (no retry)
Invariant: INV-003
```

```
TEST-006: Planning mode
Given:     onboarding is complete, backend and other agents are present
When:      user sends a task via plan
Then:      Boss creates a plan in JSON
           plan is shown in UI
           execution proceeds step by step with SSE events
           plan_done with summary
```

```
TEST-007: Agent memory limit
Given:     saveAgentMemory('boss', string of 1000 characters)
When:      function is called
Then:      no more than 800 characters are written to agentId.md
Invariant: INV-005
```

## 8.3 Smoke Test (after every change)

```
□ node server.js starts without errors in the console
□ localhost:3747 opens in the browser
□ Onboarding screen or main screen is displayed correctly
□ Send a test message to an agent → receive agent_done
□ state.json and agents.json are read correctly
```

---

# SECTION 9. PROJECT MANAGEMENT

## 9.1 Decomposition

| Module | Depends on | Estimate |
|---|---|---|
| Storage (base functions) | — | foundation |
| Agent Runner | Storage | needed for everything |
| Manual onboarding | Storage, Agent Runner | fallback MVP |
| Smart Onboarding | Agent Runner, Storage | key feature |
| Chat with agent | Agent Runner, Storage | core function |
| Planning mode | Agent Runner, extractJson | core function |
| Project management | Storage | needed for chat |
| Memory management | Storage | Should Have |
| Artifacts | Storage | Should Have |

**Critical path:** Storage → Agent Runner → Chat → Onboarding → Planning

## 9.2 Risks

| Risk | Probability | Impact | Mitigation |
|---|---|---|---|
| Streaming is buffered by CLI, chunks are delayed | High | Low | Loader is mandatory regardless of chunks |
| First MCP launch takes 30–60 sec (npx downloads the package) | High | Medium | Show "Loading tools..." |
| Plan parsing fails even with 4 strategies | Medium | Medium | Retry and manual_chat buttons are mandatory |
| Both onboarding calls fail | Low | Medium | Fallback to manual mode + onboarding_error |
| Custom agents with bad systemPrompt from Claude | Medium | Low | Show to user for review before saving |

## 9.3 Explicitly Out of Scope

- Public deployment and HTTPS
- Multi-user mode and authentication
- Mobile app / PWA
- Configuration export / import
- Voice input
- pinnedExamples UI
- Fetch/Playwright/Gmail management via UI (toggles exist in code)
- Automatic backup

---

# SECTION 10. CHANGE SPECIFICATION

*Fill in before any change to existing code.*

```
CHANGE: [Change name]
Date: YYYY-MM-DD
Affected invariants: [INV-XXX]

CURRENT STATE:
[How it works right now — specific functions/files]

TARGET STATE:
[How it should work after]

WHAT WE DO NOT CHANGE:
- [function / endpoint] — do not touch
- [data structure] — do not touch

IMPACT ANALYSIS:
- Affected functions: [list]
- Risks: [what could break]

ROLLBACK:
- Trigger: [under what condition do we roll back]
- Procedure: restore previous version of the file from git/backup

REGRESSION CHECKS:
- [ ] Smoke test passed
- [ ] [what to check after the change]
```

---

# SECTION 11. AI-FIRST (SDD)

## 11.1 Agent Configuration

**Rule hierarchy:**
```
1. CLAUDE.md (global) → highest priority
2. This spec (SOLO_FOUNDER_OS_ANSS.md) → project rules
3. Session prompt → operational instructions
The lower level NEVER overrides the upper level.
```

**Forbidden Patterns for this project:**
```
DO NOT add require() of external npm packages (INV-001)
DO NOT create additional .js files other than server.js (INV-002)
DO NOT duplicate extractJson() logic (INV-007)
DO NOT create a second way to launch the Claude subprocess (INV-008)
DO NOT delete or rename server.js sections (STORAGE/AGENT RUNNER/etc.)
DO NOT hardcode the ~/.solo-founder/ path — only via the DATA_DIR constant
DO NOT generate taskId in any way other than via generateTaskId() (INV-006)
DO NOT remove boss and setup from active agents (INV-004)
DO NOT use ANTHROPIC_API_KEY when preferSubscription:true (INV-003)
```

## 11.2 Fail-Fast Conditions

```
THE AGENT STOPS when:
□ Asked to add an npm package
□ Asked to create an additional .js file
□ Duplicate extractJson/spawnClaude logic is detected
□ Any INV-XXX must be violated
□ An architectural choice is not described in the spec

THE AGENT ACTS INDEPENDENTLY on:
□ Variable naming details
□ CSS styles and spacing
□ Order of elements within a single section
□ Error message wording
□ Specific ASCII UI mockups (when logic is described)
```

## 11.3 LLM/AI Components

Solo Founder OS uses AI as an execution tool (not as the core product):

| Agent | Temperature | Purpose |
|---|---|---|
| boss | ~0.7 | planning, coordination |
| backend, devops, qa, lawyer | ~0.1–0.2 | precise technical tasks |
| integrator, analyst, frontend | ~0.3–0.4 | structured tasks |
| sales | ~0.8 | sales copy |
| smm | ~0.9 | creative content |

**AI invariants in this product:**
- An agent does not perform an action without a response from the Claude subprocess (no mock responses)
- Human Override: user can always interrupt plan execution
- EU AI Act: Minimal risk (personal tool, no public users)

---

# SECTION 12. CHECKLIST OF 30 REQUIREMENTS

*Complete list of mandatory implementation items.*

```
□ 1.  preferSubscription:true → remove ANTHROPIC_API_KEY (INV-003)
□ 2.  MCP via --mcp-config (temporary JSON file)
□ 3.  No npm install — built-in Node.js modules only (INV-001)
□ 4.  Three files: server.js, index.html, agents_registry.json (INV-002)
□ 5.  server.js sections: STORAGE / AGENT RUNNER / PLAN EXECUTOR /
       SMART ONBOARDING / API ENDPOINTS / SSE
□ 6.  ES modules: <script type="module">
□ 7.  Windows: path.join(), shell:true
□ 8.  generateTaskId() everywhere (INV-006)
□ 9.  Single spawnClaude() (INV-008)
□ 10. Single handleStderr() → bool isAuth
□ 11. Single broadcastAuthError() from both modes
□ 12. runAgentStream (UI chat) + runAgentSync (plan, onboarding)
□ 13. buildPrompt null-safe (projectId=null → os.homedir(), empty strings)
□ 14. buildMcpConfig: mcpServers=possible, *Enabled=activates
□ 15. All STORAGE functions implemented
□ 16. POST /agents implemented (hire/dismiss)
□ 17. extractJson() — shared function for plan AND onboarding (INV-007)
□ 18. extractPlan(text, userMessage) uses extractJson + text fallback
□ 19. analyzeOnboarding() — parallel Promise.allSettled (Boss + Setup)
□ 20. finalizeOnboarding() — saves company, agents, memory, project, state
□ 21. POST /onboarding/analyze → immediate response, result via SSE
□ 22. POST /onboarding/finalize → synchronous response with projectId
□ 23. Fallback: if analysis failed → onboarding_error {fallback:true} → manual
□ 24. Mandatory agents (boss, setup) always in the final list (INV-004)
□ 25. System-generated custom agents shown to user for confirmation
□ 26. Manual onboarding preserved as an alternative path
□ 27. All 11 agents: gmailEnabled:false, fetchEnabled:false, browserEnabled:false
□ 28. tasks/index.json updated on every saveTask
□ 29. Task statuses: draft→plan_pending→plan_running→done→failed
□ 30. Agent list everywhere: boss,backend,frontend,devops,integrator,
       analyst,sales,qa,smm,lawyer,setup
```

---

# SECTION 13. LIVING SPEC

```
□ The spec is updated in the same iteration as the code
□ A code change without updating the spec = technical debt
□ ADR and Decision Log are updated on architectural decisions
□ Invariants — only Artem can change them (not the agent on its own)
□ On invariant violation — the agent stops and reports it
```

---

# SECTION 14. AGENT REVIEW — MANDATORY BEFORE CODE

*The agent executes this section BEFORE starting to write code.*

```
PROMPT FOR THE AGENT:
"Read this spec and find:
1. Contradictions between sections
2. Situations not described but that will definitely occur
3. Edge cases absent from the specification
4. Missing Acceptance Criteria
5. Conflicts with the invariants from Section 2.5
6. Forbidden Patterns that may be violated

DO NOT suggest solutions — only list the problems.
If you find > 3 problems — stop and wait for answers."
```

**Agent Review result:**
*(filled in by the agent after reading the spec, before writing code)*

Problems found:
1. ...
2. ...

Questions before start:
1. ...

---

# SPEC READINESS CHECKLIST

```
MANDATORY
□ Invariants written (8 invariants — Section 2.5) ✅
□ Architectural principles chosen (8 principles — Section 2.6) ✅
□ What is NOT in scope — stated explicitly (Sections 1.1, 9.3) ✅
□ Each key module has Acceptance Criteria ✅
□ Acceptance Criteria are specific and verifiable ✅
□ Edge cases described for onboarding and chat ✅
□ API Contracts include all endpoints ✅
□ Technology stack is fixed (Section 2.4) ✅
□ Forbidden Patterns written (Section 11.1) ✅

QUALITY
□ No contradictions between sections ✅
□ All terms in the glossary (Section 2.1) ✅
□ Checklist of 30 requirements included (Section 12) ✅
□ Agent Review template ready (Section 14) ✅
□ Decision Log filled in (Section 5.5) ✅
```

---

*ANSS-Core v1.0 — Solo Founder OS — Artem Kholomyanskiy — 2026-06-01*
*Reworked from SOLO_FOUNDER_OS_SPEC_v2.5.md according to ANSS Standard v1.3*
