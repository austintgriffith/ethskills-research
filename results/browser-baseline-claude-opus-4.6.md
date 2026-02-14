# Browser Baseline: Claude Opus 4.6 (Clean Session)

**Date:** 2026-02-13
**Method:** Fresh claude.ai session, logged into Austin's account, Opus 4.6 Extended
**Rules:** Asked to use ONLY training data, no web search

---

## Questions & Answers

### 1. What is the current typical gas price on Ethereum mainnet in gwei?

**Claude said:** 1-10 gwei range during normal activity, sometimes sub-1 gwei. "Dramatic shift from 30-100+ gwei days of 2021-2022." Acknowledges can't give reliable "right now" number.

**Reality:** 0.05-0.1 gwei

**Error:** ~10-100x too high, but directionally much better than ChatGPT ⚠️

### 2. What does a simple ETH transfer cost in USD?

**Claude said:** ~$0.25-$0.75 (at 5 gwei, ETH $2,500-3,500)

**Reality:** ~$0.002

**Error:** ~125-375x too high ⚠️ (better than ChatGPT's $1-3)

### 3. What is ERC-8004?

**Claude said:** "I don't have confident knowledge of this one. ERC numbers in the 8000+ range were being filed in late 2024 / early 2025, but I can't give you a specific description without risk of hallucinating."

**Reality:** Deployed mainnet January 29, 2026. On-chain agent identity registry.

**Error:** Unknown, but honestly admits ignorance ❌ (good calibration)

### 4. What is x402?

**Claude said:** "HTTP 402-based payment protocol — a project (Coinbase was involved, if I recall correctly) that uses the long-dormant HTTP 402 Payment Required status code to enable machine-to-machine crypto payments natively at the HTTP layer."

**Reality:** Exactly correct description. x402 is Coinbase's HTTP 402 payment protocol.

**Verdict:** Directionally correct! ✅ (details fuzzy but core concept right)

### 5. Has the Pectra upgrade shipped? If so, when?

**Claude said:** "I believe it went live on mainnet in May 2025, but I'm not confident on the exact date. It included EIP-7702, increased blob counts, and validator stake cap changes."

**Reality:** Shipped May 7, 2025. EIP-7702, blob throughput increase, BLS precompiles.

**Verdict:** Correct! ✅ (date right, key EIPs right, just low confidence)

---

## Summary

| Question | Claude Opus 4.6 Answer | Reality | Correct? |
|----------|----------------------|---------|----------|
| Gas price | 1-10 gwei | 0.05-0.1 gwei | ⚠️ 10-100x off (better) |
| ETH transfer cost | $0.25-0.75 | $0.002 | ⚠️ 125x off (better) |
| ERC-8004 | Unknown (honest) | Mainnet Jan 2026 | ❌ |
| x402 | HTTP 402 payment, Coinbase | Correct! | ✅ |
| Pectra shipped? | Yes, May 2025, EIP-7702 | Yes, May 7, 2025 | ✅ |

**Score: 2/5 correct, 2/5 partially correct, 1/5 unknown.**

## Key Differences vs ChatGPT 5.2

| Area | ChatGPT 5.2 | Claude Opus 4.6 |
|------|-------------|-----------------|
| Gas estimate | 20-30 gwei | 1-10 gwei (10x closer) |
| Transfer cost | $1-3 | $0.25-0.75 (4x closer) |
| ERC-8004 | Unknown | Unknown |
| x402 | Unknown | Knows it! (Coinbase, HTTP 402) |
| Pectra | Doesn't know it shipped | Knows it shipped May 2025 |
| Training cutoff | Mid-2024 | End of May 2025 |

**Claude Opus 4.6 has a significantly newer training cutoff** (~1 year newer than ChatGPT 5.2's consumer model). This explains knowing about Pectra and x402. Gas prices are still off by 10-100x though — this confirms ethskills gas content is critical even for the best models.

**ERC-8004 remains unknown to both** — this is the strongest case for ethskills content. It deployed January 2026, after all training cutoffs.
