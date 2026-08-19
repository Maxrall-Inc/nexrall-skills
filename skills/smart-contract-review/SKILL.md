---
name: smart-contract-review
description: Review a Solidity smart contract for the vulnerabilities that actually cause fund loss, before it is deployed. Use when the user shares a .sol file or contract address, asks to audit a contract, review DeFi code, or check whether a smart contract is safe.
license: MIT
metadata:
  author: nexrall
  version: "1.0"
---

# Smart Contract Review

Smart-contract bugs are permanent and usually expensive: code on-chain cannot
be patched after the fact, and a missed vulnerability means real funds lost.
This skill is a focused security review for Solidity, ordered by what has
historically caused the most real-world loss, not by textbook completeness.

## Read the contract first, fully

Do not review a contract you have not read end to end, including inherited
contracts and interfaces it calls. Most misses come from reviewing the entry
point while skipping a base contract, a library, or an `external` call target.

## The vulnerabilities that lose money, in order

### 1. Reentrancy

A contract that calls an external contract, then updates its own state
*afterward*, can be re-entered before the state settles — the classic
`withdraw` that sends funds before zeroing the balance.

- Look for the pattern: external call → state change. The state change must
  come first, or the call must be `nonReentrant` (OpenZeppelin
  `ReentrancyGuard`).
- Pay special attention to ERC-777/ERC-721 callback paths and native `call{value:}`.

### 2. Access control

Who can call the state-changing functions? Check every `onlyOwner` /
`onlyRole` / custom modifier:

- A missing modifier on a `setX`/`withdraw`/`mint`/`upgrade` function.
- An over-permissive role (`DEFAULT_ADMIN_ROLE` handed to the wrong address).
- `tx.origin` used for authorization. `tx.origin` is the externally-owned
  account that started the whole transaction chain, not `msg.sender` (the
  immediate caller) — they're equal for a direct call, but diverge the moment
  any contract is interposed. An attacker can trick the real owner into
  calling a malicious contract, which then calls the target on the owner's
  behalf: `msg.sender` is the malicious contract (correctly rejected), but
  `tx.origin` is still the legitimate owner (wrongly authorized). Use
  `msg.sender` for authorization checks.
- `initialize()` left callable (an uninitialized proxy can be taken over).

### 3. Integer overflow / underflow

Solidity ≥0.8.0 reverts on overflow by default, but:

- `unchecked { }` blocks bypass it — audit every one.
- Older pragmas (`^0.7` and below) have no built-in check at all.
- Casting and bit-shift edge cases can still wrap.

### 4. Unchecked external return values and failed sends

- `transfer`/`send` are capped at 2300 gas and can silently fail on contracts
  with a receive/fallback that costs more.
- `call` (the low-level one) returns a bool — an ignored `bool` means a failed
  call is treated as success.

### 5. Oracle / price manipulation

- A price derived from a spot DEX pair (`getReserves()`) is flash-loan
  manipulable — anyone can move the price within one transaction.
- Check whether a malicious actor could profitably distort the input the
  contract reads.

### 6. Flash-loan and governance attacks

- Functions that let a large, instant loan change a parameter the contract
  trusts (governance voting on spot balances, minting based on current TVL).
- `delegatecall` to an untrusted/upgradeable address — full code-execution
  risk.

### 7. Front-running and MEV

- Transactions where the outcome depends on who gets mined first (sandwichable
  swaps, reveals after commit that can be front-run).

## Output format

```
Contract: <name / address>
Severity findings:
- CRITICAL: <finding> — <location> — <why it loses funds> — <fix>
- HIGH: ...
- MEDIUM: ...
- LOW / informational: ...
Verdict: DO NOT DEPLOY | FIX BEFORE DEPLOY | LGTM (with caveats)
```

Every finding must point at the exact function/line and explain the
exploitation path, not just name a category. A category without an
exploitation path is a guess, not a finding.

## Boundaries

This is a *review*, not an audit certificate. State clearly that a clean
review is not a guarantee, recommend a professional audit + testnet/formal
verification for anything holding real value, and do not claim to have found
"all" bugs — only the ones you can demonstrate.
