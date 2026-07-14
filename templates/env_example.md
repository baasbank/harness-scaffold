```
# .env.example
# Copy this file to .env and fill in real values. .env is gitignored — never commit it.
# The agent does not have read or write access to .env (see .claude/settings.json).
# You must create and populate .env yourself before initialization can complete.

# Database
POSTGRES_USER=app
POSTGRES_PASSWORD=
POSTGRES_DB=<project_name>
DATABASE_URL=postgresql://app:${POSTGRES_PASSWORD}@db:5432/<project_name>

# Session / auth
SESSION_SECRET=
# Generate with: openssl rand -base64 32

# Web push (VAPID keys)
VAPID_PUBLIC_KEY=
VAPID_PRIVATE_KEY=
VAPID_SUBJECT=mailto:you@example.com
# Generate with: npx web-push generate-vapid-keys

# Email provider (fallback delivery)
EMAIL_API_KEY=
EMAIL_FROM=

# App
NODE_ENV=development
PORT=3000
```

Adapt the keys to the actual stack and services used in the plan — this is a
starting template, not a fixed list. Every secret referenced anywhere in
docker-compose.yml, the Makefile, or application config must have a
corresponding entry here.

## Generation Rule

Always generate `.env.example` with empty values for secrets and clear
generation instructions as comments (e.g. `# Generate with: openssl rand
-base64 32`). NEVER generate `.env` itself, and NEVER fill in real secret
values anywhere, even placeholder-looking ones that could be mistaken for
real — the agent does not have permission to read or write `.env` and should
not work around that by inventing values in `.env.example`.
