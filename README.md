# Harness Scaffold — Quick Reference

## What This Is

A reusable system for bootstrapping any new software project with a complete
AI agent harness. Based on 12 harness engineering principles from Anthropic and OpenAI.
This skill is completely based on https://walkinglabs.github.io/learn-harness-engineering/en/

## The Workflow (3 steps, once per project)

```
Step 1: PLAN          Step 2: SCAFFOLD          Step 3: BUILD
─────────────────     ─────────────────────     ─────────────────────────
Talk to Claude        Drop plan into            Claude Code sessions
to map the project    Claude Code skill         build features one by one
                      → harness files           following the harness
```

### Step 1: Plan (Claude.ai or app)

Use `PLANNING_PROMPT.md` as your conversation starter.
Talk through: description, stack, modules, features, constraints, verification.
End with: "Generate the plan document."

Output: a structured plan document (PROJECT_NAME, STACK, FEATURES, etc.)

### Step 2: Scaffold (Claude Code)

In your project directory:

```
claude
> I have a project plan. Use the harness scaffold skill to generate all harness files.

[paste your plan document]
```

Output files generated:
```
project/
├── CLAUDE.md                    # Agent entry point (50-150 lines)
├── PROGRESS.md                  # Session state tracker
├── DECISIONS.md                 # Architecture decision log
├── features.json                # Feature list (scheduler/verifier backbone)
├── Makefile                     # Standardized verification commands
├── docs/
│   ├── ARCHITECTURE.md          # System design, module boundaries
│   ├── <module>-rules.md        # Per-module constraints (one per module)
│   ├── testing-standards.md     # Test levels, passing criteria
│   └── session-exit-checklist.md # What must pass before ending a session
└── .gitignore                   # Harness-aware entries
```

### Step 3: Build (Claude Code sessions)

Every session follows the same protocol (it's in CLAUDE.md):

**Clock in:**
1. Read PROGRESS.md
2. Read DECISIONS.md
3. `make check` — confirm clean state
4. Read features.json — find next feature

**Work:**
- One feature at a time (WIP=1)
- Verify before marking done
- Record decisions in DECISIONS.md

**Clock out:**
- `make check` must pass
- Update PROGRESS.md and features.json
- Commit everything
- Clean debug artifacts

---

## The 12 Principles (Summary)

| # | Principle | Key Rule |
|---|-----------|----------|
| 1 | Harness over model | Fix harness first, swap model last |
| 2 | Five subsystems | Instructions + Tools + Env + State + Feedback |
| 3 | Repo is truth | If not in repo, doesn't exist for agent |
| 4 | Entry file = router | CLAUDE.md: 50-150 lines max |
| 5 | Split instructions | Each topic in its own docs/ file |
| 6 | State persistence | PROGRESS.md prevents blind restarts |
| 7 | Init is its own phase | Session 1: environment only, no features |
| 8 | WIP=1 | One feature active, finish before starting next |
| 9 | Feature list = primitive | behavior + verification + state, machine-readable |
| 10 | External verification | "Done" = command passes, not "looks fine" |
| 11 | E2E for cross-component | Unit tests miss interface bugs |
| 12 | Clean exit every session | Build + tests + progress update + commit |

---

## Files in This Skill Package

- `SKILL.md` — The Claude Code skill (place in `.claude/skills/` or reference as user skill)
- `PLANNING_PROMPT.md` — Conversation starter for Step 1
- `README.md` — This file

## Installing the Skill

### Option A: Claude Code user skill (recommended)
```bash
npx skills add https://github.com/baasbank/harness-scaffold -a claude-code
```
Claude Code will load it automatically on startup.

### Option B: Claude Code user skill (recommended)
```bash
mkdir -p ~/.claude/skills/harness-scaffold
cp SKILL.md ~/.claude/skills/harness-scaffold/SKILL.md
```
Claude Code will load it automatically on startup.

### Option C: Per-project skill
```bash
mkdir -p .claude/skills/harness-scaffold
cp SKILL.md .claude/skills/harness-scaffold/SKILL.md
```

### Option D: Just paste SKILL.md into the conversation
In a Claude Code session, paste the full contents of SKILL.md before your plan.
