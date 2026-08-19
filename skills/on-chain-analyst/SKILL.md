---
name: on-chain-analyst
description: Read blockchains and block explorers to answer questions about on-chain activity — what happened, to whom, how much, and when. Use when the user asks about a transaction, wallet, contract activity, token transfer, whale movement, or wants on-chain data investigated using explorers or indexers like Etherscan or Dune.
license: MIT
compatibility: Requires network access to a block explorer, indexer, or RPC endpoint.
metadata:
  author: nexrall
  version: "1.0"
---

# On-Chain Analyst

On-chain data is the only part of crypto that cannot lie — every transfer,
swap, and deploy is recorded. But the raw data is spread across explorers,
indexers, and RPC endpoints, each with a different query surface. This skill
is a method for turning a question like "what happened here" into a concrete,
cited answer from on-chain evidence.

## Know your data sources

Different questions need different tools; reach for the right one rather than
forcing one tool to do everything.

- **Block explorers** (Etherscan/Arbiscan/Polygonscan/BscScan/Solscan/…):
  transactions, internal calls, token transfers, contract source, and labels.
  Best for "what did this address / tx do".
- **Indexers** (Dune Analytics, The Graph, Flipside, Nansen, Arkham): SQL or
  GraphQL over decoded event data. Best for aggregates — "top holders",
  "volume over time", "how many users did X".
- **RPC / `cast`** (Foundry): raw state and calls — read a storage slot, a
  balance, simulate a call. Best for "what is the current state" and for
  verifying a specific number yourself.

## Procedure

1. **Pin down the entity.** A question about "the address" is ambiguous. Turn
   it into a concrete address, tx hash, contract address, or token — verify it
   by looking it up, don't assume.
2. **Formulate the on-chain question.** "Did this wallet interact with that
   contract", "what tokens does it hold", "where did these funds come from",
   "is this token's supply concentrated".
3. **Pull the evidence.** Query the appropriate source above. Prefer the
   explorer's decoded events/token-transfers page over re-deriving from raw
   calldata.
4. **Cite every number.** Every balance, transfer, or timestamp you state
   should carry its source: an explorer URL, a Dune query id, or the exact RPC
   call you ran. Un-cited on-chain numbers are the crypto equivalent of
   hallucination — confident and wrong.
5. **Interpret, don't just report.** A transfer is a fact; "this looks like a
   rug because liquidity was removed in one tx" is an interpretation. Keep them
   clearly separated.

## Common analyses

- **Wallet profile** — holdings, valuation, activity, age, first funding
  source. Answer "is this a whale / fresh wallet / exchange address".
- **Token transfer trace** — follow funds across hops (exchange deposit →
  mixer → …) and flag where the trail goes cold or hits a known entity.
- **Contract activity** — deploys, upgrades, admin changes, and whether the
  owner can rug (look for mint/withdraw functions guarded by a single EOA).
- **Concentration** — top-holder distribution, whether a few addresses control
  the supply (a red flag for any "community" token).

## Output format

```
Question: <what the user actually asked>
Findings:
- <fact> — <source: explorer URL / dune query / rpc call>
- ...
Interpretation: <clearly separated from the facts>
Uncertainty: <anything you could not verify, stated plainly>
```

## Boundaries

Only claim what the data supports. If an indexer is lagging, an explorer is
rate-limiting, or a chain isn't indexed, say so. "I couldn't verify X" is a
correct on-chain answer; inventing a number is not.
