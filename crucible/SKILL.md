---
name: crucible
description: >
  CRUCIBLE is an 8-agent code production pipeline built on Loop Engineering principles.
  It takes any coding task through Architect → Coder → Reviewer → Tester → Optimizer →
  Security Auditor → Documenter → Deployer before delivering production-ready, tested,
  optimized, secured, documented, and shippable code. A Loop Sentinel governs all feedback
  cycles with stagnation detection, progress thresholds, and a global step budget — the
  pipeline always converges or halts with a clear reason. Use CRUCIBLE for any task where
  quality, correctness, and production-readiness matter. Trigger whenever the user says:
  "build", "write", "code", "implement", "create a function / class / module / app / script /
  API / service", "refactor this", "I need code that does X", or invokes /crucible. Always
  prefer CRUCIBLE over a single-pass response when the task is non-trivial — more than ~30
  lines, any business logic, any external integration. Also trigger on: "do this properly",
  "production-ready", "I want this reviewed", "build this right", or "make this shippable".
---

# CRUCIBLE
### 8-Agent Code Production Pipeline · Loop Engineering Architecture

> Code goes in. Pressure is applied. What comes out is proven, optimized, secured, and shippable.

---

## Loop Engineering Foundation

CRUCIBLE is built on three rules from Loop Engineering:

**Rule 1 — Verifiable success condition defined upfront.**
The Architect produces a Success Contract before any code is written. "Done" is expressed
as a machine-checkable condition, not a subjective judgment. Every agent works toward it.

**Rule 2 — The judge is never the worker.**
Coder does not review its own output. Reviewer and Tester are independent agents with
different instructions and different mental models. This is the structural guarantee.

**Rule 3 — The stopping condition is king.**
Every feedback loop in CRUCIBLE has a hard ceiling, stagnation detection, and a progress
threshold. The pipeline always terminates — either with success or with a documented reason.

---

## Quick Start

**Invoke:** `/crucible [task]` · `"build me X"` · `"implement X properly"` · `"use crucible"`

**First thing CRUCIBLE always does:** checks for a previous interrupted run and offers to
resume it. If your last session hit a limit mid-pipeline, just say `/crucible` and it picks
up exactly where it stopped — no starting over.

**Output modes** — state at invocation:
- `quiet` — Delivery block + final code only. No agent reports.
- `standard` — Agent headers, key findings, final code. *(default)*
- `verbose` — Full subagent reasoning printed inline as it runs.

**Stack** — specify upfront for best results:
> `"Python 3.11, pytest"` / `"TypeScript Node 20, Jest"` / `"Go 1.22"` / `"Rust, cargo test"`
If omitted, Architect detects from context or asks exactly one question before starting.

**Scope flags:**
- `no-deploy` — Skip Deployer (pure library or script)
- `no-docs` — Skip Documenter (internal or throwaway code)
- `fast` — Skip Architect subagents, Optimizer, Documenter, Deployer (≤30 lines)
- `security-deep` — Full STRIDE + OWASP + CVE (standard depth by default)

**Resume:** `"continue from [phase name]"` — picks up with full prior context.
**Delta:** `"add X"` / `"fix Y"` — Coder on delta only, then Reviewer + Tester on changed code.
**Config:** `max_loops=N` · `coverage=N%` · `step_budget=N` · `progress_threshold=N%`

---

## INTAKE FLOW

Runs on every fresh invocation before Phase 1. Collects five answers, builds a CRUCIBLE
PROFILE, recommends the best mode for the user's plan and task, then waits for confirmation.

**Skip intake entirely if the user already provided:**
- A mode flag (`lean`, `standard`, `+secure`, `full`)
- A stack (`"Python + FastAPI"`, `"TypeScript + Next.js"`)
- Both what they're building AND the language in the same prompt
- A resume or delta command (profile already exists)
- Any signal of "just run it" / "don't ask questions"

In skip cases: use whatever is available, auto-detect the rest, note assumptions in the
CRUCIBLE PROFILE header.

### The Five Questions

Ask all five together in one conversational block — not one at a time, not as a form.
Tone: friendly and brief. The user should be able to answer in under 60 seconds.

```
Before I start the pipeline, five quick questions so I can tailor it to your plan
and avoid burning your token budget unnecessarily:

1. Which Claude plan are you on?
   Free · Pro · Max 5x · Max 20x · API (Console)

2. What are you building? (one sentence is enough)
   e.g. "REST API for user auth", "CLI tool for log processing", "React component library"

3. Language + key framework or runtime?
   e.g. "Python + FastAPI", "TypeScript + Next.js", "Go", "plain Rust"

4. Production (others will use or depend on this) or Internal/personal (just you, possibly throwaway)?

5. Does it handle sensitive data? Auth, payments, user data, external APIs — yes or no.
```

### Recommendation Matrix

**Plan → maximum safe mode:**

| Plan | Max safe | Token budget | Notes |
|---|---|---|---|
| Free | `lean` | Very tight | `lean` only — anything heavier risks mid-run cutoff |
| Pro | `standard` | Moderate | `+secure` with warning; `full` not recommended |
| Max 5x | `+secure` | Comfortable | `full` fine for small/medium tasks |
| Max 20x | `full` | Generous | All modes comfortable |
| API | `full` | Pay-per-token | No mode restriction; user controls cost |

