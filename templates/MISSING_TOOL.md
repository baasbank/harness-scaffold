# Missing Tool Protocol

> What to do when a feature needs a tool, command, or MCP server that isn't
> available or isn't permitted. Read this whenever a task stalls because
> something required is missing — don't guess, don't skip, don't silently
> work around it.

## Why This Exists

There's no built-in fallback when a needed tool is unavailable — Claude Code
doesn't automatically substitute, degrade gracefully, or notify in a
structured way. Left undefined, an agent under pressure to finish a feature
will often improvise: guess at DOM selectors instead of using Playwright,
skip a verification step quietly, or invent a workaround that produces
unreliable results. This protocol exists so that doesn't happen by default.

## Recognize the Failure Modes

A missing tool shows up as one of these:

1. **Permission denied** — a command exists but isn't in `.claude/settings.json`'s
   `allow` list, so it sits in `ask` or `deny`.
2. **Tool not connected** — an MCP server (e.g. Playwright) was never
   installed or configured for this machine.
3. **Binary/dependency not installed** — a CLI tool the feature needs
   (a specific linter, a CLI for a third-party service, etc.) isn't present
   in the environment at all.
4. **Capability gap** — the task genuinely requires something outside any
   tool the agent has access to (e.g. visually comparing a screenshot to a
   design mockup pixel-by-pixel).

## The Rule

**Stop. Do not improvise a workaround. Do not skip the step silently.**

1. Record exactly what's missing and why it's needed in `PROGRESS.md` under
   Blocked, with the specific command or tool name.
2. State what you were trying to verify and what you'll do once the tool is
   available.
3. If it's category 1 (permission) or 2 (not connected) — these are usually
   a one-line fix. Tell the user the exact command to run:
   - Permission: "Add `Bash(<command>:*)` to the `allow` list in
     `.claude/settings.json`, or approve it once and choose 'Always allow.'"
   - MCP not connected: give the exact `claude mcp add ...` command (see
     `docs/DOGFOODING.md` for the Playwright example).
4. If it's category 3 (not installed) — tell the user the install command
   for the missing dependency. Do not attempt to install system-level
   packages yourself without asking, even if `Bash(*)` would technically
   allow it — new dependencies are a decision worth a human seeing.
5. If it's category 4 (genuine capability gap) — say so plainly and suggest
   what a human should check manually instead.
6. Commit whatever work is safely complete, with the blocker clearly noted.
   Do not mark the feature `passing` with a fabricated or skipped
   verification step.

## What NOT to Do

- Do not mark a feature `passing` because the automated test passed if the
  required dogfooding step was skipped due to a missing tool — that's not
  done, it's untested.
- Do not silently fall back to a weaker verification method (e.g. reading
  source code instead of actually running the app) without saying so
  explicitly in `evidence`.
- Do not attempt to bypass a permission boundary by writing a script that
  does indirectly what a direct command isn't allowed to do.
- Do not install new system packages, global tools, or dependencies without
  flagging it to the user first, even if technically permitted.

## Example

```
Blocked: F06 (web push subscription) needs Playwright MCP to dogfood the
permission prompt flow in a real browser, but it isn't connected on this
machine.

To unblock: run `claude mcp add playwright npx @playwright/mcp@latest`,
then restart the session.

Work completed so far: subscription endpoint implemented and unit-tested
(`make test` passing). NOT yet manually verified end-to-end — do not
consider F06 done until the dogfooding step above is completed.
```

## Self-Resolvable Exception: Stale Environment vs. Genuinely Missing Dependency

Not every "missing tool" failure is category 3 (a dependency that needs a
human decision to add). Sometimes the dependency is already committed —
already in the Dockerfile, already in `package.json`/lockfile, already in
setup scripts — and the failure just means the *running* environment
(container, venv, node_modules volume) predates that commit. This is
self-serve, not an escalation:

1. Check whether the missing thing is already declared in a committed setup
   file (Dockerfile, lockfile, `make setup` steps) that the running
   environment simply hasn't picked up yet (an image built before a layer was
   added, a dependency installed after the container/venv was created, etc.).
2. If so, rebuild/recreate from that already-committed config — e.g.
   `docker compose build <service> && docker compose up -d <service>`,
   reinstalling into an existing venv, re-running the setup command — and
   confirm the tool is now present before continuing.
3. This is NOT "installing a new dependency without asking" (still forbidden)
   — the install step is already in the repo; rebuilding just applies it.
   Only escalate (per the categories above) if the rebuild itself fails (e.g.
   the download is blocked) — that's a genuine environment blocker, not a
   stale one.

## After You've Resolved the Blocker

**Same session, agent still active:** just confirm it's resolved and say
what to do next — the agent still has full context of what it was doing.

```
Installed. Continue with F06.
```

**New session, or context was reset/compacted:** the agent has no memory of
the blocker unless it's in PROGRESS.md. Point it there explicitly rather
than just saying "it's installed" — a fresh session can't tell which
feature was waiting or what was already done versus still pending.

```
Read PROGRESS.md — there was a blocker on F06 that's now resolved
(Playwright MCP installed). Continue from there.
```

When in doubt about whether context carried over, default to the second
form — it costs one extra read and removes any ambiguity. The agent should
also update PROGRESS.md to move the entry from Blocked back into the active
feature's normal flow once it confirms the blocker is actually gone (e.g.
by successfully calling the tool that was missing), not just because the
user said so — verify, then proceed.
