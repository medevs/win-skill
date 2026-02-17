---
name: win
description: >
  Comprehensive thoroughness framework. Auto-invoked when planning features,
  implementing code, fixing bugs, writing tests, analyzing existing code,
  auditing features, reviewing architecture, or investigating issues.
  Forces systematic consideration of all edge cases, failure modes, error
  scenarios, security implications, and state transitions so nothing gets missed.
  Do NOT invoke for trivial changes like typos, renames, single-line fixes,
  adding imports, or updating config values.
---

# WIN: Complete Coverage Framework

**Core Rule: Before writing any code, enumerate what could go wrong. Before calling anything done, verify nothing was missed. Before saying "looks good", prove every path is covered.**

---

## PHASE 1: PLANNING — Think Like an Attacker

When planning any feature, fix, or change, work through every section below. If a section doesn't apply, explicitly state why.

### 1.1 Input Space Analysis

For every input the feature accepts (user input, API params, DB data, URL params, file uploads, environment variables):

- **Valid inputs**: What are all the valid forms? (types, ranges, formats)
- **Boundary values**: Empty string, 0, -1, MAX_INT, max length, min length, exactly-at-limit
- **Invalid inputs**: Wrong type, null, undefined, NaN, Infinity, empty array, nested nulls
- **Malicious inputs**: SQL injection, XSS payloads, path traversal, oversized payloads, unicode edge cases (ZWJ, RTL, emoji)
- **Concurrent inputs**: Same user submitting twice, race between two users, stale data

### 1.2 State Space Analysis

Map every state the system can be in during and after this change:

- **Loading states**: Initial load, refresh, pagination, background sync
- **Empty states**: No data yet, data deleted, filtered to nothing
- **Error states**: Network failure, auth expired, rate limited, server 500, partial failure
- **Success states**: Single result, many results, exactly-at-limit results
- **Transition states**: Optimistic update that rolls back, concurrent modification, mid-operation failure
- **Stale states**: Cached data that's outdated, browser tab left open, websocket disconnected

### 1.3 Failure Mode Analysis

For every external dependency (API call, DB query, file read, third-party service):

- What if it's **slow** (5s, 30s, timeout)?
- What if it **fails** (network error, 500, malformed response)?
- What if it returns **unexpected data** (empty, wrong shape, extra fields, null where not expected)?
- What if it **partially succeeds** (3 of 5 items saved, then crash)?
- What if the **user retries** while the first attempt is still in-flight?
- What is the **recovery path**? Can the user retry? Is the data left in a consistent state?

### 1.4 Security Threat Model

- **Authentication**: Can this be accessed without login? With an expired token?
- **Authorization**: Can user A access user B's data? Can a free user access pro features?
- **Data exposure**: Are we returning more fields than the client needs? PII in logs?
- **Injection**: Any string concatenation in SQL, HTML, or shell commands?
- **Rate limiting**: Can this endpoint be abused? Is there a cost multiplier (e.g., triggers expensive AI call)?

### 1.5 Impact Analysis

- **What existing features could this break?** Trace all callers and consumers.
- **What data could this corrupt?** Check migration safety, default values, nullable columns.
- **What performance could this degrade?** New queries without indexes? N+1? Large payloads?
- **What's the rollback plan?** If this goes wrong in production, how do we undo it?

### 1.6 Plan Completeness Checklist

Before finalizing any plan, verify:

1. Every user-facing state is accounted for (loading, empty, error, success, partial)
2. Every external call has error handling specified
3. Every input has validation rules defined
4. Edge cases are explicitly listed (not "handle edge cases" — LIST them)
5. The testing strategy covers more than just the happy path
6. Performance implications are noted for any new queries or API calls
7. Security implications are noted for any new endpoints or data access
8. Migration safety is confirmed (no data loss, backwards compatible)

---

## PHASE 2: IMPLEMENTATION — Defensive by Default

### 2.1 Error Handling Rules

- **Every `await` gets error handling** — no unhandled promise rejections
- **Every API/DB client call checks for errors before accessing response data**
- **Every array index access is bounds-checked** — `array[i]` needs `if (i < array.length)`
- **Every optional chain has a fallback** — what happens when the value IS nullish?
- **Network errors get user-facing messages** — not silent failures or console.log only
- **Partial failure states are handled** — if step 2 of 3 fails, what happens to step 1's data?

### 2.2 UI State Coverage

For every component that displays data, implement ALL states:

