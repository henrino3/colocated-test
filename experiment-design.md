# Close the Loop — Experiment Design Template

Use this template to verify close-the-loop works for YOUR project before committing to the methodology.

## Why Experiment?

Don't cargo-cult. Verify it works for your specific stack, team, and codebase.

## The 3-Phase Experiment

### Phase 1: Baseline

1. Pick 2 modules with pure logic (no I/O, no DB, no network)
2. Write colocated tests (aim for 20-30 tests per module)
3. Run tests — should all pass
4. Record: number of tests, execution time, coverage

### Phase 2: Controlled Bug Introduction

1. Back up original source files
2. Introduce 5-6 deliberate bugs:
   - 1x logic inversion (return wrong value)
   - 1x missing case (remove a branch)
   - 1x off-by-one (change boundary condition)
   - 1x security bug (weaken validation)
   - 1x config bug (remove an entry)
   - 1x type/edge case (empty input handling)
3. Run tests
4. Count: bugs caught vs bugs missed
5. Restore original files

### Phase 3: Analysis

**Score:**
- ≥83% caught = **STRONG PASS** → adopt close-the-loop
- ≥67% caught = **PASS** → adopt, but improve test quality
- <50% caught = **FAIL** → tests need better edge case coverage before this works

**For each missed bug, ask:**
- Was there a test for this case? If not, add one.
- Was the test testing the right thing? (behavior vs implementation)
- Was the boundary value tested?

## Success Criteria

Close-the-loop is working when:
- [ ] Tests catch ≥80% of introduced bugs
- [ ] Agent fixes without human intervention ≥50% of time
- [ ] Tests run in <5 seconds (fast feedback loop)
- [ ] Zero false positives (tests don't cry wolf)

## Tracking Template

| # | Module | Bug Type | Description | Caught? | Failing Tests |
|---|--------|----------|-------------|---------|---------------|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
| 6 | | | | | |

**Total caught: __ / 6 (___%)**
