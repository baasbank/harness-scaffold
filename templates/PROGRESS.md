# Progress

> Updated at: [SESSION START]
> Last commit: [run `git rev-parse --short HEAD`]
> Completed session details → `docs/PROGRESS_ARCHIVE.md` (once this file has grown enough to need it)

## Current State

- Tests: [unknown until first run]
- Lint: [unknown]
- Active feature: none — initialization phase

## Completed

<!-- Nothing yet -->

## In Progress

- [ ] Session 1: Initialization phase (NO feature code until all items below are checked)

**Initialization Checklist**

- [ ] **Human step:** `.env` created from `.env.example` with real secret values filled in (the agent cannot do this — it has no read/write access to `.env`)
- [ ] Required tools installed (runtime, package manager, DB, etc. — see ARCHITECTURE.md)
- [ ] `make setup` runs without errors
- [ ] Dev server starts (`make dev`) and responds
- [ ] `make test` executes (0 passing tests is fine — the framework must run)
- [ ] `make lint` executes without crashing
- [ ] `make build` completes successfully
- [ ] `make check` passes end-to-end
- [ ] Initial commit made with this scaffold in place

> Do not begin F01 until every box above is checked.
> The agent cannot complete the first checklist item — confirm with the user that `.env` exists before attempting `make setup`.

## Blocked

<!-- None -->

## Known Issues

<!-- None yet -->

## Next Steps

1. Install required tools for this stack (see ARCHITECTURE.md → Key External Dependencies)
2. Run `make setup`
3. Work through the initialization checklist above top to bottom
4. Only after `make check` passes: update this file, mark init complete, begin F01

## Session Log

<!-- Append a one-line entry per session: date, what was done, commit hash -->

> **Archiving rule:** if this file exceeds 150 lines, move Completed entries and session
> log lines older than the last 3 sessions into `docs/PROGRESS_ARCHIVE.md`. That file is
> reference-only — the agent does not read it at clock-in, only when a specific past
> session's detail is needed.
