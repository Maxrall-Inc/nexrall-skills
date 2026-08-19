---
name: refactor-verifier
description: Verify that a rename, extraction, or signature change updated every call site and import across the codebase, with no stale references left behind. Use when renaming a function, changing a type signature, moving a file, or extracting a helper — before you declare the refactor complete.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Refactor Verifier

A refactor that misses one call site is a regression, not an improvement. The
classic failure: you rename `getUser` to `fetchUser` and update the file you
are looking at, but three other files still call the old name — and the
language doesn't error because the old export was left in "for safety", or
because they're in another package, or because the reference is dynamic. This
skill makes "no call site left behind" a checkable claim, not an assumption.

## Procedure

1. **Find every reference BEFORE you rename.** Use your editor's semantic
   tools if available (find-references, find-symbol, go-to-definition), and a
   plain text search (grep, or your client's search tool) as the backstop —
   semantic tools miss string references, dynamic `require`/`import()` calls,
   and config/template mentions; text search misses indirect usages that a
   language server resolves (re-exports, interface implementations). Use both.
2. **Enumerate the full surface.** For a rename/removal, the old name can
   appear as:
   - an import (`import { oldName }`, `require('./old')`)
   - a call (`oldName(...)`)
   - a string (`'oldName'`, a route path, a tool name, a config key)
   - a re-export (`export { oldName }`)
   - a dynamic reference (`obj['oldName']`, a lookup table, a registry entry)
3. **Change the definition AND all references in one pass.** If you use a
   tool that only touches one file, do it file by file but verify the whole
   set at the end.
4. **Re-scan for the old name after the change.** The old name should now
   appear *nowhere* except in legitimate historical contexts (changelogs,
   migration notes, a compatibility shim you are deliberately keeping).
5. **If you keep a shim or alias "for compatibility", verify it is actually
   used.** A no-op shim with zero callers and zero dynamic references is dead
   code wearing a justification. Grep for it; if nothing uses it, delete it —
   and flip any test that asserted the shim's existence into one that asserts
   its *removal*.
6. **Verify behavior with the test suite.** Run the relevant tests. A green
   suite after a rename is necessary but not sufficient — it will not catch a
   stale reference in an untested path, which is why steps 1 and 4 matter.

## What "complete" means

You are not done until BOTH hold:

- A text search for the old name returns only legitimate historical/alias
  hits (which you can name and justify).
- The test suite (or the relevant subset) passes.

## Output format

```
Refactor: <old> -> <new>
References found: <N> (<list of files>)
References updated: <N>
Old-name residue: <none | list with justification>
Tests: <pass/fail, with the command run>
Verdict: complete | <what is still left>
```

## Why this is worth a skill

The failure is silent. Nothing errors when you leave a stale reference in a
string or a config key — it only misbehaves at runtime, sometimes far from
where the mistake is. The discipline of enumerating the surface first, and
re-scanning last, is what catches it before a user does.
