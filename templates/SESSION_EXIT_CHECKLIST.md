# Session Exit Checklist

ALL items must pass before ending a session.

## Required Checks
- [ ] `make check` passes (tests + lint + typecheck + build)
- [ ] features.json is up to date (correct states for all touched features)
- [ ] PROGRESS.md updated (completed, in-progress, blocked, next steps)
- [ ] DECISIONS.md records EVERY significant decision made this session — with reason and
      rejected alternatives, not just the outcome (see CLAUDE.md → "Recording Decisions").
      Decisions should already be written down as they were made; this is the backstop check.
- [ ] No debug code remaining (console.log, debugger, hardcoded secrets)
- [ ] No temp files remaining (debug-*, *.tmp, commented-out blocks > 5 lines)
- [ ] All completed work committed to git

## If Any Check Fails
Fix it before ending. If you cannot fix a failing test: record it in PROGRESS.md as a blocker with the full error output, then commit.

## Commit Message Format
```
<type>(<scope>): <what was done>

Features: F01 passing, F02 in progress
Tests: 42/43 passing (test_X failing — see PROGRESS.md)
```
Types: feat | fix | refactor | test | docs | chore
