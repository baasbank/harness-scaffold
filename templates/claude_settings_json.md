```json
{
  "permissions": {
    "allow": [
      "Bash(make:*)",
      "Bash(npm:*)",
      "Bash(npx:*)",
      "Bash(docker compose:*)",
      "Bash(docker:*)",
      "Bash(git status*)",
      "Bash(git diff*)",
      "Bash(git log*)",
      "Bash(git add*)",
      "Bash(git commit*)",
      "Bash(git branch*)",
      "Bash(git checkout*)",
      "Bash(curl:*)",
      "Bash(cat:*)",
      "Bash(ls:*)",
      "Read(**)",
      "Write(**)",
      "Edit(**)",
      "Glob(**)",
      "Grep(**)",
      "Write(.env.example)",
      "Edit(.env.example)",
      "Read(.env.example)",
      "mcp__playwright"
    ],
    "deny": [
      "Bash(rm -rf /*)",
      "Bash(rm -rf ~*)",
      "Bash(git push --force*)",
      "Bash(git reset --hard*)",
      "Read(.env)",
      "Read(.env.local)",
      "Read(.env.development)",
      "Read(.env.production)",
      "Read(.env.staging)",
      "Read(.env.test)",
      "Write(.env)",
      "Write(.env.local)",
      "Write(.env.development)",
      "Write(.env.production)",
      "Write(.env.staging)",
      "Write(.env.test)",
      "Edit(.env)",
      "Edit(.env.local)",
      "Edit(.env.development)",
      "Edit(.env.production)",
      "Edit(.env.staging)",
      "Edit(.env.test)",
      "Read(**/*secret*)",
      "Read(**/*.pem)",
      "Read(**/*.key)"
    ],
    "ask": [
      "Bash(git push*)",
      "Bash(docker compose down*)",
      "Bash(docker volume rm*)",
      "Bash(docker compose *down*)"
    ]
  }
}
```

Place this at `.claude/settings.json` in the project root (commit it — it's the
team-shared baseline). Adjust the `allow` list to match the actual package
manager and stack (e.g. swap `npm` for `cargo`/`poetry`/`go` as needed).

## Why these specific boundaries

- **Build/test/lint tooling is fully allowed** — `make *`, `npm *`, `docker compose *`.
  These are exactly what the harness's verification loop runs constantly; prompting
  for each one defeats the point of an automated `make check`.
- **Git is allowed up to `commit`** — status, diff, log, add, commit, branch,
  checkout. This lets the agent follow the session-exit protocol (commit
  completed work) without interruption.
- **`git push` stays in `ask`** — pushing to a remote is the boundary between
  "local iteration" and "affecting shared state." Keep a human in the loop here.
- **`.env` and its real-secret variants are explicitly denied** for read,
  write, and edit — listed by exact filename (`.env`, `.env.local`,
  `.env.production`, etc.), not by wildcard. This is deliberate:
  `.env.*` as a wildcard would also match `.env.example`, which the agent
  needs to write (it's the documented, secret-free template). Listing exact
  filenames avoids that collision. `.env.example` is explicitly allowed for
  read/write/edit so this is unambiguous rather than relying on it simply
  not matching a pattern.
- **`rm -rf /`, `rm -rf ~`, `git push --force`, `git reset --hard` are denied**
  outright — these are the genuinely irreversible operations a misfire could
  trigger. Note: deny rules are filesystem-tool-level, not a sandbox — see
  the warning below.

## Important Caveat

Deny rules apply to Claude Code's own file/bash tools, not to arbitrary code
those tools might execute. If a script the agent writes calls `subprocess` or
opens a file handle directly, a deny rule on that path doesn't stop it. For
real isolation, also run the agent inside the project's own Docker container
or a disposable environment where destructive commands can't reach anything
outside the project — not your home directory or other projects.

## Quick Start (instead of hand-writing this file)

You can also build the allowlist interactively: run Claude Code normally for
a session, and each time it asks permission for a safe repeated command
(`npm test`, `make check`, etc.), choose "Always allow." Claude Code appends
a rule to `.claude/settings.json` automatically, so the file fills in based
on what you actually use — useful for stacks not covered by the template
above.
