# Operations Guide

> How to interact with the agent across different scenarios.
> Read this when starting a session or switching modes.

---

## Before You Start: Set Up Secrets

The agent cannot create or read `.env` — it's explicitly blocked in
`.claude/settings.json` so secrets never enter agent context. Before starting
initialization:

```
cp .env.example .env
```

Then fill in real values yourself: generate `SESSION_SECRET` with
`openssl rand -base64 32`, generate VAPID keys with
`npx web-push generate-vapid-keys`, and fill in any other provider keys
(email, etc.) from their respective dashboards. Once `.env` exists with
real values, proceed to initialization below.

## Before You Start: Install Browser Automation

If this project has a web frontend, install Playwright MCP once per machine
so the agent can actually click through the running app during dogfooding
(see `docs/DOGFOODING.md`) instead of just running automated tests:

```
claude mcp add playwright npx @playwright/mcp@latest
```

This is a one-time setup per machine, not per project.

## Starting the Initialization Phase

Open Claude Code in the project root and say:

```
Read PROGRESS.md and CLAUDE.md, then begin the initialization phase.
Work through the initialization checklist in PROGRESS.md top to bottom.
Do not write any feature code.
```

The agent will install tools, run `make setup`, and work through each checklist
item in order. It will update PROGRESS.md as items complete and make the initial
commit once `make check` passes.

**You are done when:** the agent reports every checklist item ticked and
`make check` passes.

---

## Starting the Next Feature

At the start of any session after init, say:

```
Read PROGRESS.md and features.json, then continue with the next feature.
```

The agent will find the first feature with state `not_started` (respecting
`depends_on`), set it to `active` in features.json, and begin work.

**You are done when:** the agent updates the feature to `passing` in features.json,
runs `make check`, updates PROGRESS.md, and commits.

To be explicit about which feature:

```
Read PROGRESS.md and features.json, then work on F03.
```

---

## Adding a Feature Not in the Plan

First, add it to `features.json` yourself, or tell the agent:

```
Add a new feature to features.json before continuing:
- Title: <title>
- Behavior: <what the user experiences>
- Verification: <shell command that proves it works>
- Depends on: <F01, F02, or empty>

Then read PROGRESS.md and begin this feature.
```

The agent will insert it with a new ID (e.g. F07), set state to `not_started`,
then activate and build it.

**Rule:** never describe a feature as a code task. Always describe it as a user
outcome. The agent figures out the implementation.

---

## Fixing a Bug

```
There is a bug: <describe what's wrong — what you did, what you expected, what happened>.
Do not work on any features. Fix the bug, then run `make check` to confirm nothing broke.
Update PROGRESS.md with what you found and fixed.
```

Be specific. The more precisely you describe the symptom, the less the agent
has to guess:

- ❌ "Login is broken"
- ✅ "Logging in with a valid email and password returns a 401. I expect a 200 and a JWT token."

If you have an error message or stack trace, paste it directly:

```
There is a bug. Here is the error:
<paste error>

Fix it, run `make check`, update PROGRESS.md.
```

**The agent must not start new feature work while fixing a bug.** If it
discovers the bug requires a design decision, it should stop, record the
decision in DECISIONS.md, and ask you before proceeding.

---

## Making a Redesign or Other Cross-Cutting Change

A redesign, a refactor, a dependency upgrade, or a performance pass that
touches multiple already-`passing` features is NOT a new feature — it changes
something already working, which means it can silently break behavior.
This applies whether or not the project has a frontend: a backend-only project
can just as easily need a cross-cutting refactor (e.g. swapping an ORM, changing
an error-handling convention across every route). Use this workflow instead of
the normal feature flow:

```
This is a [redesign / refactor / upgrade] — NOT a new feature. Do not add
anything to features.json.

<If this project has a UI design-system doc (e.g. docs/DESIGN.md), name it
here and tell the agent to read it in full before touching any code.>

Before touching any code:
1. Run `make check` — confirm the baseline is green (record the commit hash)
2. Ask me to confirm scope before proceeding

Work through it incrementally (one module/screen/component at a time), and
after each unit re-verify every feature that touches it — both the automated
suite and the dogfooding pass — updating `evidence` in features.json.
```

**Rules that apply to any cross-cutting change:**

- Automated tests should not need to change — they assert behavior, not
  implementation details (DOM structure, class names, internal function
  shapes). If a test breaks, ask whether the change broke real behavior (a
  genuine bug) or the test was asserting on an implementation detail (a bad
  test — flag it before "fixing" it).
- WIP=1 still applies: one unit of the change active at a time: finish it,
  re-verify the features it touches, then move to the next.
- No `features.json` entry is created for the change itself, but every
  feature it touches must be re-dogfooded and have its `evidence` updated —
  "the refactor was clean" is not evidence; re-running the actual
  verification is.
- If a genuine design or architecture gap appears mid-change (something no
  existing doc covers), STOP and record the decision in DECISIONS.md
  *before* writing code that depends on it.
- When finished: `make check` must pass, every previously-passing feature
  touched by the change must still pass, and DECISIONS.md must reflect any
  decisions resolved along the way.

---

## Resuming After a Blocker Is Resolved

If a previous session stopped because a tool, permission, or dependency was
missing (see `docs/MISSING_TOOL.md`) and you've now fixed it:

**Still in the same session** — just confirm and continue:
```
Installed. Continue with F06.
```

**New session, or unsure if context carried over** — point back at
PROGRESS.md explicitly rather than assuming the agent remembers:
```
Read PROGRESS.md — there was a blocker on F06 that's now resolved
(Playwright MCP installed). Continue from there.
```

The agent should verify the blocker is actually gone (successfully use the
tool) before resuming, not just take your word for it.

---

## General Rules for Every Session

- Always start by saying "Read PROGRESS.md" — this is the agent's orientation
- One task per session: init, one feature, or one bug fix — not a mix
- If the agent goes quiet or seems lost, ask: `What does PROGRESS.md say is the current state?`
- If something goes wrong mid-session: `Stop. Update PROGRESS.md with what happened, run make check, commit what's working, and tell me what's blocked.`

---

## Why You're Not Being Asked to Approve Every Command

`.claude/settings.json` in this project pre-approves the harness's normal
verification loop (`make *`, `npm *`, `docker compose *`, git up to commit)
so the agent can run `make check` repeatedly without interrupting you.
`git push` and destructive operations (force push, hard reset, volume
removal) still require your approval — that boundary is intentional.

If the agent ever asks permission for something unexpected, that's a signal
to look closely before approving — it means the command falls outside the
pre-approved verification loop.
