# Browser Baseline: ChatGPT 5.2 (Clean Session)

**Date:** 2026-02-13
**Method:** Fresh chatgpt.com session, not logged in, model 5.2 (default)
**Rules:** Asked to use ONLY training data, no web search

---

## Questions & Answers

### 1. What is the current typical gas price on Ethereum mainnet in gwei?

**ChatGPT said:** 20-30 gwei typical average. Low: 8-15 gwei. Moderate: 20-35 gwei. High: 50-150+ gwei.

**Reality:** 0.05-0.1 gwei

**Error:** ~400-600x too high ❌

### 2. What does a simple ETH transfer cost in USD?

**ChatGPT said:** ~$1-3 USD (at 25 gwei, ETH $2,000-3,000). During congestion $5-20+.

**Reality:** ~$0.002

**Error:** ~500-1500x too high ❌

### 3. What is ERC-8004?

**ChatGPT said:** "There is no widely adopted or finalized Ethereum standard known as ERC-8004." Listed it as possibly a draft, private, or non-canonical reference.

**Reality:** Deployed mainnet January 29, 2026. On-chain agent identity registry with Identity, Reputation, and Validation registries.

**Error:** Completely unknown ❌

### 4. What is x402?

**ChatGPT said:** "There is no widely recognized Ethereum, networking, or cryptography standard formally known as 'x402'." Speculated it could be related to HTTP 402.

**Reality:** Production-ready HTTP payment protocol by Coinbase. Actively deployed with SDKs.

**Error:** Completely unknown ❌ (vague HTTP 402 guess in right direction)

### 5. Has the Pectra upgrade shipped? If so, when?

**ChatGPT said:** "The Pectra upgrade had not yet shipped to Ethereum mainnet" as of mid-2024 cutoff. Was in planning/spec discussion phases.

**Reality:** Shipped May 7, 2025

**Error:** Doesn't know it shipped ❌

---

## Summary

| Question | ChatGPT 5.2 Answer | Reality | Correct? |
|----------|-------------------|---------|----------|
| Gas price | 20-30 gwei | 0.05-0.1 gwei | ❌ 400x off |
| ETH transfer cost | $1-3 | $0.002 | ❌ 500x off |
| ERC-8004 | Unknown | Mainnet Jan 2026 | ❌ |
| x402 | Unknown | Coinbase HTTP payment protocol | ❌ |
| Pectra shipped? | No (as of mid-2024) | Yes, May 7, 2025 | ❌ |

**Score: 0/5 correct.** Training data cutoff is mid-2024. ChatGPT 5.2 web UI default model has the same blind spots as the API models.
