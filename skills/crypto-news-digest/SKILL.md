---
name: crypto-news-digest
description: Aggregate blockchain and crypto news from multiple sources into one structured daily digest. Use when the user asks for a crypto or blockchain news roundup, a daily digest, "what happened in crypto today", or wants market moves and regulation summarized from several outlets.
license: MIT
compatibility: Requires network access to fetch news sources.
metadata:
  author: nexrall
  version: "1.0"
---

# Crypto News Digest

Crypto news is noisy, duplicated across outlets, and often PR dressed as
reporting. A useful digest isn't "everything published today" — it's the few
developments that actually moved the market or changed the rules, each from
more than one source, organized so the reader can skim in thirty seconds.

## Sources to pull from

Draw from several, and prefer primary/established outlets over aggregators:
CoinDesk, The Block, CryptoSlate, crypto.news, Decrypt, Yahoo Finance
(crypto), CNBC (crypto). Cross-check any single-source claim against at least
one other outlet before including it as fact.

## What to include (and what to skip)

Include only items that are **material**:

- **Market / price** — large moves and the reason behind them (not every
  percentage wiggle).
- **ETF & institutional** — flows, filings, approvals, major custody changes.
- **Regulation / legal** — enforcement actions, new rules, court rulings,
  government policy that changes what is legal to do.
- **Stablecoins** — peg events, issuer reserves, new launches, de-pegs.
- **Altcoins & DeFi** — protocol launches, exploits, notable partnerships.
- **Security** — hacks, exploits, vulnerabilities disclosed.
- **Enterprise / adoption** — real integrations, not roadmap announcements.

Skip: every token's daily PR, "X hints at Y" without substance, predictions,
and anything with no second source.

## Procedure

1. Gather from at least 3–4 sources; note which outlets carried each story.
2. Filter to the material items using the list above.
3. For each item: one fact line + why it matters (one sentence) + the
   source(s). If sources conflict on a number, give the range and say it's
   disputed.
4. Order by importance (market-moving first), not by category.
5. Write in the reader's language, plain and skimmable.

## Output structure

```
# Bản tin Blockchain ngày DD/MM/YYYY
(use the reader's language; adapt section titles to it)

## Thị trường / giá
- <fact> — <why it matters> — <sources>

## ETF & tổ chức
…

## Quy định pháp lý
…

## Stablecoin
…

## Altcoin & DeFi
…

## Bảo mật
…

## Doanh nghiệp / áp dụng
…
```

## Boundaries

Every factual claim needs a source; every opinion needs a "why". Do not
fabricate a price, a filing, or a ruling — if you cannot verify a number from
a source you actually fetched, either omit it or mark it explicitly as
unverified. A digest is only worth reading if the reader can trust that what
it says is real.
