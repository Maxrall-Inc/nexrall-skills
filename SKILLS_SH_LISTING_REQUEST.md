# Listing: request indexing for Maxrall-Inc/nexrall-skills

## Repo

**Maxrall-Inc/nexrall-skills** — https://github.com/Maxrall-Inc/nexrall-skills

Production-grade Agent Skills (agentskills.io format), MIT-licensed, 11 skills in three packs:

- **Reliability** — `agent-verification`, `flaky-test-detector`, `test-integrity-guard`, `claim-evidence`, `refactor-verifier` (stop an AI coding agent from reporting success that never happened)
- **Security** — `skill-security-audit`, `prompt-injection-hardening` (audit a skill/plugin before install; harden against prompt injection)
- **Blockchain** — `smart-contract-review`, `on-chain-analyst`, `defi-risk-assessment`, `crypto-news-digest`

## Verified install works

`npx skills add Maxrall-Inc/nexrall-skills --list` already discovers the repo and reports **"Found 11 skills"** — I verified this live before opening this issue. The repo is public with MIT license, all skills are pure prompt packs (no `hooks.json` / `mcp.json` / executable scripts), and every `SKILL.md` passes the agentskills.io spec (name matches directory, valid charset, description ≤1024 chars, body <500 lines).

## Request

Please index this repo on skills.sh so it appears on the leaderboard / topic pages (`security`, `blockchain`, `testing`, `agent workflows`, etc.).

Thank you!
