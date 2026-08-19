---
name: flaky-test-detector
description: Diagnose tests that pass and fail non-deterministically, instead of blaming the code under test or "fixing" the test. Use when a test fails sometimes but not others, when the user says a test is "flaky", "unstable", "passes locally but fails in CI", or when the same test gives different results on repeated runs.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Flaky Test Detector

A test that fails intermittently is worse than a test that always fails: it
erodes trust in the whole suite, and the natural instinct — "tweak it until
it passes" — usually hides a real race, a shared-state bug, or a broken
assumption. This skill diagnoses *why* a test is flaky before anything is
changed.

## First: prove it is flaky before touching anything

Run the failing test in isolation, several times, and record the outcomes. A
single failure is not flakiness — it may be a genuine bug that only manifests
on a particular input.

```bash
for i in 1 2 3 4 5; do <run-just-this-test>; echo "exit=$?"; done
```

- Fails every time → not flaky; treat it as a real bug (use the
  test-integrity-guard skill instead of loosening it).
- Passes/fails inconsistently → flaky; continue below.

## The three causes, in order of likelihood

### 1. Shared state between tests

The most common cause. Tests that mutate a shared store, cache, database, or
in-process singleton leak into each other. Evidence to look for:
- The test passes in isolation but fails when the whole file/suite runs.
- Two tests write to the same fixture, temp file, or module-level variable.
- An eviction/cache keyed by insertion order (LRU) that a "touched" entry
  doesn't refresh — the test count looks wrong because entries weren't evicted
  in the order assumed.

Fix the **isolation**, not the assertion: give each test its own fixture/DB/
temp dir, or reset shared state in a `beforeEach`/`afterEach`.

### 2. Timing or ordering assumptions

- The test waits a fixed `sleep` instead of an event/condition.
- It assumes async work finishes by the time the next line runs.
- Two tests race on the same resource and the winner varies.

Fix by waiting on a condition or sequencing explicitly — not by lengthening
the sleep (that only lowers the failure rate, it doesn't remove it).

### 3. Environment nondeterminism

- Iteration order of a hash map / object keys that isn't guaranteed.
- Reliance on locale, timezone, clock, or filesystem `readdir` order.
- A value derived from a real network/clock that changes between runs.

Fix by pinning the source of nondeterminism (sort before asserting, inject a
fixed clock, freeze the map order).

## What NOT to do

- Do not change the assertion to something looser just to make it green.
- Do not skip the test (`it.skip`, `.only` on another, `pytest.mark.skip`).
- Do not delete the test. A flaky test still guards something; silencing it
  removes the guard.

## Output format

```
Diagnosis: shared state | timing | environment
Evidence: <the specific line or behavior that proves it>
Root cause: <one sentence>
Fix: <change to isolation/sequencing/determinism — not to the assertion>
```

If you genuinely cannot reproduce the flake after several isolated runs, say
so and report what you ran — do not invent a cause.
