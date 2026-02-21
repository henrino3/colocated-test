# Entity Testing Guide

## Philosophy

We use **close the loop** — agents write code AND tests, verify before reporting done.

## Stack

- **Test runner:** Vitest
- **Coverage:** None required (yet)
- **Pattern:** Colocated tests next to source

## Commands

```bash
# Run all tests
pnpm test

# Run tests in watch mode
pnpm test:watch

# Run with coverage
pnpm test:coverage

# Run specific file
pnpm test utils.test.ts

# Force run (kill stale processes)
pnpm test:force
```

## What to Test

### ✅ DO Test

**Pure logic functions:**
- Data transformation/parsing
- Utility functions
- Validation functions  
- Classification logic
- Metrics calculation
- Configuration parsing

**API routes (unit):**
- Input validation
- Error handling
- Response formatting

### ❌ DON'T Test

- UI/visual components (Next.js app)
- E2E flows (use Playwright for those)
- External API calls (mock them)
- Database operations (mock or use test DB)

## Test Structure

```typescript
// utils.ts — source file
export function add(a: number, b: number): number {
  return a + b;
}

// utils.test.ts — colocated test
import { describe, it, expect } from 'vitest';
import { add } from './utils';

describe('add', () => {
  it('should add two numbers', () => {
    expect(add(1, 2)).toBe(3);
  });
  
  it('should handle negative numbers', () => {
    expect(add(-1, 1)).toBe(0);
  });
});
```

## Close the Loop Protocol

1. **Write code → write test**
2. **Run `pnpm test`** before reporting done
3. **If test fails → fix code → rerun tests**
4. **Only report "done" when tests pass**
5. **Never push code with failing tests**

## Anti-Patterns

- ❌ Tests that always pass (no real assertion)
- ❌ Test file without actual test cases
- ❌ Testing implementation details instead of behavior
- ❌ Skipping tests to "save time"

---

*Last updated: 2026-02-21*
