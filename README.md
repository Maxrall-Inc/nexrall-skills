# Nexrall Skills

> **Evidence over claims.** Every skill in this repo exists because a real
> coding agent once reported "done" when the file never changed, "green" when
> the test was lying, or "safe" when the thing it read was trying to steer it.

Production-grade [Agent Skills](https://agentskills.io) maintained by the
[Nexrall](https://nexrall.com) team. Each skill is a plain `SKILL.md` folder
that works in **any compliant client** — Claude Code, Codex, Gemini CLI,
Cursor, VS Code, and Nexrall Code alike. No vendor lock-in, no Nexrall-specific
format.

Most skill collections chase "yet another dev prompt." This one is different.
We encode the lessons a real team learned **the hard way** — not a
283,000-and-1st template, but the two categories the ecosystem is short on,
where battle scars are worth more than another prompt:

- **Reliability** — the skills that stop an AI coding agent from lying to you.
- **Security** — auditing a skill/plugin *before* you install it, and hardening
  against prompt-injection in anything the agent reads.
- **Blockchain** — a vertical pack with real appetite and almost no curated
  SKILL.md packs.

**Start with [`evidence-first`](skills/evidence-first/SKILL.md)** — the master
workflow that routes you to the right discipline for whatever you're doing.
Then read its [`war-stories.md`](skills/evidence-first/references/war-stories.md)
to see the actual incidents behind each rule. The rules are not ceremony; they
are scars.

## Install

**One command, any client:**

```bash
npx skills add Maxrall-Inc/nexrall-skills
```

This installs the pack into whatever agent directory you have (Claude Code,
Codex, Cursor, Gemini CLI, Nexrall Code, and 70+ more) via the
[skills.sh](https://skills.sh) CLI. Add `--agent <name>` to target a specific
agent, or `--list` to see what it will install before committing.

**Claude Code plugin (native):**

```bash
/plugin marketplace add Maxrall-Inc/nexrall-skills
/plugin install nexrall-skills@nexrall-skills
```

**Manual install** — copy the skill folder you want into the skills directory
your client scans:

```bash
# per-project (Claude Code, Codex, Cursor, Nexrall Code, …)
cp -r skills/agent-verification .agents/skills/

# or the client-native project path
cp -r skills/agent-verification .claude/skills/   # .nexrall/skills/ for Nexrall

# everywhere (user scope)
cp -r skills/agent-verification ~/.agents/skills/
```

The agent picks the skill up automatically on the next session: only its
`name` + `description` sit in context up front, and the full body loads on
demand ("progressive disclosure").

## The skills

### Reliability — make the agent prove its work

| Skill | What it stops |
|---|---|
| [`evidence-first`](skills/evidence-first/SKILL.md) | **The master workflow** — routes every "I'm done" through the right check |
| [`agent-verification`](skills/agent-verification/SKILL.md) | "I've made that edit" when the file never changed |
| [`flaky-test-detector`](skills/flaky-test-detector/SKILL.md) | Test failures that come and go, blamed on code that's actually fine |
| [`test-integrity-guard`](skills/test-integrity-guard/SKILL.md) | Weakening an assertion to make a test pass |
| [`claim-evidence`](skills/claim-evidence/SKILL.md) | Confident-sounding claims with no `file:line` behind them |
| [`refactor-verifier`](skills/refactor-verifier/SKILL.md) | Renames/extractions that miss a call site |

### Security — trust nothing the agent reads

| Skill | What it stops |
|---|---|
| [`skill-security-audit`](skills/skill-security-audit/SKILL.md) | Installing a skill/plugin that does more than its description says |
| [`prompt-injection-hardening`](skills/prompt-injection-hardening/SKILL.md) | Instruction injection in READMEs, config, CLAUDE.md, tool output |

### Blockchain — a vertical domain pack

| Skill | What it does |
|---|---|
| [`smart-contract-review`](skills/smart-contract-review/SKILL.md) | Reviews Solidity for the vulnerabilities that actually lose funds |
| [`on-chain-analyst`](skills/on-chain-analyst/SKILL.md) | Reads blockchains/explorers to answer "what happened and to whom" |
| [`defi-risk-assessment`](skills/defi-risk-assessment/SKILL.md) | Sizes up a protocol's smart-contract, liquidity, and oracle risk |
| [`crypto-news-digest`](skills/crypto-news-digest/SKILL.md) | Turns many news sources into one structured daily digest |

## Why "evidence over claims", and not more "dev" skills

The public skill indexes already list ~289k development-and-engineering skills.
A 289,001st commit-message helper moves nothing. The single most expensive
failure in AI coding is **not** a wrong line of code — it is an agent that
*sounds right while being wrong*: reporting success that never happened,
citing a function it never read, certifying a suite that compares two wrongs
to each other.

The Reliability and Security packs are one discipline under two names: *find
the evidence before you believe the signal.* That discipline is the thing
users complain about most, the thing that costs the most when missing, and the
thing a generic prompt cannot teach because it requires the *specific* failure
modes we documented as war stories.

See `CONTRIBUTING.md` for the quality bar and how to add a skill.

## License

MIT — use freely, fork, and contribute. See `LICENSE`.

---

Made with the same paranoia we ship in [Nexrall Code](https://nexrall.com).
