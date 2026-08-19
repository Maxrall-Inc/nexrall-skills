# Contributing to Nexrall Skills

These skills use the open [Agent Skills](https://agentskills.io) format
(`SKILL.md` + optional `references/`, `scripts/`, `assets/`), so anything you
add here works in every compliant client — Claude Code, Codex, Gemini CLI,
Cursor, Nexrall Code, and 20+ more — without modification.

## A good skill, in one line

A skill is a **routing rule plus a playbook**. The `description` frontmatter is
the routing rule (it decides *when* the agent loads the skill); the body is the
playbook (it decides *what the agent does* once loaded). Most rejected skills
fail the routing rule, not the playbook.

Two special cases:

- **`evidence-first` is the router.** It exists to *point* at the other
  reliability/security skills, not to re-explain them. Keep it a routing table
  plus the five-trap summary — do not fold a full skill's procedure into it.
- **`references/war-stories.md` is the proof.** A rule with no scar behind it
  is a prompt, not a skill. When you add a rule, add the incident that forced
  it (what we first believed → what actually happened → why it hurt), or the
  rule doesn't earn its place.

## The bar

Before opening a PR, check each of these:

1. **`description` is "pushy" and third-person.** It must name both *what the
   skill does* and *the literal phrases a user would type to need it*. A skill
   that says only "Helps with PDFs" will never fire. One that says "Extract
   text and tables from PDF files, fill PDF forms, merge documents. Use when
   the user mentions PDFs, forms, document extraction, or uploading a .pdf"
   will. See Anthropic's
   [best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
   for the reasoning.
2. **`name` matches its directory** and is lowercase-letters-and-hyphens only
   (`my-skill`, not `MySkill` or `my_skill`). This is a spec constraint.
3. **SKILL.md stays under ~500 lines.** If it grows past that, split detail
   into `references/` and point at the files from the body. References should
   be **one level deep** (SKILL.md → reference.md, not reference.md → another).
4. **Explain *why*, not just *what*.** Prefer "read the file back after editing
   because the editor can return success without persisting" over "ALWAYS
   re-read the file". Rigid MUST/NEVER walls without reasoning train the model
   to pattern-match instead of understand.
5. **No surprise.** A skill's contents must not do anything its description
   doesn't lead the user to expect. No exfiltration, no hidden side effects.

## Directory layout

```
skills/
  <skill-name>/
    SKILL.md          # required: frontmatter (name, description) + instructions
    references/       # optional: docs loaded on demand (one level deep)
    scripts/          # optional: executable code for deterministic steps
    assets/           # optional: templates and other files copied into output
```

## The frontmatter

```yaml
---
name: my-skill
description: What it does AND the phrases that mean "use this skill".
license: MIT
compatibility: Requires git and network access   # only when genuinely needed
metadata:
  author: your-name
  version: "1.0"
---
```

`name` and `description` are required. Everything else is optional.

## Testing your skill

The honest test is **does it fire when it should, and stay quiet when it
shouldn't?** Write 8–10 "should trigger" prompts and 8–10 "should not trigger"
prompts (the near-misses matter more than the obvious negatives) and check the
skill gets picked for the first set and ignored for the second. Anthropic's
official `skill-creator` skill (in
[anthropics/skills](https://github.com/anthropics/skills)) has a full eval
loop if you want the rigorous version.

## Pull request checklist

- [ ] New skill is one directory with a `SKILL.md` at its root
- [ ] `name` matches the directory name
- [ ] `description` names trigger phrases, in third person
- [ ] If the skill adds a rule, a `references/war-stories.md` entry names the
      incident behind it (not just "best practice")
- [ ] No bundled `hooks.json` / `mcp.json` that would execute code on install
- [ ] License is MIT (or you've said otherwise explicitly)
- [ ] Ran the trigger test above and pasted the result in the PR

Questions? Open an issue or reach out at https://nexrall.com.
