---
name: harness-scaffold
description: >
  Generate a complete AI agent harness scaffold for a new project.
  Use when given a project plan document and asked to scaffold all harness files
  for Claude Code to build the project reliably. Produces CLAUDE.md, PROGRESS.md,
  DECISIONS.md, features.json, Makefile, per-module docs, and session checklist.
---

# Harness Scaffold Skill

Generate a complete, ready-to-use AI agent harness from a project plan.
All generated files encode the 12 harness engineering principles below.

---

## The 12 Principles (apply to every file you generate)

1. **Harness over model** — Fix the harness first. Swap the model last.
2. **Five subsystems** — Instructions + Tools + Environment + State + Feedback. All five required.
3. **Repo is truth** — If not in the repo, it doesn't exist for the agent.
4. **Entry file = router** — CLAUDE.md: 50–150 lines max. Overview, hard constraints, verification commands, links only.
5. **Split by topic** — Each concern lives in its own `docs/` file, read on demand.
6. **State persistence** — PROGRESS.md and DECISIONS.md prevent blind session restarts. Rebuild cost target: < 3 min.
7. **Init is its own phase** — Session 1: environment only. No feature code until `make check` runs clean.
8. **WIP=1** — One feature active at a time. Verify before starting the next.
9. **Feature list = primitive** — Every feature has: (behavior, verification command, state). Machine-readable.
10. **External verification** — "Done" = command passes. Not "code looks fine."
11. **E2E for cross-component** — Unit tests miss interface bugs. E2E is mandatory for cross-module changes.
12. **Clean exit every session** — Build + tests + progress update + commit. Non-negotiable.

---

## Input: Required Plan Format

```
PROJECT_NAME: <name>
DESCRIPTION: <2-3 sentences>
STACK: <language, framework, DB, infra — with versions>
DEPLOY_TARGET: <where and how it runs>
MODULES:
  - <module_name>: <one sentence on what it owns>
HARD_CONSTRAINTS:
  - MUST <rule>
  - MUST NOT <rule>
FEATURES:
  F01: <user-facing behavior>
  F02: <user-facing behavior>
VERIFICATION:
  test: <command>
  lint: <command>
  typecheck: <command or "none">
  build: <command>
  e2e: <command or "none yet">
```

If any field is missing, ask before proceeding.

---

## Generation Steps

1. **Parse the plan** — extract all fields, ask for any missing ones.
2. **Show features.json first** — this is the backbone. Get user confirmation before writing other files.
3. **Write all files** — use the templates in `templates/` as your starting structure.
4. **Fill real commands** — never leave `<placeholder>`. Derive from the stack. Mark unknowns with `# TODO: verify`.
5. **Check internal consistency** — feature verification commands must match what `make test` / `make e2e` actually runs.
6. **Report** — list every file created with a one-line description.

---

## Files to Generate

Read the corresponding template from `templates/` before writing each file.

| File | Template | Notes |
|------|----------|-------|
| `CLAUDE.md` | `templates/CLAUDE.md` | Entry point. 50–150 lines max. Hard constraints at the TOP. |
| `PROGRESS.md` | `templates/PROGRESS.md` | State tracker. Pre-filled for session 1 init phase. |
| `DECISIONS.md` | `templates/DECISIONS.md` | Fill stack + module boundary decisions from plan. Keep the index (one-liner per decision) in sync with the full entries below it — every future decision needs both, added together. |
| `docs/PROGRESS_ARCHIVE.md` | `templates/PROGRESS_ARCHIVE.md` | No changes needed — universal. Created empty; PROGRESS.md's own instructions say when to move entries here. |
| `features.json` | `templates/features_json.md` | One entry per FEATURE. States all `not_started`. |
| `Makefile` | `templates/Makefile.md` | Fill real commands from VERIFICATION section. |
| `docs/ARCHITECTURE.md` | `templates/ARCHITECTURE.md` | Fill from MODULES + DESCRIPTION. |
| `docs/<module>-rules.md` | `templates/MODULE_RULES.md` | One file per MODULE. Fill from HARD_CONSTRAINTS. |
| `docs/testing-standards.md` | `templates/TESTING_STANDARDS.md` | Fill test commands from VERIFICATION. |
| `docs/session-exit-checklist.md` | `templates/SESSION_EXIT_CHECKLIST.md` | No changes needed — universal. |
| `docs/OPERATIONS.md` | `templates/OPERATIONS.md` | No changes needed — universal. |
| `docs/DOGFOODING.md` | `templates/DOGFOODING.md` | No changes needed — universal. |
| `docs/MISSING_TOOL.md` | `templates/MISSING_TOOL.md` | No changes needed — universal. |
| `.claude/settings.json` | `templates/claude_settings_json.md` | Adapt allow-list to the actual package manager / stack from VERIFICATION. |
| `.env.example` | `templates/env_example.md` | Adapt keys to actual stack/services. NEVER generate `.env` itself. |

---

## Critical Rules for features.json

- `behavior`: user outcome. Bad: "Create User model". Good: "User can register and receives a JWT."
- `verification`: runnable shell command only. Never "manual check". Cross-component features must invoke `make e2e`.
- `state`: always `not_started` at scaffold time. Only a passing command promotes to `passing`.
- `depends_on`: list feature IDs that must be `passing` first.
- Order features: infrastructure → foundational → user-facing.

---

## Critical Rules for CLAUDE.md

- Hard constraints go at the TOP (above the fold) — never in the middle (Lost in the Middle effect).
- 50–150 lines maximum. If it wants to grow longer, move content to a `docs/` file and add a link.
- Every topic doc listed must explain WHEN to read it (not just what it is).

---

## After Scaffolding

Tell the user:

> "Your harness is ready. **Session 1 must be initialization only** — no feature code.
> The agent must work through PROGRESS.md's initialization checklist in order:
> install required tools, run `make setup`, confirm `make dev` starts, confirm
> `make test` executes (0 passing is fine — the framework must run), and confirm
> `make check` passes end-to-end. Only after every checklist item is ticked
> should the agent commit and move to F01. Each subsequent session reads
> PROGRESS.md and features.json to know exactly where to start."
