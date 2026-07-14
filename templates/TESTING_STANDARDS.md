# Testing Standards

> Read this when writing or modifying tests.

## Test Levels Required
1. Unit tests — every function with business logic
2. Integration tests — every API endpoint / DB interaction
3. E2E tests — every user-facing flow that crosses component boundaries

## Verification Hierarchy
- Level 1 (unit): `<unit test command>`
- Level 2 (integration): `<integration test command>`
- Level 3 (e2e): `make e2e`
- All levels: `make check`

## Definition of a Passing Test
- Tests must exercise real behavior, not mock the thing under test
- A feature is NOT done until its tests pass at all required levels

## Agent Rules
- NEVER skip tests to "speed things up"
- NEVER declare a feature done if any test is red
- If a test is failing and you don't understand why: STOP, record it in PROGRESS.md as a blocker
