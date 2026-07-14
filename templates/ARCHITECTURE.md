# Architecture

## Overview
<DESCRIPTION — 2-3 sentences>

## Module Responsibilities
<For each MODULE: one paragraph — what it owns, what it does NOT own, its public interface>

## Data Flow
<How data moves between modules>

## Dependency Rules
<Which modules may import from which. Use MUST NOT for forbidden dependencies.>
Example: "The API layer MUST NOT import directly from the DB layer — always via the service layer."

## Key External Dependencies
<Significant third-party libraries and why each was chosen>
