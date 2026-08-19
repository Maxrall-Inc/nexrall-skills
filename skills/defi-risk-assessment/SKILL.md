---
name: defi-risk-assessment
description: Assess the risk of a DeFi protocol or position across smart-contract risk, liquidity, oracle dependence, and concentration, before the user deposits or invests. Use when the user asks "is this protocol safe", "should I put money in this", or wants a yield pool, lending market, or token evaluated.
license: MIT
compatibility: Benefits from network access to check TVL, audits, and holder distribution; can also work from information the user provides directly.
metadata:
  author: nexrall
  version: "1.0"
---

# DeFi Risk Assessment

DeFi risk is not one number — it is a stack of independent failure modes, and
a protocol that is safe against one can be wide open on another. A user
deciding whether to deposit needs the *separate* risks named, sized, and
weighted, not collapsed into a single "safe / not safe" verdict that hides
which assumption would have to break.

## The risk stack (assess each independently)

### 1. Smart-contract risk
Can the code lose funds on its own? This is the domain of the
smart-contract-review skill — run that lens first: reentrancy, access control,
unchecked calls, upgradeability (is the proxy admin a multisig or a single
EOA?). For an *established* protocol, note whether it has public audits and a
bug bounty; for a new one, the absence of both is itself a finding.

### 2. Liquidity risk
Can you get out?
- Depth: how much can be withdrawn/swapped before meaningful slippage?
- Lockup: is there a staking lock, a vest, or a withdrawal delay?
- Exit: is there a single liquidity provider / pool that, if pulled, strands
  everyone else?

### 3. Oracle / price risk
What price does the protocol trust, and can it be moved?
- Spot-DEX prices are flash-loan manipulable within one block.
- A stale or centralized oracle (a single reporter) is a single point of
  failure.
- A token whose price the protocol *derives* from its own pool is circular and
  exploitable.

### 4. Concentration / governance risk
- Token supply: do a few addresses control governance or the majority of the
  supply?
- Admin keys: can an owner mint, pause, or upgrade unilaterally? (A protocol
  with an active admin key is "trust me", not "trustless", no matter the
  marketing.)
- Upgradeability: what changes can a governance vote or multisig push, and how
  quickly?

### 5. Counterparty / composability risk
What does the protocol depend on?
- Other protocols (a lending market built on another lending market inherits
  that one's risk).
- Bridges and wrapped assets (a bridged token is only as good as the bridge).
- Stablecoins with their own solvency question.

## Procedure

1. Identify the protocol and the *specific position* the user is considering
   (which pool, which asset, how much, for how long).
2. Gather evidence for each of the five risks — contracts, audits, TVL,
   liquidity depth, holder concentration, oracle design.
3. Rate each risk **Low / Medium / High**, with a one-line reason.
4. Give the verdict in terms of *what has to be true for you to lose money*,
   not a single score.

## Output format

```
Protocol / position: <name>
- Smart-contract risk: Low/Med/High — <reason>
- Liquidity risk:    Low/Med/High — <reason>
- Oracle/price risk: Low/Med/High — <reason>
- Concentration/governance risk: Low/Med/High — <reason>
- Composability risk: Low/Med/High — <reason>
Biggest single risk: <the one failure mode that dominates>
Verdict: <a sentence that names the assumption the position depends on>
```

## Boundaries

This is an assessment of *technical* risk, not financial advice and not a
guarantee. State that explicitly. A "Low" rating is still not "risk-free" —
crypto has no risk-free yield. If an offered yield is far above the market
average, lead with the question that explains most DeFi losses: *where is the
yield actually coming from?* — and check whether it is just paying early
depositors from new deposits (a ponzi dynamic) rather than real protocol
revenue.