- **Loading**: Skeleton or spinner (not blank screen)
- **Empty**: Helpful message with action (not blank screen)
- **Error**: Specific message with retry option (not generic "Something went wrong")
- **Success**: The actual content
- **Partial**: Some data loaded, some failed (don't hide the successes)
- **Stale**: Visual indicator if data might be outdated

### 2.3 Implementation Completeness Check

After writing each piece of code, verify:

1. What happens if called with null? undefined? empty string? empty array?
2. What happens if the network call fails? Times out? Returns unexpected shape?
3. What happens if the user navigates away mid-operation?
4. What happens if two users do this simultaneously?
5. Did I handle the error path in the UI, not just console.log it?
6. Am I exposing any data the user shouldn't see?

---

## PHASE 3: TESTING — Prove It Works, Then Prove It Fails Gracefully

### 3.1 Test Categories (ALL Required)

For every feature or fix, address each category:

**Happy Path Tests**
- Basic functionality works with typical inputs
- Verify the exact output/behavior, not just "no errors"

**Input Boundary Tests**
- Empty inputs (empty string, empty array, null, undefined)
- Minimum valid input (1 character, single item)
- Maximum valid input (at the limit)
- Just over the limit (should reject gracefully)
- Special characters, unicode, very long strings

**Error Path Tests**
- Network failure during operation
- Auth token expired mid-session
- Invalid data from API (missing fields, wrong types)
- Database constraint violations (duplicate key, foreign key)
- Rate limit hit

**State Transition Tests**
- Component handles loading → success correctly
- Component handles loading → error → retry → success
- State is consistent after error recovery
- Back button / navigation during async operation

**Security Tests**
- Unauthorized access returns 401/403, not 500
- Cross-user data access is blocked
- SQL/XSS payloads in inputs are handled safely
- Sensitive data isn't leaked in error messages

**Regression Tests**
- Existing functionality still works after the change
- Related features aren't broken by side effects

### 3.2 Testing Completeness Check

Verify before moving on:

1. Every public function/endpoint has at least one happy path test
2. Every error code/message returned has a test that triggers it
3. Every conditional branch in the code is exercised by a test
4. Boundary values for every input are tested
5. The test suite would CATCH a regression if someone broke this feature

---

## PHASE 4: ANALYSIS & REVIEW — Audit Like a Senior Engineer

When analyzing existing code, reviewing a feature, investigating a bug, or auditing for improvements, apply this systematic framework. Do NOT give surface-level "looks fine" answers.

### 4.1 Existing Code Audit

When asked to analyze or review existing code:

- **Read the full flow**, not just the file mentioned — trace the data from entry point to database and back
- **Map every conditional branch** — is every if/else/switch case handled? Are there missing cases?
- **Check every external call** — does each API/DB/service call have proper error handling?
- **Identify silent failures** — places where errors are caught but swallowed (empty catch blocks, `.catch(() => {})`, missing error states in UI)
- **Check null/undefined paths** — what happens when optional data is actually missing? Is the code assuming data always exists?
- **Verify auth boundaries** — is every endpoint/query properly scoped to the authenticated user?

### 4.2 Bug Investigation

When investigating a bug or unexpected behavior:

- **Reproduce the full path** — don't guess. Trace the exact code path from trigger to symptom
- **Check ALL callers** — if the bug is in a shared function, who else calls it? Are they affected too?
- **Look for the systemic cause** — a bug in one place often indicates the same pattern elsewhere. Search for similar patterns in the codebase
- **Identify what ELSE could break** — fixing the immediate bug is not enough. What related code has the same vulnerability?
- **Check data integrity** — has the bug corrupted any existing data? Does the fix need a data migration or backfill?
- **Verify the fix prevents recurrence** — will this same bug happen again for new users, new data, or new features? If yes, fix the root cause, not the symptom

### 4.3 Feature Improvement Analysis

When asked to improve or optimize an existing feature:

- **Benchmark the current state** — what's the actual performance/behavior now? Don't optimize blindly
- **Identify ALL consumers** — who depends on this feature? Will improvements break any downstream consumers?
- **Check for unhandled edge cases in the current implementation**:
  - What inputs/states does the current code NOT handle?
  - What error paths are missing or incomplete?
  - Are there race conditions or timing issues?
  - Does it degrade gracefully under load?
- **Assess the blast radius** — how many files, functions, and features does this change touch?
- **Propose with tradeoffs** — every improvement has a cost (complexity, performance, migration). State the tradeoffs explicitly

### 4.4 Architecture Review

When reviewing system design or architecture:

- **Data flow completeness** — trace data from user input through every layer to storage and back. Are there gaps?
- **Error propagation** — when something fails deep in the stack, does the error surface correctly to the user? Or does it get lost?
- **Consistency guarantees** — if a multi-step operation fails halfway, is the system in a valid state?
- **Scaling bottlenecks** — what happens at 10x current load? 100x? Where does it break first?
- **Dependency risks** — what happens if a third-party service goes down? Is there a fallback?
- **Security surface area** — every new endpoint, every new data flow is an attack surface. Map them all

### 4.5 Analysis Completeness Check

Before delivering any analysis or review, verify:

1. I traced the complete data flow, not just the surface-level code
2. I identified edge cases the current code does NOT handle
3. I checked for the same pattern/bug elsewhere in the codebase
4. I assessed security implications (auth, data exposure, injection)
5. I considered performance under stress (concurrent users, large data)
6. I noted specific file:line references for every finding
7. My recommendations include tradeoffs, not just "do this"
8. I answered "will this happen again?" and addressed recurrence prevention

---

## PHASE 5: FINAL VERIFICATION — The "Ship It" Checklist

Before declaring ANY task complete, verify every item:

### Code Quality
1. TypeScript compiles with zero errors
2. No lint warnings
3. No `any` types, no `@ts-ignore`, no `eslint-disable` added
4. No console.log/debug statements left in
5. No hardcoded values that should be constants or config

### Functional Completeness
1. Every requirement from the original request is addressed
2. Every edge case identified in planning has code handling it
3. Error states have user-facing feedback, not just thrown exceptions
4. The feature works on first use, not just happy path demo

### Safety
1. No new security vulnerabilities (check OWASP top 10)
2. No data leaks in API responses or error messages
3. Auth/authz is enforced on every new endpoint
4. Database migrations are backwards compatible and reversible

### Resilience
1. External service failures are handled gracefully
2. User can recover from errors without refreshing the page
3. Data integrity is maintained even if operations are interrupted
4. No race conditions in concurrent access scenarios
