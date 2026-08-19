---
name: test-integrity-guard
description: Prevent weakening or deleting tests to make a failing suite pass, and decide whether a failure is in the code or in the test. Use when a test fails and the user is tempted to "just make it green", when you are asked to change a test, or when code changes make an existing test fail.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Test Integrity Guard

The fastest way to a green suite is also the most corrosive: loosen the
assertion, skip the test, or delete it. A test that was bent to fit the code
guards nothing. This skill enforces the discipline of asking *what actually
failed* before changing anything.

## The one rule

**Never change a test's assertion, skip, or delete a test in the same breath
as "fixing" a failure.** The two are separate acts with separate justifications.

## Procedure when a test fails

1. **Read the failure completely** — the assertion, the expected vs actual, and
   the stack trace. Do not pattern-match on the test name.
2. **Classify the failure.** It is exactly one of:

   - **The code is wrong.** The test encodes a correct expectation the code no
     longer meets. Fix the code.
   - **The test is wrong.** The test's expectation no longer matches the
     intended behavior (e.g. a feature was removed on purpose, an API was
     legitimately renamed). Fix the test — *and* say why it was wrong, in the
     commit.
   - **The test is flaky or environment-dependent.** Diagnose with the
     flaky-test-detector skill, don't loosen the assertion.

3. **If the code is wrong, fix the code.** If the test is wrong, fix the test
   — with a written reason. Never flip between the two silently.

## Red flags that mean "stop and think"

Each of these is a sign the fix is hiding a problem, not solving it:

- Changing `assert.equal(x, y)` to `assert.ok(x)` or comparing two outputs to
  each other instead of to a literal.
- Raising a tolerance, a timeout, or a retry count to absorb a real failure.
- `it.skip` / `describe.skip` / `pytest.mark.skip` / `.only` on a neighbor.
- Deleting a test that was checking a feature you're not 100% sure is gone.
- Commenting out an assertion so a "removed" behavior still passes.
- Comparing two variants of the same (possibly both-wrong) data to each other,
  which passes because they share the bug.

## When deleting a test is legitimate

A test may be deleted only when the *feature it guards* is gone, and only with
a guard in place that the deletion is real:

- Verify the feature is actually removed (grep for the function/route/table —
  zero callers, zero references, zero dynamic references).
- If the test read from a source-of-truth table or constant that was deleted,
  freeze a literal baseline of what it asserted *before* removal, with a
  comment explaining it must not be re-derived from the new code (which would
  make the test a tautology).
- State in the commit message *why* the test goes, not just *that* it goes.

## Output format

```
Failure: <test> — <one-line actual vs expected>
Classification: code is wrong | test is wrong | flaky
Action: <fix the code | fix the test with reason | diagnose flake>
Reason (required if touching the test): <one sentence>
```
