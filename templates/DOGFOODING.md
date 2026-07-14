# Dogfooding Protocol

> Automated tests prove the code runs. This proves the feature actually works
> the way a real user would experience it. Both are required before a feature
> can move to `passing` in features.json.

## Required Tool: Playwright MCP

Web/UI features cannot be properly dogfooded without actually controlling a
browser. Install the Playwright MCP server once per machine:

```
claude mcp add playwright npx @playwright/mcp@latest
```

This gives the agent real browser tools — navigate, click, fill forms,
select dropdowns, screenshot, read console/network output — grounded in the
live DOM via accessibility snapshots, not guessed selectors. Without it, the
agent can only generate test code from assumptions about what the UI
probably looks like; scripts built that way commonly fail at runtime because
the selectors don't match the real page.

On first use in a session, be explicit: "use playwright mcp to test the
signup flow" — this ensures the right tools load before Claude starts
guessing at DOM structure from the prompt alone.

Playwright MCP launches its own isolated browser instance — it does not use
your personal logged-in Chrome profile or cookies, which keeps dogfooding
sessions separate from your real accounts elsewhere.

## Why This Exists

Tests can pass while a feature is still broken in practice: a notification that
fires but renders "undefined", a form that technically submits but has no error
state, a CSV that opens wrong in Excel, a reminder that's technically sent but
arrives with the wrong timezone. Automated tests check behavior against
assertions you wrote — they can't check whether the assertions were the right
ones to write. Manually driving the running app catches what tests can't.

## The Rule

Before marking ANY feature `passing`, the agent must drive the actual running
system end-to-end as a real user would — not just run `npm test`.

This means:
- **Web features**: use Playwright MCP (see below) to drive the actual running
  app — navigate, click, fill the real form, submit, and read back what
  rendered. Report exact observed results: what appeared on screen, what the
  network tab showed, any console errors. If Playwright MCP isn't installed,
  follow `docs/MISSING_TOOL.md` rather than guessing at selectors or
  skipping this step.
- **API features**: issue real `curl` (or equivalent) requests against the
  running server with realistic payloads — not just call the handler function
  directly in a test file. Inspect the actual HTTP response: status code,
  body, headers.
- **Notification/email features**: actually trigger delivery in the dev
  environment (e.g. a test push subscription, a real outbound email to a
  test inbox or a logging-only provider) and confirm the content is correct
  — not just that a "send" function was called with the right arguments.
- **Scheduled/background jobs**: actually advance time or wait for the
  polling interval, and confirm the job fired and did the right thing —
  not just unit-test the query in isolation.

## What "Use It Like a Real User" Means Concretely

For each feature, before marking it `passing`, the agent answers these
questions with evidence (command output, response body, screenshot
description, log excerpt) — not just "yes, it works":

1. What exact action did I take to trigger this feature?
2. What did the system actually return/show/send?
3. Does that match what a user would expect, not just what the code intended?
4. Did I check the unhappy path too (bad input, missing auth, edge case from
   the feature's behavior description)?

## Recording Evidence

Each feature in `features.json` has an `evidence` field. When marking a
feature `passing`, fill it with a short description of what was manually
verified and how — e.g.:

```json
"evidence": "Signed up via POST /api/v1/auth/signup with test email, received session cookie, confirmed GET /api/v1/me returns the new user. Tried duplicate email signup, got 409 as expected."
```

If `evidence` is empty or just restates the verification command, the feature
is not actually done — only mechanically passing.

## What This Is Not

- Not a replacement for automated tests — both are required
- Not exhaustive QA — one realistic pass through the happy path plus one
  obvious unhappy path is enough for v1 features
- Not required for internal refactors with no user-facing behavior change