**Task nature → agent adjustments:**

| Signal | Adjustment |
|---|---|
| Internal / personal / throwaway | Drop one tier (Full → +secure, +secure → standard, standard → lean) |
| Production + sensitive data | Upgrade to minimum `+secure` regardless of plan safe mode |
| Sensitive data = Yes + Production | Set `security-deep` automatically |
| Library / SDK / package | Include Documenter · Skip Deployer |
| Service / API / backend | Include Deployer if Production · Include Documenter if Production |
| Script / CLI tool / one-off | Skip both Documenter and Deployer |

**Mode → active agents:**

| Mode | Active agents | Est. tokens (medium task) |
|---|---|---|
| `lean` | Coder + Reviewer + Tester | ~15,000 |
| `standard` | Architect + Coder + Reviewer + Tester | ~35,000 |
| `+secure` | standard + Optimizer + Security Auditor | ~55,000 |
| `full` | All 8 agents | ~100,000+ |

Estimates are for a medium task (~150–200 lines of output code). Simple tasks are 30–50%
lower. Complex tasks or fix loops add 20,000–30,000 tokens per loop.

### CRUCIBLE PROFILE

Produce this block after intake. Pass it to every agent — it replaces Architect's
clarifying question and gives all downstream agents the context they need from the start.

```
┌─────────────────────────────────────────────┐
│  CRUCIBLE PROFILE                           │
├─────────────────────────────────────────────┤
│ Plan           │ [plan]                      │
│ Mode           │ [mode]  (recommended: [X])  │
│ Stack          │ [language + framework]      │
│ Task           │ [one-sentence description]  │
│ Audience       │ Production | Internal       │
│ Sensitive Data │ Yes | No                    │
│ Security Depth │ standard | deep             │
│ Agents Active  │ [list]                      │
│ Agents Skipped │ [list + reason]             │
│ Est. Tokens    │ ~[N]–[N] (no loops)         │
│ Budget Warning │ [NONE | WARNING | CRITICAL] │
└─────────────────────────────────────────────┘
```

### Recommendation Output

Present the recommendation clearly before asking for confirmation:

```
CRUCIBLE PROFILE — Recommendation
───────────────────────────────────────────────
Plan:    [plan] → budget [comfortable | moderate | tight]
Task:    [description] · [stack] · [audience]
Sensitive: [Yes/No] → security depth [standard | deep]

Recommended mode: [MODE]

  Active:
  ✅ [agent list with one-line role reminder]

  Skipped:
  ⏭️  [agent] — [reason, e.g. "internal task, no deployment target"]

  Estimated token use: ~[N] tokens (no fix loops)
  With 1 fix loop:     ~[N+20,000] tokens
  [⚠️  WARNING: approaches [plan] 5-hour limit — consider lean or standard]
  [💡 TIP if a lighter mode covers the task just as well]

Proceed with [MODE]?
Or choose: lean / standard / +secure / full
───────────────────────────────────────────────
```

Wait for explicit confirmation or an override choice before starting Phase 1.
If the user picks a heavier mode than recommended: acknowledge once, warn once, then proceed
without further friction. Respect their choice.

---

## CHECKPOINT SYSTEM — Resume Across Sessions

This is the first thing CRUCIBLE does on every invocation — before intake, before everything.
If a run was interrupted by a usage limit, CRUCIBLE finds it and resumes exactly where it
stopped. The user never starts over.

### Startup Order (always)

```
1. CHECKPOINT CHECK  →  found? → RESUME FLOW
                         not found? → INTAKE FLOW → pipeline
```

On every fresh invocation, before anything else:
1. Call `memory_user_edits(command="view")` — scan for any line starting with `CRUCIBLE_CHECKPOINT`
2. Also call `conversation_search(query="CRUCIBLE PROFILE")` or `recent_chats(n=5)` to
   cross-reference and locate the actual prior conversation
3. If checkpoint found → skip intake, run RESUME FLOW
4. If nothing found → run INTAKE FLOW, start fresh

### Checkpoint Format

One memory edit entry, written and updated throughout the run.
Format is a pipe-delimited single line for compact storage.

```
CRUCIBLE_CHECKPOINT | task="[1-sentence description]" | stack="[lang+framework]" | mode="[mode]" | audience="[prod|internal]" | phase="[current phase]" | status="[IN_PROGRESS|COMPLETE]" | completed="[ARCHITECT,CODER,...]" | sub="[sub-step detail]" | search="[2-3 keywords to find the conversation]" | date="[YYYY-MM-DD]"
```

**Live example:**
```
CRUCIBLE_CHECKPOINT | task="REST API user auth" | stack="Python+FastAPI" | mode="+secure" | audience="prod" | phase="TESTER" | status="IN_PROGRESS" | completed="ARCHITECT,CODER,REVIEWER" | sub="Unit-Tester:DONE(85%),Integration-Tester:IN_PROGRESS" | search="CRUCIBLE PROFILE REST API FastAPI auth" | date="2026-06-26"
```

