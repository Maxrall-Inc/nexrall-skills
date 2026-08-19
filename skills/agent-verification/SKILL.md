---
name: agent-verification
description: Verify that file edits and tool calls actually took effect on disk, instead of trusting success messages. Use whenever the user asks you to "make sure it worked", "verify", "double-check", "did that actually save", "confirm the change", or after you have made a batch of edits and need to report whether they are real.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Agent Verification

The most expensive bug in AI coding is not a wrong line of code — it is an
agent reporting "done" when the file on disk never changed. Editors and tool
wrappers can return a success result for an edit that silently failed to
persist, applied to the wrong file, or was overwritten by a later step. This
skill turns "it should have worked" into "here is the proof it did."

## When this matters most

- After a batch of `edit_file` / `multi_edit` / `write_file` calls, before
  claiming the task is complete.
- Whenever the user asks whether something "actually" happened.
- After a tool result that looks odd (an empty diff, a "no change" message,
  a truncation warning, a multi-edit that reported a partial failure).
- Before declaring a fix "done" in a PR or commit message.

## The core rule

**A success message is an intention, not a fact.** The file is the source of
truth. Read it back and compare.

## Procedure

1. **Identify what you claim changed.** For every file you edited, note the
   exact `old_string` → `new_string` (or the write) you intended.
2. **Read the file back.** Use `read_file` (or `search_files` for the changed
   region) to fetch the current on-disk content. Never trust an in-memory
   buffer or the tool's own echo.
3. **Verify the change is actually present.**
   - Search for the NEW text — it must be there.
   - Search for the OLD text — it must be gone (unless it legitimately appears
     elsewhere; if it does, say so and point at the specific location).
4. **Check for truncation and partial writes.** A `multi_edit` with N changes
   can report failure on one while the others were applied — or, in some
   wrappers, roll back everything. Always confirm the END state, not the
   reported count.
5. **Check for silent overwrites.** If two steps wrote the same file, confirm
   the final content contains BOTH intended changes, not just the last one.
6. **Report honestly, with evidence.** Say "verified: `read_file` at
   `path:line` shows the new value `X`". If something did NOT apply, say so
   explicitly and re-do it — never describe a half-applied edit as complete.

## Output format

For each file you were asked to verify, produce a short table:

```
| File            | Intended change       | Verified? | Evidence                     |
|-----------------|-----------------------|-----------|------------------------------|
| src/app.ts      | foo -> bar           | yes       | read_file line 42 shows "bar" |
| src/app.ts      | remove deprecated fn | no        | "old_fn" still present at line 88 |
```

Then one line of verdict: "All changes verified" or "N changes missing —
fixing now."

## Why this beats the naive approach

"Read the file back" sounds obvious; the value is doing it *every time*, not
just when you feel unsure. The failures this catches are exactly the ones that
feel sure: a write to the wrong path, a case-sensitivity mismatch, a
`multi_edit` that rolled back, a template literal that silently truncated
generated output. They all report success. Only the read-back catches them.

## Edge cases

- **Large files:** verify the changed hunk with `read_file` `offset`/`limit`,
  not the whole file.
- **Generated/worker output:** if a step builds a worker or bundle from a
  template, the source file can parse fine while the *generated* output is
  truncated. Verify the artifact, not just the source.
- **Case-insensitive filesystems:** a rename that changes only case can look
  like "no change". Check with the exact expected string.
