# Soteria — 4-Layer Close the Loop Implementation Plan

**Date:** 2026-02-21
**Projects:** soteria-cs-ops-agent, soteria-renewal-ops-mvp
**Stack:** Next.js 16, TypeScript, AI SDK, IMAP, PDF parsing

---

## Layer 1: Unit Tests (Week 1)

**Goal:** Test all pure logic in `lib/` — no API calls, no DB, no network.

### CS Ops Agent — Candidates
| File | Lines | What it does | Testability |
|------|-------|-------------|-------------|
| `plan-analysis.ts` | 291 | Insurance plan analysis logic | ⭐ HIGH — pure data transformation |
| `text.ts` | 105 | Text processing utilities | ⭐ HIGH — pure functions |
| `training-eval.ts` | 91 | Training evaluation scoring | ⭐ HIGH — pure scoring logic |
| `assist-suggest.ts` | 73 | Suggestion generation | MEDIUM — may call AI |
| `retrieval.ts` | 76 | Document retrieval | MEDIUM — may need mocking |
| `embeddings.ts` | 55 | Embedding generation | LOW — calls OpenAI API |
| `pdf.ts` | 34 | PDF parsing | MEDIUM — file I/O |

### Renewal Ops — Candidates
| File | Lines | What it does | Testability |
|------|-------|-------------|-------------|
| `compliance-scorer.ts` | 75 | Compliance scoring logic | ⭐ HIGH — pure scoring |
| `attachment-classifier.ts` | 74 | File type classification | ⭐ HIGH — pure logic |
| `entity-resolver.ts` | 181 | Entity resolution/matching | ⭐ HIGH — pure logic |
| `parser-interface.ts` | 62 | Parser abstraction | HIGH — interface layer |
| `gap-notifier.ts` | 66 | Gap detection | MEDIUM — may have side effects |
| `ai-parser.ts` | 214 | AI-powered parsing | LOW — calls AI APIs |

**Start with:** `plan-analysis.ts`, `text.ts`, `compliance-scorer.ts`, `attachment-classifier.ts`, `entity-resolver.ts`

**Commands:**
```bash
cd ~/Code/soteria-cs-ops-agent && npx vitest run
cd ~/Code/soteria-renewal-ops-mvp && npx vitest run
```

---

## Layer 2: E2E Tests (Week 2)

**Goal:** Test API routes end-to-end — request in, response out.

### What to test
- `POST /api/plans/upload` — file upload + parsing
- `GET /api/plans` — list plans
- `GET /api/plans/[id]` — get single plan
- `POST /api/assist/stream` — AI assist streaming
- `GET /api/dashboard/summary` — dashboard data
- `POST /api/training/start` — training session
- `GET /api/settings/status` — settings check

### How
- Use Vitest + `next/test-utils` or `supertest` for HTTP
- Mock external APIs (OpenAI, IMAP)
- Test: correct status codes, response shape, error handling, auth

---

## Layer 3: Live Tests (Week 3)

**Goal:** Verify real third-party integrations work.

### What to test
- OpenAI embeddings API — does it return vectors?
- IMAP email connection — can we connect to a test mailbox?
- PDF parsing — does it extract text from real PDFs?

### How
- Separate test file: `*.live.test.ts`
- Requires real API keys (skip in CI)
- Run manually: `LIVE=1 npx vitest run --grep live`

---

## Layer 4: Docker Tests (Week 4)

**Goal:** Verify full app starts from scratch.

### What to test
- `docker build` succeeds
- App starts and responds on port 3000
- Health check endpoint returns 200
- Can process a sample plan upload

### How
```bash
docker build -t soteria-test .
docker run -d -p 3000:3000 soteria-test
curl -f http://localhost:3000/api/settings/status
```

---

## Setup Checklist

- [ ] Add vitest to both repos (`npm install -D vitest`)
- [ ] Create AGENTS.md with test commands
- [ ] Create TESTING.md
- [ ] Write first 5 unit tests (Layer 1)
- [ ] Run experiment CTL-002 (introduce bugs, verify detection)
- [ ] Add E2E test setup (Layer 2)
- [ ] Add live test with skip mechanism (Layer 3)
- [ ] Add Dockerfile + docker test script (Layer 4)

---

*Created: 2026-02-21*