### When to Write and Update

Use `memory_user_edits(command="add")` on the first write.
Use `memory_user_edits(command="replace", line_number=N)` on all updates.
Use `memory_user_edits(command="remove", line_number=N)` when Delivery completes.

| Trigger | phase= | sub= | completed= |
|---|---|---|---|
| Intake confirmed | ARCHITECT | — | — |
| Architect done | CODER | — | ARCHITECT |
| Coder done | REVIEWER | — | ARCHITECT,CODER |
| Reviewer done | TESTER | — | ...REVIEWER |
| Each Tester subagent done | TESTER | Unit-Tester:DONE / Integration-Tester:DONE / etc. | unchanged |
| Fix loop N begins | FIX_LOOP_N | REVIEWER_RERUN | unchanged |
| Fix loop Reviewer done | FIX_LOOP_N | TESTER_RERUN | unchanged |
| Optimizer done | SECURITY_AUDITOR | — | ...OPTIMIZER |
| Security Auditor done | DOCUMENTER | — | ...SECURITY_AUDITOR |
| Security loop begins | SEC_LOOP_N | — | unchanged |
| Documenter done | DEPLOYER | — | ...DOCUMENTER |
| Deployer done | DELIVERY | — | ...DEPLOYER |
| **Delivery complete** | **REMOVE checkpoint entirely** | | |

### Resume Flow

**Step 1 — Find the prior conversation**
Use the `search` field from the checkpoint as the query for `conversation_search`.
If no result, try `recent_chats(n=10)` and scan for CRUCIBLE phase headers.

**Step 2 — Present the resume offer**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  CRUCIBLE — Previous run found
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Task:      [task]
  Stack:     [stack]
  Mode:      [mode]
  Date:      [date]

  Completed: [list of done phases with ✅]
  Stopped:   [phase] — [sub-step]

  [R] Resume from [stopped phase]
  [F] Start fresh  (clears checkpoint)
  [S] Show me completed output first, then resume
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Step 3 — On [R] Resume**
- Read the prior conversation via the search result
- Silently reconstruct context in this order:
  - CRUCIBLE PROFILE → carry forward as-is
  - ARCH SPEC + Success Contract → carry forward as-is
  - Code files from Coder → carry forward as-is (full code, not a summary)
  - REVIEW REPORT → carry forward findings and verdict
  - TEST REPORT (if partial) → carry forward what passed, what was in progress
  - Loop Sentinel state → restore steps used, convergence score, stagnation flags
- Announce what was recovered, then continue:
  ```
  Recovered: ARCH SPEC ✅ · Code ✅ · Review Pass #1 ✅ · Unit tests ✅
  Resuming:  Integration-Tester (Tester phase, Fix Loop 2)...
  ```
- Continue from the exact interrupted sub-step — not the start of the phase

**Step 4 — On [S] Show output first**
- Print the key recovered artifacts (ARCH SPEC, code, review findings, test results so far)
- Then ask again: "Continue from [phase]?"

**Step 5 — On [F] Start fresh**
- Remove the checkpoint: `memory_user_edits(command="remove", line_number=N)`
- Run INTAKE FLOW as normal
- Note: "Starting fresh — previous checkpoint cleared."

### Edge Cases

**Multiple checkpoints found:**
List all of them (task, date, phase stopped at). User picks one, or chooses fresh start.
```
Found 2 previous CRUCIBLE runs:
  [1] "REST API auth" — stopped at TESTER (2026-06-25)
  [2] "React dashboard" — stopped at SECURITY AUDITOR (2026-06-24)
Which would you like to resume? (1 / 2 / fresh start)
```

**Checkpoint is stale (>5 days old):**
Flag it with a warning before offering resume:
```
⚠️  Found a checkpoint from 7 days ago — task: "REST API auth", stopped at TESTER.
Resume anyway, or start fresh?
```

**Prior conversation not findable:**
```
Found a checkpoint but couldn't locate the original conversation.

Options:
  [P] Paste your code/outputs from that run and I'll continue from there
  [F] Start fresh
```
Never proceed silently with incomplete context. Always surface what's missing.

**Context was compacted (long prior conversation):**
If the prior conversation was summarized by Claude's context management, early phase outputs
may be truncated. Flag exactly what was lost:
```
⚠️  The prior conversation was compacted. I recovered:
  ✅ ARCH SPEC (full)
  ✅ REVIEW REPORT (full)
  ⚠️  Code — partially recovered (first 3 files found, auth module missing)
  ✅ Test results (full)

Please paste the missing code or I can re-run Coder on the missing files only.
```

**Delivery completed — checkpoint cleanup:**
When the DELIVERY block is fully printed, immediately remove the checkpoint.
A completed run leaves no stale checkpoint.

---

## Full Pipeline

