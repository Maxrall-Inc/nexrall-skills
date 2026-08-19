---
name: claim-evidence
description: Back every factual claim about a codebase with a file and line number, and refuse to state as fact anything you have not actually read. Use when the user asks "are you sure", "where is that", "prove it", or when you are about to describe code behavior, architecture, or a bug to the user.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Claim Evidence

An agent that states confident falsehoods destroys trust faster than one that
says "I don't know yet." The fix is mechanical: every claim about the code
carries a citation, and a claim you cannot cite is not yet a claim — it is a
hypothesis, and must be labeled as one.

## The distinction that matters

- **Fact** — you have read it in a file this session, and can point at the
  exact location. "The route is defined in `backend/routes/auth.js:42`."
- **Hypothesis** — you believe it from context, training, or a pattern, but
  have not verified it in *this* codebase. "I'd expect the auth middleware to
  run first — I haven't confirmed it yet."

Do not present a hypothesis as a fact. The label is the difference.

## Procedure

1. **Before asserting, read.** If the claim is about this repo, use your
   environment's file-reading and code-navigation tools (read a file, search
   its contents, jump to a definition, find its usages — whatever your client
   exposes) to locate the truth. Do not reason from a file you have only seen
   referenced.
2. **Attach the evidence.** State it as `path:line`, and quote the smallest
   slice that supports the claim.
3. **Narrow claims that are actually narrower.** "The API returns 401 on
   expired tokens" is a fact about one code path; "the whole app is secure"
   is a hypothesis built on it. Keep the claim at the level you verified.
4. **When unsure, say so.** "I haven't verified X" is a correct and valuable
   answer. Prefer it to a guess dressed as knowledge.

## Citation format

Use one of:

```
path/to/file.ts:42  —  `return tokens;`
path/to/file.ts     —  function `resolveToken` (exact line not read)
unknown             —  hypothesis, not verified in this session
```

The `unknown` tag is allowed; omitting it and stating the thing anyway is not.

## Why this matters beyond honesty

Two concrete failure modes this prevents:

- **Stale belief.** You remember `getPricing()` from a previous session, but
  the route now bills through a different path. Only a fresh read catches the
  drift.
- **Hallucinated shape.** Naming a function, file, or API shape you *inferred*
  rather than read sends the user (and your own later steps) down a wrong
  path. A citation forces the read that would have caught it.

## Output format

When the user asks a "where / why / prove it" question, answer with:

```
Claim: <one sentence>
Evidence: <path:line> — <quoted slice>, or "unverified"
Confidence: fact | hypothesis
```
