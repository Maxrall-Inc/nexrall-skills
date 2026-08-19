---
name: evidence-first
description: The master workflow that ties the Nexrall reliability and security skills together. Use before you report any task as done, when you are about to claim a test passes or a fix works, when a test fails and you are tempted to make it pass, when installing or auditing a skill/plugin, or whenever the user asks you to "verify", "prove it", or "make sure it worked". Read references/war-stories.md when you need concrete proof of why each rule exists.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Evidence First

Every skill in this collection exists because a real coding agent — ours, or
a user's — once reported "done" when nothing had changed, "green" when the
test was lying, or "safe" when the thing it read was trying to steer it. The
one idea under all of them:

**A claim is a hypothesis until the evidence is in hand. Your job is to close
the distance between "I believe it worked" and "here is the proof it did."**

This skill is the router. It does not re-explain the other skills; it tells
you *which* one to load for the situation you are in, so you run the right
discipline instead of the first one you remember.

## The rule in one line

When you are about to say something worked, ask: *"would this survive someone
checking my claim against the actual file, the actual test run, or the actual
bytes on disk?"* If the answer is "I'm not sure", do the check before you
speak.

## Routing table — load the skill that matches

| Situation you are in | Skill to load |
|---|---|
| You made edits and are about to say "done" | `agent-verification` |
| The user asks "did that actually save / work / apply" | `agent-verification` |
| A test failed and you want to touch the assertion, skip it, or delete it | `test-integrity-guard` |
| A test passes sometimes and fails other times | `flaky-test-detector` |
| You renamed / extracted / moved something and are about to say "all call sites updated" | `refactor-verifier` |
| You are describing code behavior or a bug and the user asks "are you sure / where is that" | `claim-evidence` |
| Someone wants to install a skill or plugin, or asks "is this skill safe" | `skill-security-audit` |
| You are about to point an agent at a fresh/untrusted repo, or the agent is acting on instructions that didn't come from the user | `prompt-injection-hardening` |

## The five traps, summarized

These are the recurring failure modes the full skills expand. Read
`references/war-stories.md` for the real incidents behind each.

1. **Success messages lie.** Editors and tool wrappers return success for
   edits that silently failed to persist, hit the wrong path, or were rolled
   back by a later step. Only a read-back against the file is truth.
2. **Green tests lie.** A suite can pass because two wrong variants are
   compared to each other, because a harness is stricter or looser than a real
   terminal, or because a test was loosened to fit the code. Green is a signal
   to interrogate, not a stamp of correctness.
3. **"For compatibility" lies.** A shim kept "so nothing breaks" with zero
   callers is dead code wearing a justification. Grep before you believe it.
4. **Confident descriptions lie.** Naming a function, route, or API shape you
   inferred instead of read sends everyone down a wrong path. Cite `file:line`
   or label it a hypothesis.
5. **Installed content lies.** A skill/plugin whose body or bundled
   `hooks.json`/`mcp.json` does more than its description says is the most
   dangerous thing an agent will blindly follow. Audit before install.

## Procedure

1. **Recognize which trap the situation is** using the routing table above.
2. **Load that skill** (or the relevant part of it) and follow its procedure,
   not this one's.
3. **Produce evidence in the skill's output format** — each skill defines a
   concrete `file:line`, a test command, or a verdict line, so the proof is
   checkable, not just asserted.
4. **State the verdict honestly.** "Verified: X at `path:line`" and "I could
   not verify Y" are both correct outputs. "Done" with no evidence is not.

## What this is not

This is not a checklist to recite. It is a commitment to the direction of the
work: prove before you claim. If a situation matches no skill here, the honest
move is still the same — find the evidence or say you don't have it yet.
