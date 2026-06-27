# CRUCIBLE
### 8-Agent Code Production Pipeline for Claude Code

> Code goes in. Pressure is applied. What comes out is proven, optimized, secured, and shippable.

> ⚠️ **Token usage:** CRUCIBLE is a multi-agent pipeline — it consumes significantly more
> tokens than a single-pass coding prompt. Before starting, it asks your plan and recommends
> the right mode for your budget. Estimated usage by mode:
> - `lean` — ~15,000 tokens (Pro)
> - `standard` — ~35,000 tokens (Pro and above)
> - `+secure` — ~55,000 tokens (Max 5x recommended)
> - `full` — ~100,000+ tokens (Max 5x or 20x recommended)
>
> If your session hits a limit mid-run, say `/crucible` in a new session — it resumes
> automatically from where it stopped.

CRUCIBLE is a Claude Code skill that routes every coding task through a structured 8-agent pipeline before delivery. Built on Loop Engineering principles — each agent has a distinct role, a separate evaluator checks the worker's output, and every feedback loop has intelligent fail-safes that prevent infinite cycling.

---

## What it does

| Phase | Agent | Role |
|---|---|---|
| 1 | **Architect** | Defines the Success Contract and spec before any code is written |
| 2 | **Coder** | Writes complete, production-quality code — no stubs, no TODOs |
| 3 | **Reviewer** | 4 independent lenses: Security · Performance · Logic · Maintainability |
| 4 | **Tester** | Runs code in sandbox, holds a structured debate with Coder on every failure |
| 5 | **Optimizer** | Improves proven-correct code against real benchmarks |
| 6 | **Security Auditor** | STRIDE threat model · OWASP Top 10 · CVE scan · secrets scan |
| 7 | **Documenter** | README · API docs · Architecture · Runbook · Changelog |
| 8 | **Deployer** | Dockerfile · CI/CD · Makefile · IaC scaffold |

---

## Key features

**Loop Engineering architecture** — The judge is never the worker. Reviewer and Tester are structurally separate from Coder with different instructions and different mental models.

**Loop Sentinel** — Every feedback loop has stagnation detection (same issue in 2 consecutive loops = halt), a 25% progress threshold per loop, a convergence score, and a global step budget.

**Checkpoint system** — If a session hits a usage limit mid-pipeline, CRUCIBLE writes the checkpoint to memory. Start a new session, say `/crucible`, and it resumes exactly where it stopped.

**Intake flow** — Before starting, CRUCIBLE asks 5 questions (plan, task, stack, audience, sensitive data) and recommends the best mode for your token budget.

**Four pipeline modes:**
- `lean` — Coder + Reviewer + Tester (~15,000 tokens)
- `standard` — Architect + Coder + Reviewer + Tester (~35,000 tokens)
- `+secure` — standard + Optimizer + Security Auditor (~55,000 tokens)
- `full` — All 8 agents (~100,000+ tokens)

---

## Requirements

- **Claude Code** (any version)
- **Recommended plan:** Claude Max 5x or higher for `+secure` and `full` modes
- `standard` mode works on Pro
- `lean` mode works on any plan

---

## Installation

**1. Locate your Claude Code user skills folder:**

```
# Windows
C:\Users\<your-username>\.claude\skills\user\

# macOS / Linux
~/.claude/skills/user/
```

**2. Clone or download this repo:**

```bash
git clone https://github.com/<your-username>/crucible.git
```

**3. Copy the skill folder into your skills directory:**

```bash
# Windows (PowerShell)
Copy-Item -Recurse crucible\crucible "C:\Users\<your-username>\.claude\skills\user\crucible"

# macOS / Linux
cp -r crucible/crucible ~/.claude/skills/user/crucible
```

**4. Verify the final path looks like this:**

```
~/.claude/skills/user/crucible/SKILL.md
```

That's it. No restart needed. The skill activates on the next Claude Code session.

---

## Usage

```
/crucible build me a REST API for user authentication in Python + FastAPI
```

```
/crucible implement a rate limiter middleware for Express.js
```

```
/crucible refactor this service to use async/await properly
```

**With mode flag:**
```
/crucible full build me an e-commerce checkout service in TypeScript
/crucible lean write a CSV parser in Python
/crucible +secure create a JWT auth module in Go
```

**Resume an interrupted run:**
```
/crucible
```
CRUCIBLE checks for a previous checkpoint automatically and offers to resume.

---

## Output

A full CRUCIBLE run delivers:

- ✅ Source code (complete, reviewed, tested, optimized)
- ✅ Test suite (unit + integration + edge case, runnable standalone)
- ✅ README.md
- ✅ ARCHITECTURE.md
- ✅ API reference (if applicable)
- ✅ RUNBOOK.md
- ✅ Dockerfile + docker-compose.yml
- ✅ GitHub Actions CI/CD workflow
- ✅ Makefile
- ✅ .env.example
- ✅ CHANGELOG.md

---

## Why CRUCIBLE

Most coding tools have the same model write the code and decide if it's good. That model has blind spots baked into its own reasoning — it literally cannot see what it assumed wrong.

CRUCIBLE separates the worker from the judge structurally. The Reviewer and Tester have different instructions, different mental models, and no access to Coder's internal reasoning. They only see the output. This single architectural decision produces a fundamentally different quality of output.

---

## License

MIT — use it, modify it, share it.