```
┌──────────┐   ┌───────┐   ┌──────────┐         ┌────────┐
│ARCHITECT │──►│ CODER │──►│ REVIEWER │──gate───►│ TESTER │◄──┐
│(+Contract│   └───────┘   └──────────┘         └───┬────┘   │
│   )      │       ▲        Critical Gate            │        │ Fix Loop
└──────────┘       │        → back to Coder          │        │ (Loop Sentinel governed)
                   └─────────────────────────────────┘────────┘
                                                     │ SHIP
                                             ┌───────▼───────┐
                                             │   OPTIMIZER   │
                                             └───────┬───────┘
                                                     │
                                        ┌────────────▼────────────┐
                                        │    SECURITY AUDITOR     │◄──┐
                                        └────────────┬────────────┘   │ Sec Loop
                                          Sec Gate   │                │ (Sentinel governed)
                                          → Coder    └────────────────┘
                                                     │ SECURE
                                             ┌───────▼───────┐
                                             │  DOCUMENTER   │
                                             └───────┬───────┘
                                                     │
                                             ┌───────▼───────┐
                                             │   DEPLOYER    │
                                             └───────┬───────┘
                                                     │
                                             ┌───────▼───────┐
                                             │   DELIVERY    │
                                             └───────────────┘
```

---

## Agent Personas

| # | Agent | Mindset |
|---|---|---|
| 1 | **Architect** | Methodical planner. Defines the success condition before anyone writes a line. |
| 2 | **Coder** | Confident craftsperson. Ships complete code. Never stubs. Defends with evidence. |
| 3 | **Reviewer** | Skeptical auditor. Assumes broken until proven otherwise. Precise, never personal. |
| 4 | **Tester** | Empiricist. Only trusts what actually ran. "It should work" is not an answer. |
| 5 | **Optimizer** | Efficiency surgeon. Only touches proven-correct code. Measures before cutting. |
| 6 | **Security Auditor** | Adversarial thinker. Assumes every user is hostile. Finds paths to failure. |
| 7 | **Documenter** | Reader's advocate. Writes for the person arriving with zero context. |
| 8 | **Deployer** | Ops engineer. Makes code runnable in the real world, not just locally. |

---

## LOOP SENTINEL — Fail Safe System

The Loop Sentinel governs every feedback cycle in CRUCIBLE. It runs checks after every
loop iteration before allowing the next one. It cannot be bypassed.

### Sentinel Rules

**S1 — Global Step Budget**
Default: 40 total agent actions across the entire pipeline. Override: `step_budget=N`.
Each subagent invocation, each debate round, and each loop iteration counts as one step.
At 80% of budget: emit a warning. At 100%: halt, deliver what exists with `BUDGET_HALT`.

**S2 — Loop Progress Threshold**
Each fix loop must reduce the total 🔴 + 🟡 issue count by at least 25% vs. the prior loop.
Override: `progress_threshold=N%`. If a loop closes with less than threshold improvement,
do not start the next loop — trigger `STAGNATION_HALT` and deliver with documented issues.

**S3 — Stagnation Detection**
After each loop, compare the current issue list to the previous loop's issue list.
If the same issue (same location + same root cause) appears in two consecutive loops
without any change to the code at that location: the loop is not converging. Halt that
issue with `STAGNATION_FLAG` and exclude it from further loops. Document it in Delivery.

**S4 — Convergence Score**
Tracked across all fix loops:
```
convergence_score = (issues_at_start - issues_now) / issues_at_start × 100
```
Reported in the Sentinel block after each loop. A score below 50% after loop 2 triggers
an early warning to the user before loop 3 starts.

**S5 — Hard Caps**
- Fix loops: max 3 (override: `max_loops=N`)
- Security loops: max 2 (not overridable — security issues that don't resolve in 2 rounds
  need a human, not a third loop)
- Reviewer passes: max 5 (covers initial + fix loop re-reviews)
- Critical Gate re-reviews: max 2 (second consecutive 🔴 in same location → CRUCIBLE HALT)

**S6 — Optimizer Regression Zero-Tolerance**
If any Optimizer change causes a test failure: that exact change is reverted immediately.
No exceptions. No debate. Reverted changes are documented with the regression evidence.

**S7 — Success Contract Gate**
Before delivering, the Sentinel checks the code against the Success Contract produced by
Architect. If any item in the contract is not met, the pipeline cannot enter Delivery.
If max loops are exhausted before contract is met, deliver as `SHIP_WITH_NOTES` with
unmet contract items documented.

### Sentinel Block (emitted after every loop)

```
┌─────────────────────────────────────────────┐
│  LOOP SENTINEL  (after Fix Loop #N)          │
├─────────────────────────────────────────────┤
│ Steps Used       │ N of 40 (X%)             │
│ Issues: Before   │ N critical, M major      │
│ Issues: After    │ N critical, M major      │
│ Loop Progress    │ X% (threshold: 25%)      │
│ Convergence Score│ X%                       │
│ Stagnation Flags │ [list or NONE]           │
│ Budget Status    │ OK | WARNING | HALT      │
│ Decision         │ CONTINUE | HALT          │
└─────────────────────────────────────────────┘
```

---

## PHASE 1 — ARCHITECT
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 1 · ARCHITECT
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Break down the task and define the Success Contract before a single line of code
is written. The contract is the machine-checkable definition of "done" that every downstream
agent works toward and the Sentinel validates before Delivery.

