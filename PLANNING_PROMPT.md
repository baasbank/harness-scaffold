# Project Planning Prompt

Use this prompt at the start of a new project conversation with Claude.
The output of this conversation is a plan document you drop into the harness scaffold skill.

---

## How to Use

1. Start a new Claude conversation
2. Paste this prompt (or describe your project in your own words — Claude will ask the right questions)
3. Have the conversation — answer Claude's questions about design, stack, features
4. At the end, ask: **"Generate the plan document for the harness scaffold skill"**
5. Copy the output
6. In a Claude Code session in your project directory, paste the plan and say:
   **"Use the harness scaffold skill to generate all harness files from this plan"**

---

## Conversation Starter Prompt

```
I want to map out a new software project so we can generate a complete AI agent harness for it.

Ask me questions to gather everything needed. We need to end up with:
- What the project does (user-facing description)
- The tech stack (language, framework, database, infra)
- The major modules/layers
- The features as a numbered list (each a concrete user-facing behavior)
- Hard constraints (auth approach, coding standards, deploy target, anything non-negotiable)
- How to verify the code works (test commands, lint, type-check for this stack)
- Where it deploys

Ask me one topic at a time. When you have everything, generate a plan document in this exact format:

---
PROJECT_NAME: <name>
DESCRIPTION: <2-3 sentence description>
STACK: <each layer: language version, framework version, DB, infra>
DEPLOY_TARGET: <Hetzner/GCP/Vercel/etc. and how: Docker, bare metal, managed>
MODULES:
  - <module_name>: <one sentence on what it owns>
  - ...
HARD_CONSTRAINTS:
  - MUST <rule>
  - MUST NOT <rule>
  - ...
FEATURES:
  F01: <concrete user-facing behavior>
  F02: <concrete user-facing behavior>
  ...
VERIFICATION:
  test: <exact shell command>
  lint: <exact shell command>
  typecheck: <exact shell command or "none">
  build: <exact shell command>
  e2e: <exact shell command or "none yet">
---

Project: [describe your project here, or I'll ask]
```

---

## Tips for a Good Plan

**Features**: Write them as user outcomes, not technical tasks.
- ❌ "Create User model and migration"
- ✅ "A new user can register with email and password and receives a JWT token"

**Hard constraints**: Be specific. Vague constraints create ambiguous harnesses.
- ❌ "Use modern patterns"
- ✅ "MUST use SQLAlchemy 2.0 async sessions for all DB queries"
- ✅ "MUST NOT store secrets in code — use environment variables"

**Modules**: Match your actual directory structure. If you'll have `src/api/`, `src/db/`, `src/workers/` — those are your modules.

**Verification**: Real commands. If you're not sure yet, your best guess — the scaffold will mark them `# TODO: verify`.
