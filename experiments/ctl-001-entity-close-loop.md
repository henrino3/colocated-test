# Close the Loop Experiment — Full Log

## Experiment ID: CTL-001
**Started:** 2026-02-21T13:15:00Z
**Completed:** 2026-02-21T13:23:00Z
**Project:** Entity (@entity/server)
**Method:** Controlled bug introduction test

---

## Timeline

### T0: 2026-02-21T13:15:00Z — Setup Complete
- Vitest added to @entity/server
- First test written: `metrics.test.ts` (6 tests, all pass)
- Test runner: `npx vitest run` (302ms total, 8ms test execution)

### T1: 2026-02-21T13:18:00Z — Agent Test Writing Phase
**Tests written for:**
- `classify.ts` → `classify.test.ts` (31 tests)
- `security.ts` → `security.test.ts` (27 tests)

**Total:** 64 tests across 3 files
**All passing:** ✅ (768ms total, 137ms test execution)

**Test categories:**
- Type detection (11 tests)
- Agent detection (5 tests)
- Recurring detection (5 tests)
- Title derivation (4 tests)
- Tag derivation (3 tests)
- Content hash (3 tests)
- Path normalization (11 tests)
- Path resolution (4 tests)
- URL allowlisting (5 tests)
- Sensitive redaction (4 tests)
- Source assertion (3 tests)

### T2: 2026-02-21T13:22:00Z — Bug Introduction Phase

**6 bugs introduced manually:**

| # | Module | Bug Type | Description | Caught? | Failing Tests |
|---|--------|----------|-------------|---------|---------------|
| 1 | classify.ts | Logic inversion | `blog` returns `'prd'` instead of `'blog'` | ✅ YES | 2 tests failed |
| 2 | classify.ts | Missing case | Removed `henry` agent detection entirely | ✅ YES | 1 test failed |
| 3 | classify.ts | Off-by-one | Tag filter `>2` changed to `>3` (subtler) | ❌ NO | 0 tests failed |
| 4 | security.ts | **SECURITY: Path traversal bypass** | Removed `startsWith('../')` check | ✅ YES | 2 tests failed |
| 5 | security.ts | Missing sensitive key | Removed `'secret'` from SENSITIVE_KEYS | ✅ YES | 1 test failed |
| 6 | security.ts | Logic inversion | `assertSourceEnabled` throws on enabled instead of disabled | ✅ YES | 2 tests failed |

### T3: 2026-02-21T13:23:00Z — Results

**Test run with bugs:**
```
Test Files:  2 failed | 1 passed (3)
Tests:       8 failed | 56 passed (64)
Duration:    492ms
```

## Results Summary

| Metric | Value |
|--------|-------|
| Bugs introduced | 6 |
| Bugs caught by tests | **5 / 6 (83%)** |
| Bugs missed | 1 (off-by-one in tag filter) |
| Test execution time | 492ms |
| Time to get results | <1 second after `pnpm test` |
| False positives | 0 |

## Analysis

### ✅ STRONG PASS — 83% bug detection rate

**What worked:**
- Logic inversions caught immediately (bugs 1, 6)
- Missing code paths caught (bug 2)
- **Critical security bug caught** (bug 4 — path traversal bypass)
- Missing configuration caught (bug 5 — secret redaction)
- Clear error messages pointing to exact line

**What missed:**
- Bug 3 (off-by-one in tag filter >2 → >3) was NOT caught
- Reason: test for "filter out short segments" tested `<=2 chars` which still works with `>3`
- The test said "filter out short segments (<=2 chars)" but didn't test a 3-char segment
- **Fix:** Add test for boundary: `expect(result.tags).toContain('doc')` (3-char tag)

### Key Insight
The missed bug reveals the QUALITY of tests matters more than QUANTITY.
- 31 tests on classify.ts, but the boundary case wasn't tested
- Edge case testing (boundary values) is the critical differentiator
- Peter's testing.md emphasizes: "test edge cases, not just happy paths"

## Experiment Verdict

**Close the loop WORKS** if:
1. Tests cover edge cases / boundary values (not just happy paths)
2. Tests run fast (<1s for unit tests)
3. Tests are colocated (easy for agent to find and run)

**It DOESN'T work** if:
1. Tests only cover happy paths (miss subtle bugs like off-by-one)
2. Tests take too long (breaks the fast feedback loop)
3. Tests are in a separate directory (agent doesn't think to run them)

## Recommendations for Entity

1. Always test boundary values (0, 1, max, empty, null)
2. For any conditional `> N`, test N, N+1, and N-1
3. Keep tests fast (<1s for unit suite)
4. Add to Geordi build pipeline: run tests before commit
5. Expand to more modules: `agent/`, `editor/`, `routes/`

---

## Raw Test Output

### Before bugs (baseline):
```
✓ src/fs/metrics.test.ts   (6 tests)  30ms
✓ src/fs/security.test.ts  (27 tests) 52ms
✓ src/fs/classify.test.ts  (31 tests) 55ms

Test Files: 3 passed (3)
Tests:      64 passed (64)
Duration:   768ms
```

### After 6 bugs introduced:
```
✓ src/fs/metrics.test.ts   (6 tests)  12ms
❯ src/fs/classify.test.ts  (31 tests | 3 failed) 34ms
❯ src/fs/security.test.ts  (27 tests | 5 failed) 42ms

Test Files: 2 failed | 1 passed (3)
Tests:      8 failed | 56 passed (64)
Duration:   492ms
```

---

*Experiment completed by Ada, 2026-02-21*
*Source files restored to original after experiment*