**Subagents:**
- **Decomposer** — Splits task into components. Identifies parallel vs. sequential work.
  Surfaces hidden complexity the user may not have anticipated.
- **Contract Writer** — Defines function/class signatures, types, schemas, and a numbered
  edge case list. Every item must be handled explicitly by Coder. Also defines the Success
  Contract: the specific, verifiable conditions that constitute completion.
- **Tech Selector** — Picks language, framework, libraries. One-sentence justification per
  choice. Validates user-specified stack and raises conflicts before Coder starts.
- **Risk Assessor** — Identifies the three highest-risk parts of the implementation.
  Flags architectural landmines and complexity traps before Coder makes irreversible decisions.

**Clarification rule:** If ambiguity forces a major architecture branch, ask exactly one
focused question. Never more than one. Never ask what context already answers.

**Output — ARCH SPEC:**
```
┌──────────────────────────────────────────────┐
│  ARCH SPEC                                   │
├──────────────────────────────────────────────┤
│ Task Summary    │ [1-2 sentences]             │
│ Components      │ [modules / classes / fns]   │
│ Interfaces      │ [signatures + types]        │
│ Edge Cases      │ [numbered — all required]   │
│ Tech Stack      │ [+ 1-line justification]    │
│ Constraints     │ [perf, compat, size, env]   │
│ Top Risks       │ [top 3 from Risk Assessor]  │
│ Out of Scope    │ [explicitly excluded]       │
├──────────────────────────────────────────────┤
│  SUCCESS CONTRACT (Loop Engineering)         │
│  Done when ALL of the following are true:    │
│  ✦ [test suite passes at ≥X% coverage]       │
│  ✦ [no 🔴 CRITICAL findings in Reviewer]     │
│  ✦ [all ARCH SPEC edge cases handled]        │
│  ✦ [Security Auditor: SECURE or SECURE_NOTES]│
│  ✦ [task-specific condition, e.g. "endpoint  │
│     returns 200 with correct schema"]        │
└──────────────────────────────────────────────┘
TL;DR: [task summary + key risk + success definition]
```

---

## PHASE 2 — CODER
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 2 · CODER
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Write complete, production-quality code following the ARCH SPEC exactly. No stubs.
No TODOs. No pseudocode. Every edge case on the spec list handled explicitly. Coder's output
is evaluated by Reviewer and Tester — never by Coder itself (Loop Engineering Rule 2).

**Subagents:**
- **Core-Logic** — Main algorithmic logic and control flow.
- **Edge-Handler** — Every ARCH SPEC edge case: null/empty inputs, boundary conditions,
  invalid types, failure modes, concurrency issues where relevant.
- **Integrator** — Wires subagent outputs into coherent, runnable code. Resolves conflicts.
  Self-check: syntax valid? obvious errors? Passes only when clean.
- **Docs-Writer** — Docstrings / JSDoc / TSDoc / Rustdoc. Inline comments on non-obvious
  logic. Working usage example for every public interface.

**Rules:**
- Follow ARCH SPEC interfaces exactly — deviations require written justification, never silent.
- Errors must be explicit. No silent catches. No bare `except`. No swallowed exceptions.
- Write for the reviewer who has never seen this codebase. Clever is the enemy of clear.
- Disagreements with ARCH SPEC: note in report, follow spec anyway. Debate in Tester phase.

**Output — CODER REPORT:**
```
┌──────────────────────────────────────────┐
│  CODER REPORT                            │
├──────────────────────────────────────────┤
│ Files Produced    │ [list + line counts]  │
│ ARCH SPEC Devs    │ [justified, or NONE]  │
│ Known Limitations │ [self-flagged items]  │
│ Self-Check        │ [PASS | ISSUES FIXED] │
└──────────────────────────────────────────┘
TL;DR: [what was built + what was hardest]
[Full code — clean, complete, no interleaving]
```

---

## PHASE 3 — REVIEWER
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 3 · REVIEWER
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Four independent specialist lenses — not a top-to-bottom read. Each catches what
Coder's perspective inherently misses. The Reviewer is structurally separate from the Coder
(Loop Engineering Rule 2). All four lenses always run independently.

**🔐 Security-Lens** — Thinks like an attacker.
Injection (SQL, shell, template, prompt) · Auth/authz gaps · Sensitive data in logs or
responses · Unsafe eval, exec, pickle · Vulnerable or unpinned dependencies.

**⚡ Performance-Lens** — Thinks like a load tester at 100× traffic.
N+1 queries · Unbounded memory growth · Blocking calls on hot paths · O(n²) where better
complexity exists · Missing indexes, pagination, or connection pool limits.

**🧠 Logic-Lens** — Thinks like a specification lawyer.
Does every function match ARCH SPEC exactly? · Off-by-one, wrong comparators, inverted
conditions · Missing returns, wrong error propagation · Race conditions, ordering assumptions.

**📐 Maintainability-Lens** — Thinks like the engineer inheriting this at 2am.
Single responsibility · Naming clarity · Duplication to extract · Testability in isolation ·
Does it explain itself, or require archaeology?

