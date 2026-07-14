```json
{
  "version": "1.0",
  "wip_limit": 1,
  "features": [
    {
      "id": "F01",
      "title": "<feature title>",
      "behavior": "<user-facing behavior — what the USER experiences, not what code does>",
      "verification": "<exact runnable shell command — curl, pytest, npm test, etc. Never 'manual check'>",
      "state": "not_started",
      "depends_on": [],
      "evidence": null,
      "notes": ""
    }
  ]
}
```

Rules:
- behavior: user outcome. Bad: "Create User model". Good: "User can register and receives JWT."
- verification: must be a shell command. If cross-component, it must invoke the e2e suite.
- state: always `not_started` at scaffold time. Only a passing command promotes to `passing`.
- depends_on: IDs of features that must be `passing` before this one activates.
- evidence: required before state can become `passing`. Must describe the actual manual check performed (see docs/DOGFOODING.md) — not a restatement of the verification command. Empty/null evidence means the feature is not actually done.
- Order: infrastructure features first, user-facing features after.
