# Close the Loop — Research & Implementation

## What is "Close the Loop"?

Peter Steinberger's methodology for autonomous agent coding:
- Give the agent terminal access
- Agent writes code → runs tests → fixes failures → iterates
- Only reports back when tests pass
- No human in the loop for verification

**Origin:** From a podcast where Peter was asked "how do you develop faster than anyone?" → "because I close the loop"

## Source Files

Peter's OpenClaw reference files (retrieved from GitHub):
- `AGENTS.md` (21KB) — Build, test, dev commands
- `testing.md` (19KB) — 4-layer testing methodology
- `copilot.instructions.md` (2KB) — Anti-redundancy rules
- `test.md` (2KB) — Quick test commands

## The 4-Layer Testing Pyramid

1. **Unit tests** — Mock tests, logic validation (fast, seconds)
2. **E2E tests** — Gateway smoke, webhooks (medium, minutes)
3. **Live tests** — Real API calls, third-party integrations (slow)
4. **Docker tests** — Containerized onboarding (slowest)

Start at bottom, move up only when needed.

## Evidence It Works

### OpenClaw GitHub Stats
- 1,376 test files in codebase
- 563 source files have colocated tests (~25% coverage)
- Real tests: 45-65KB files, not stubs
- Recent commits consistently pair source + test together

### Peter's Results
- 3-8 Codex agents in parallel
- Merged **600 commits in a single day**
- Doesn't read most code he ships — tests verify it

## Kinan's Implementation Prompt

For his Next.js + Vitest stack:

> **Stack:** Next.js frontend + Postgres, Vitest, zero tests
> 
> **Rules:**
> 1. Colocated tests — every source file gets `.test.ts` next to it
> 2. Close the loop — run tests after writing code. If fail, fix. Don't ask human.
> 3. Full gate before PR — build + lint + test
> 4. Anti-redundancy — search for existing helpers first

## Key Insight: Speed Matters

Kinan's discovery: "it took me a week to understand"
- He initially tried browser/UI testing (15min per run) → too slow
- Peter uses CLI unit tests (seconds) → fast feedback loop
- If tests take >1min per run, the loop is too slow to be useful

---

*Last updated: 2026-02-21*