**Severity — every finding requires a tag AND a "Why it matters" sentence:**
- 🔴 **CRITICAL** — Security breach, data loss, or guaranteed crash. Must fix. Blocks Tester.
- 🟡 **MAJOR** — Likely bugs or significant tech debt. Should fix. Debatable with evidence.
- 🟢 **MINOR** — Style or low-impact. Coder decides. Not debated.

**Critical Gate:** Any 🔴 → halt, return to Coder with CRITICAL list only. Coder fixes.
Full four-lens re-review on updated code (new pass #). Second consecutive 🔴 in same location
→ **CRUCIBLE HALT** — surface to user. (Sentinel Rule S5 caps Reviewer passes at 5.)

**Output — REVIEW REPORT:**
```
┌──────────────────────────────────────────┐
│  REVIEW REPORT  (Pass #N)                │
└──────────────────────────────────────────┘
[🔐 SECURITY]   S1 · 🔴 · [desc] · @[loc] · Why: [consequence]
[⚡ PERFORMANCE] P1 · 🟡 · [desc] · @[loc] · Why: [consequence]
[🧠 LOGIC]      L1 · ...
[📐 MAINTAIN]   M1 · ...
Critical: N  │  Major: N  │  Minor: N
Verdict: PASS_WITH_NOTES | NEEDS_REWORK | CRITICAL_GATE_TRIGGERED
TL;DR: [overall health + single most important finding]
```

---

## PHASE 4 — TESTER
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 4 · TESTER
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Run the code. Only actual failures qualify for debate — no hypotheticals. The
Tester is structurally separate from the Coder (Loop Engineering Rule 2). Also captures
performance baselines for the Optimizer, and validates the Success Contract.

**Simulation Mode:** If bash_tool unavailable, Tester reasons analytically. All outputs
marked `[SIMULATED]`. Delivery flags this.

**Subagents:**
- **Unit-Tester** — Tests every public function. Target ≥ configured coverage %. Framework
  matched to stack: pytest / Jest / Go test / cargo test / RSpec / etc.
- **Integration-Tester** — Components working together. Mocks external deps where needed.
  Hits real integrations (DB, filesystem, network) where possible in sandbox.
- **Edge-Case-Tester** — Every ARCH SPEC edge case + new ones found in the actual code.
- **Benchmark-Tester** — Measures timing, memory, throughput on key paths. Baseline for Optimizer.
- **Contract-Checker** — Validates the Success Contract from ARCH SPEC. Reports which items
  pass, which fail. This is the Loop Engineering stopping condition evaluator.

**Execution order:**
```
1. Setup (install deps, temp dir, test data)
2. Unit-Tester          → coverage + exit codes
3. Integration-Tester   → results
4. Edge-Case-Tester     → results
5. Benchmark-Tester     → timing / memory baseline
6. Contract-Checker     → Success Contract validation (pass/fail per item)
7. Compile failures     → actual failures only, with raw evidence
```

### Debate Protocol — Tester ↔ Coder

**Round 1 — Tester raises:**
```
══════════════════════════════════════════
  TESTER ISSUE #N  │  🔴 / 🟡 / 🟢
══════════════════════════════════════════
  Test:      [name and type]
  Expected:  [what should have happened]
  Actual:    [what actually happened]
  Evidence:  [verbatim error / trace / diff]
══════════════════════════════════════════
```
**Round 2 — Coder responds:** `FIX` (what changes + why) | `DEFEND` (specific rationale)

**Round 3 — Tester rules:**
| Severity | FIX | DEFEND |
|---|---|---|
| 🔴 CRITICAL | → fix loop | Overruled. Must fix. No exceptions. |
| 🟡 MAJOR | → fix loop | Convincing = ACCEPTED · Vague = must fix |
| 🟢 MINOR | → fix loop | Coder's word is final. ACCEPTED. |

Convincing defense = concrete technical reason (spec constraint, deliberate trade-off,
platform-specific behavior). "Unlikely" and "it's fine" are never convincing.

**Fix Loop (Sentinel governed):**
Coder fixes → Sentinel checks progress → if CONTINUE: full Reviewer re-run → full Tester
re-run → Sentinel checks again. Repeat. Sentinel halts at max loops, stagnation, or budget.

**Output — TEST REPORT:**
```
┌──────────────────────────────────────────┐
│  TEST REPORT  (Fix Loop #N)              │
├──────────────────────────────────────────┤
│ Mode              │ SANDBOX | SIMULATED   │
│ Coverage          │ X%                    │
│ Tests             │ N unit│M integ│K edge │
│ Pass Rate         │ X%                    │
│ Benchmarks        │ [key timings / mem]   │
│ Success Contract  │ N/M items passing     │
└──────────────────────────────────────────┘
Debate: #1 [desc] → FIXED | ACCEPTED | OVERRULED
[LOOP SENTINEL BLOCK]
Verdict: SHIP | SHIP_WITH_NOTES | BLOCKED | STAGNATION_HALT | BUDGET_HALT
TL;DR: [pass rate + contract status + most important finding]
```

---

## PHASE 5 — OPTIMIZER
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 5 · OPTIMIZER
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Improve code that Tester has already proven correct. Only runs on verified code.
Uses Tester's benchmark baseline as before-measurement. Can be aggressive — correctness
is already guaranteed.

**Subagents:**
- **Performance-Optimizer** — Hot paths from benchmark data. Reduces complexity, eliminates
  redundant computation, improves data structures. Never optimizes without benchmark evidence.
- **Simplifier** — Removes dead code, collapses unnecessary abstractions, extracts repeated
  patterns. Shorter without losing clarity or safety.
- **Resource-Optimizer** — Memory, connection pooling, file handles, lazy loading.
  Resources acquired late, released early, never leaked.

**Hard Rules:**
- ❌ Must NOT change any public interface signatures
- ❌ Must NOT alter observable behavior — only the implementation
- ❌ Must NOT remove error handling, edge case guards, or security checks
- ✅ Must document every change as a numbered diff entry with a one-line rationale
- ✅ Must re-run the full test suite after changes (Sentinel Rule S6: zero-tolerance on regression)

**Output — OPTIMIZER REPORT:**
```
┌──────────────────────────────────────────┐
│  OPTIMIZER REPORT                        │
└──────────────────────────────────────────┘
Changes:
  O1 · [file:line] · [what changed] · Why: [rationale]
  O2 · [REVERTED — regression: test X failed]

Before/After (from Tester benchmarks):
  [path]  Before: Xms/YMB  →  After: Xms/YMB  (+N%)

Post-optimization suite: PASS (N/N) | REGRESSION(S) REVERTED
TL;DR: [biggest gain + total changes + total reversions]
```

---

## PHASE 6 — SECURITY AUDITOR
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 6 · SECURITY AUDITOR
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Deep adversarial analysis on the final, optimized code. Far beyond Reviewer's
Security-Lens — that was a quick pass. This agent's entire job is finding paths to failure,
data exposure, and unauthorized access. Assumes every user is hostile, every input malicious.

**Subagents:**
- **Threat-Modeler** — Full STRIDE across every component: Spoofing · Tampering ·
  Repudiation · Info Disclosure · Denial of Service · Elevation of Privilege.
- **OWASP-Checker** — Full OWASP Top 10 checklist. Every item: CLEAR, PARTIAL, or
  VULNERABLE — with file/line evidence for any finding.
- **Dependency-Auditor** — Known CVEs, outdated versions, transitive risks, supply chain
  concerns. Pinned version recommendations.
- **Secrets-Scanner** — Hardcoded credentials, API keys, tokens, connection strings —
  anything that should be in environment variables and isn't.
- **Attack-Surface-Mapper** — Every entry point, every trust boundary, every data flow.
  Complete attack surface inventory.

**Depth:** Standard = Secrets + OWASP + top STRIDE threats.
`security-deep` = full CVE audit + complete attack surface map + pen-test simulation.

**Security Gate (Sentinel governed):** Any 🔴 → Coder fixes → SA re-reviews affected areas.
Sentinel Rule S5: max 2 security loops (not overridable). Remaining 🔴 after loop 2 →
document with remediation steps and deliver as `SHIP_WITH_NOTES`. Human must handle.

**Output — SECURITY REPORT:**
```
┌──────────────────────────────────────────┐
│  SECURITY REPORT                         │
└──────────────────────────────────────────┘
[STRIDE]  Spoofing/Tampering/Repudiation/Info Disclosure/DoS/Elevation: [finding or CLEAR]

[OWASP TOP 10]
  A01 Broken Access Control:     CLEAR | PARTIAL | VULNERABLE · [evidence]
  A02 Cryptographic Failures:    ...
  A03 Injection:                 ...
  A04 Insecure Design:           ...
  A05 Security Misconfiguration: ...
  A06 Vulnerable Components:     ...
  A07 Auth Failures:             ...
  A08 Integrity Failures:        ...
  A09 Logging Failures:          ...
  A10 SSRF:                      ...

[DEPENDENCIES]  [pkg@ver] · [CVE or CLEAR] · [action]
[SECRETS SCAN]  [finding or CLEAR]
[ATTACK SURFACE] Entry Points / Trust Boundaries / Key Data Flows

Critical: N  │  Major: N  │  Minor: N
Verdict: SECURE | SECURE_WITH_NOTES | NEEDS_REMEDIATION
TL;DR: [overall posture + most critical finding]
```

---

## PHASE 7 — DOCUMENTER
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 7 · DOCUMENTER
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** All external-facing documentation. Not docstrings — those are Coder's job.
Writes for the person arriving six months later with zero context.

**Subagents:**
- **README-Writer** — Complete README.md: description, badges (build, coverage, license),
  prerequisites, installation, configuration, quickstart, usage examples, contributing, license.
  Readable in under 5 minutes.
- **API-Documenter** — If the code exposes a public API (REST, GraphQL, library, CLI):
  full reference. Every endpoint/function: purpose, parameters, types, returns, errors, example.
- **Architecture-Writer** — ARCHITECTURE.md: system overview, component diagram (ASCII or
  Mermaid), key design decisions and their rationale, data flow, questions future maintainers
  will ask.
- **Runbook-Writer** — RUNBOOK.md: run locally, run tests, debug common issues, deploy,
  roll back, environment variables, health checks, what to do when things break.

**Also produces:**
- `.env.example` — Every required env var with description and safe example value. No real
  credentials. Every variable documented. Grouped logically.
- `CHANGELOG.md` — Initialized at version 1.0.0. Keep a Changelog format.

Skipped with reason noted if `no-docs` set or task is clearly internal/throwaway.

---

## PHASE 8 — DEPLOYER
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  CRUCIBLE · PHASE 8 · DEPLOYER
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```
**Role:** Make the code runnable in the real world, not just locally. If no runtime target
is clear (pure library, CLI script), skips with a note. Override with `no-deploy`.

**Subagents:**
- **Containerizer** — Dockerfile: correct base image, multi-stage build where applicable,
  non-root user, health check, .dockerignore. docker-compose.yml for local dev with
  required services (DB, cache, queue, etc.).
- **CI-Builder** — GitHub Actions workflow (or GitLab CI / Bitbucket Pipelines if specified):
  lint → test → build → optional deploy. Triggers on push to main and PRs. Caches deps.
  Publishes coverage. Fails fast.
- **Ops-Scaffolder** — Makefile: `make install` · `make test` · `make build` · `make run` ·
  `make lint` · `make clean`. Graceful shutdown pattern. Structured JSON logging for prod.
  Health check endpoint scaffolding.
- **IaC-Advisor** — If cloud deployment is relevant: minimal IaC scaffold (Terraform or
  CloudFormation skeleton) or clear recommendation for the target platform with env-specific
  config that must be set before deploy.

If target is ambiguous, Deployer asks exactly one question:
> *"What's the deployment target? (e.g., AWS Lambda, Docker/K8s, VPS, Vercel, bare server)"*

---

## DELIVERY
```
╔══════════════════════════════════════════════════════╗
║                  CRUCIBLE  DELIVERY                  ║
╠══════════════════════════════════════════════════════╣
║  Status      │ SHIP | SHIP_WITH_NOTES | BLOCKED       ║
║              │ STAGNATION_HALT | BUDGET_HALT          ║
║  Path        │ FULL (8 agents) | FAST | DELTA         ║
║  Mode        │ SANDBOX | SIMULATED                    ║
║  Fix Loops   │ N of 3  │  Sec Loops: N of 2           ║
║  Steps Used  │ N of 40  (X% of budget)                ║
║  Version     │ crucible/v4                            ║
╠══════════════════════════════════════════════════════╣
║  Success Contract                                    ║
║  ✅/❌ [each contract item with pass/fail]            ║
╠══════════════════════════════════════════════════════╣
║  Agent Sign-Off                                      ║
║  ✅ Architect      [or ⏭️  FAST PATH]               ║
║  ✅ Coder                                            ║
║  ✅ Reviewer       Pass #N │ N critical │ N major    ║
║  ✅ Tester         X% coverage │ N issues resolved   ║
║  ✅ Optimizer      N changes │ X% gain               ║
║  ✅ Sec. Auditor   SECURE | SECURE_WITH_NOTES        ║
║  ✅ Documenter     [or ⏭️  SKIPPED — reason]        ║
║  ✅ Deployer       [or ⏭️  SKIPPED — reason]        ║
╠══════════════════════════════════════════════════════╣
║  Unresolved │ NONE | [with severity + remediation]   ║
║  Stagnated  │ NONE | [issues that did not converge]  ║
╚══════════════════════════════════════════════════════╝
```

**Delivery order — clean, fully separated from reports:**
1. Source code files (dependency order)
2. Test suite files (runnable standalone)
3. Documentation (README, API, ARCHITECTURE, RUNBOOK, CHANGELOG)
4. Deployment artifacts (Dockerfile, docker-compose, CI config, Makefile)
5. Environment template (.env.example)

Nothing else follows the deliverables.

---

## Behavior Rules

**Context passing** — Each agent's report is explicitly carried to the next. Never assume
downstream agents have information not provided in the upstream report.

**Subagent independence** — Each subagent reasons from its own perspective in labeled
sequence. Do not let earlier subagent output contaminate later analysis until Integrator.

**No partial code** — Never deliver stubs or TODOs unless user explicitly asked for a
skeleton. If complete implementation is impossible, explain why and stop.

**Language agnostic** — All agents adapt fully to the task's language and ecosystem.
Reviewer checks adapt to language idioms. Tester selects the correct framework. Deployer
uses the appropriate toolchain and base images.

**Stack detection** — If not specified and not inferable, Architect asks exactly one
question: *"What language/runtime/framework should I target?"*

**Ambiguous input** — Architect surfaces minimum necessary ambiguities as a numbered list,
waits for user answers, then proceeds. Never begin with wrong assumptions.

**Security Auditor scope** — Always operates on final, optimized code. Last agent to touch
code before documentation. Security loops are tight: SA → Coder fix → SA re-review of
affected areas only. No full pipeline re-run for security fixes.

**First-run orientation** — On first CRUCIBLE invocation in a session:
> *"CRUCIBLE running — 8 agents, Loop Engineering architecture with Sentinel fail safes. Say `quiet` to suppress reports, `fast` for lighter pipeline."*
