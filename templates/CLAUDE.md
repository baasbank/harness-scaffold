# CLAUDE.md — Agent Entry Point

## What This Project Is
<DESCRIPTION from plan — 2-3 sentences>

## Tech Stack
<STACK — list each layer with version if known>

## Quick Start
- Install: `<install command>`
- Dev server: `<dev command>`
- Run tests: `<test command>`
- Full verification: `make check`

## Hard Constraints
<!-- Max 15 rules. MUST / MUST NOT language. Non-negotiable. -->
- MUST define the app name as a single exported constant (e.g. `src/config/app.ts` → `APP_NAME`) and import it everywhere — never hardcode the project name as a string literal anywhere else
<Generated from HARD_CONSTRAINTS — each as a bullet>

## Work Rules (WIP=1)
- Work on ONE feature at a time
- Only move a feature to `passing` after its verification command executes successfully
- Do NOT start a new feature while one is `active`
- Do NOT refactor adjacent code until the current feature is `passing`

## Recording Decisions
- Record every significant decision AT THE MOMENT IT IS MADE. PROGRESS.md = state; DECISIONS.md = rationale. A decision only in code or a commit message is lost context.
- **When adding any decision: add an index line at the top of DECISIONS.md AND the full entry below — both at the same time.** The index is what clock-in reads; full entries are read on demand.
- Index line format: `- [date] Title — one-sentence summary`
- Full entry format: `## <date>: <title>` → **Decision**, **Reason**, **Rejected alternatives**, **Constraints upheld**, **For next sessions** (where useful).
- Significant = a future session could reasonably have chosen differently: schema shape, auth/security, library/provider, module/API contracts, infra workflow, any deviation from a topic doc.
- If superseded: add a new dated entry explaining the change; mark the old index line `[superseded by: date+title]` — do not silently edit history.

## Session Protocol
### Clock In (start of every session)
1. Read PROGRESS.md — understand current state
2. Read DECISIONS.md index (top section only) — scan titles, then read full entries only when relevant to the current feature
3. Run `make check` — confirm repo is in a consistent state
4. Read features.json — find the next `active` or first `not_started` feature
5. Begin work on that feature only

### Clock Out (end of every session)
1. Run `make check` — must pass before exiting
2. Update PROGRESS.md — completed, in-progress, blocked, next steps, append one session log line. If PROGRESS.md exceeds 150 lines: move Completed entries and session log lines older than the last 3 into `docs/PROGRESS_ARCHIVE.md`
3. Update features.json — set correct state (and `evidence`) for all touched features
4. **Verify DECISIONS.md records every significant decision made this session** (see "Recording Decisions" above) — this is a backstop; decisions should already be written down as they were made, not first captured here
5. Commit all completed work
6. Clean up debug code, temp files, TODO markers

## Definition of Done
A feature is DONE only when:
- [ ] Its verification command passes
- [ ] All pre-existing tests still pass
- [ ] `make check` passes
- [ ] The feature was manually exercised end-to-end as a real user/caller would (see `docs/DOGFOODING.md`) — not just verified by automated test
- [ ] No required tool was missing/skipped during verification (if one was, see `docs/MISSING_TOOL.md` — the feature is NOT done until resolved)
- [ ] features.json `evidence` field filled with what was manually checked and how
- [ ] features.json updated to `passing`

## Topic Docs (read when relevant)

- `docs/OPERATIONS.md` — how to start init, start a feature, add a feature, fix a bug, or make a cross-cutting change (redesign/refactor/upgrade)
- `docs/DOGFOODING.md` — how to manually verify a feature like a real user before marking it passing
- `docs/MISSING_TOOL.md` — what to do when a needed tool, permission, or MCP server isn't available — read this instead of improvising
- `docs/ARCHITECTURE.md` — module boundaries, dependency rules, data flow
- `docs/<module>-rules.md` — constraints and patterns for that module
- `docs/testing-standards.md` — test levels, passing criteria, agent test rules
- `docs/session-exit-checklist.md` — what must pass before ending any session

## Verification Commands
<Full list: test, lint, typecheck, build, e2e>
