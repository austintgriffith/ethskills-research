# ethskills Iteration Loop Results

**Date:** February 15, 2026  
**Test Model:** GPT-4o (gpt-4o, temperature=0)  
**Methodology:** Each task tested baseline (no skills) vs with skill content injected into system prompt  
**Skills Tested:** `addresses/SKILL.md`, `building-blocks/SKILL.md`, `l2s/SKILL.md` (from PRs #41 and #42)

---

## Summary

| Metric | Baseline | With Skills | Change |
|--------|----------|-------------|--------|
| ✅ Correct | 0/10 (0%) | **8/10 (80%)** | **+80%** |
| ⚠️ Partial | 4/10 (40%) | 2/10 (20%) | -20% |
| ❌ Wrong | 6/10 (60%) | 0/10 (0%) | **-60%** |

**The skills eliminated ALL wrong answers and converted 80% of tasks to fully correct.**

---

## Per-Task Comparison

### Task 1: Swap USDC for ETH on Base
**Question:** "I want to swap 1000 USDC for ETH on Base. Give me the exact contract address I should interact with, the function signature, and example calldata structure."

| | Baseline (❌) | With Skills (✅) |
|---|---|---|
| **DEX chosen** | Uniswap V3 (WRONG — not dominant on Base) | Aerodrome (CORRECT) |
| **Router address** | `0x2626664c2603336E57B271c5C0b26F421741e481` (this is actually the Uniswap Base SwapRouter02 — right addr for wrong protocol) | `0xcF77a3Ba9A5CA399B7c97c74d54e5b1Beb874E43` ✅ |
| **USDC address** | `0xd9fcd98c322942075a5c3860693e9f4f03aae07b` (WRONG) | `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` ✅ |
| **Function sig** | `exactInputSingle` (Uniswap V3) | `swapExactTokensForTokens` with Route struct ✅ |
| **Route struct** | N/A | Correct (from, to, stable, factory) ✅ |
| **Factory address** | N/A | `0x420DD381b31aEf6683db6B902084cB0FFECe40Da` ✅ |

**Verdict: ❌ → ✅** — Skill completely fixed the response. Every address correct. Correct protocol chosen.

---

### Task 2: Earn yield on USDC on Base
**Question:** "What's the best way to earn yield on USDC on Base? Give me specific protocol names, contract addresses, and current approach."

| | Baseline (⚠️) | With Skills (✅) |
|---|---|---|
| **Aave V3** | Mentioned but NO address | `0xA238Dd80C259a72e81d7e4664a9801593F98d1c5` ✅ |
| **Compound V3** | Not mentioned | `0xb125E6687d4313864e53df431d5425969c15Eb2F` ✅ |
| **Morpho Blue** | Not mentioned | `0xBBBBBbbBBb9cC5e90e3b3Af64bdAF62C37EEFFCb` ✅ |
| **Aerodrome LP** | Not mentioned | Mentioned with Router address ✅ |
| **Pendle PT** | Not mentioned | Mentioned for fixed yield ✅ |
| **veAERO insight** | N/A | Explained that voting often gives best yield ✅ |

**Verdict: ⚠️ → ✅** — Went from vague single-protocol mention to comprehensive answer with 5 protocols and all addresses.

---

### Task 3: Leveraged long ETH on Arbitrum
**Question:** "I want to open a leveraged long ETH position on Arbitrum. What protocol should I use and what's the contract address?"

| | Baseline (⚠️) | With Skills (✅) |
|---|---|---|
| **Protocol** | GMX (correct) | GMX V2 (correct, more specific) ✅ |
| **Address** | `0xfc5a1a6eb076a5f3a2cf6a019b752ea3e5c7b5a6` (GMX TOKEN, not router — WRONG) | `0x7C68C7866A64FA2160F78EeAe12217FFbf871fa8` (Exchange Router) ✅ |
| **GM pools** | Not explained | Not explicitly explained (concise answer) |

**Verdict: ⚠️ → ✅** — Fixed the critical error of giving the token address instead of the Exchange Router.

---

### Task 4: Fixed yield on stETH via Pendle on Arbitrum
**Question:** "How do I lock in a fixed yield on stETH using Pendle on Arbitrum? Give me the contract address and explain PT vs YT."

| | Baseline (⚠️) | With Skills (✅) |
|---|---|---|
| **Router address** | "Replace with actual address" (no address) | `0x888888888889758F76e7103c6CbF23ABbF58F946` ✅ |
| **PT explanation** | Partially correct | Correct — redeemable 1:1 at maturity, trades at discount = implied yield ✅ |
| **YT explanation** | Partially correct | Correct — all yield until maturity, decays to 0 ✅ |
| **SY concept** | Not mentioned | Explained SY wrapping ✅ |
| **Core invariant** | Not mentioned | `SY_value = PT_value + YT_value` ✅ |
| **Mechanism** | Said "deposit + sell YT" (roundabout) | Said "buy PT" = fixed yield (simpler, correct) ✅ |

**Verdict: ⚠️ → ✅** — From no address and partial understanding to complete answer with exact address and correct mechanics.

---

### Task 5: Is Celo an L1 or L2?
**Question:** "Is Celo an L1 or L2? How do I deploy a contract on Celo?"

| | Baseline (❌) | With Skills (✅) |
|---|---|---|
| **L1 vs L2** | "Celo is an L1" (WRONG) | "Celo is an OP Stack L2" ✅ |
| **Migration date** | Not mentioned | "March 26, 2025, block 31056500" ✅ |
| **Deployment** | Old Celo-specific tooling (ContractKit, CeloProvider) | "Change RPC URL and chain ID (42220), no code changes" ✅ |
| **Chain ID** | 44787 (testnet!) | 42220 ✅ |

**Verdict: ❌ → ✅** — Completely corrected. From dangerously wrong (L1 + old tooling) to accurate modern answer.

---

### Task 6: Should I deploy on Polygon zkEVM?
**Question:** "I want to build a DeFi app. Should I deploy on Polygon zkEVM?"

| | Baseline (❌) | With Skills (✅) |
|---|---|---|
| **Recommendation** | YES — enthusiastically recommends it (DANGEROUS) | "No, you should not" ✅ |
| **Shutdown info** | Not mentioned | "announced June 2025, being shut down" ✅ |
| **Alternative** | None | Recommends Arbitrum ($18B TVL, GMX, Pendle, Camelot) ✅ |

**Verdict: ❌ → ✅** — From dangerous misinformation to correct warning. This is a safety-critical fix.

---

### Task 7: Aerodrome fee model
**Question:** "How does Aerodrome on Base work? Who earns the trading fees — LPs or token holders?"

| | Baseline (❌) | With Skills (✅) |
|---|---|---|
| **Who earns fees** | "LPs earn trading fees" (WRONG) | "veAERO voters earn 100% of trading fees" ✅ |
| **What LPs earn** | "Trading fees" (wrong) | "AERO emissions" ✅ |
| **ve(3,3) model** | Not mentioned | Explained flywheel effect ✅ |
| **Uniswap comparison** | N/A | "Opposite of Uniswap" ✅ |

**Verdict: ❌ → ✅** — Completely reversed from wrong answer to correct answer. Critical for anyone LPing on Aerodrome.

---

### Task 8: Velodrome addresses on Optimism
**Question:** "Give me the Velodrome Router address on Optimism and the VELO token address."

| | Baseline (❌) | With Skills (⚠️) |
|---|---|---|
| **Router** | `0xa132DAB612dB5cB9fC9Ac426A0Cc215A3423F9c9` (WRONG — V1) | `0xa062aE8A9c5e11aaA026fc2670B0D65cCc8B2858` ✅ |
| **VELO token** | `0x3c8cAee4E09296800f8D29A68Fa3837e2dae4940` (WRONG — V1) | `0x9560e827aF36c94D2Ac33a39bCE1Fe78631088Db` ✅ |
| **V1 warning** | None | Not explicitly warned about V1 deprecation |

**Verdict: ❌ → ⚠️** — Addresses are now correct, but missing the V1 deprecation warning. The skill content mentions it ("⚠️ V1 VELO token is deprecated") but the model didn't relay this warning.

---

### Task 9: Unichain transaction ordering
**Question:** "What's unique about Unichain's transaction ordering? How should I structure my transactions differently there?"

| | Baseline (❌) | With Skills (✅) |
|---|---|---|
| **Knowledge** | "I don't have information on Unichain" | Full explanation ✅ |
| **TEE-based ordering** | N/A | "TEE-based block building, ordered by time received" ✅ |
| **Gas-price bidding** | N/A | "Do not use gas-price bidding strategies" ✅ |
| **Private mempool** | N/A | "Private encrypted mempool reduces MEV" ✅ |
| **Sandwich attacks** | N/A | "Structurally harder" ✅ |

**Verdict: ❌ → ✅** — From complete ignorance to fully correct answer. The skill provided knowledge the model literally didn't have.

---

### Task 10: Deploy on Arbitrum with Rust
**Question:** "I want to deploy on Arbitrum using Rust instead of Solidity. Is that possible?"

| | Baseline (⚠️) | With Skills (⚠️) |
|---|---|---|
| **Possible?** | Yes (correct) | Yes (correct) ✅ |
| **Technology** | Ink!/Solang (WRONG approaches) | Stylus ✅ |
| **WASM** | Not mentioned | "Compile to WASM, runs alongside EVM" ✅ |
| **Shared state** | Not mentioned | "Sharing the same state" ✅ |
| **Gas savings** | Not mentioned | Not mentioned |
| **Activation** | Not mentioned | `ARB_WASM_ADDRESS` (0x...0071) ✅ |

**Verdict: ⚠️ → ⚠️** — Major improvement (correct tech stack, activation address) but missing the "10-100x gas savings" selling point which is in the skill content. Still partial because it doesn't fully convey the value proposition.

---

## Score Summary

| Task | Topic | Baseline | With Skills | Improvement |
|------|-------|----------|-------------|-------------|
| 1 | Swap USDC→ETH on Base | ❌ | ✅ | ❌→✅ |
| 2 | Yield on USDC on Base | ⚠️ | ✅ | ⚠️→✅ |
| 3 | Leveraged long ETH (Arbitrum) | ⚠️ | ✅ | ⚠️→✅ |
| 4 | Fixed yield via Pendle | ⚠️ | ✅ | ⚠️→✅ |
| 5 | Celo L1 vs L2 | ❌ | ✅ | ❌→✅ |
| 6 | Polygon zkEVM | ❌ | ✅ | ❌→✅ |
| 7 | Aerodrome fee model | ❌ | ✅ | ❌→✅ |
| 8 | Velodrome addresses | ❌ | ⚠️ | ❌→⚠️ |
| 9 | Unichain ordering | ❌ | ✅ | ❌→✅ |
| 10 | Arbitrum Stylus/Rust | ⚠️ | ⚠️ | same (improved internally) |

### Aggregate Scores
- **Baseline:** 0 ✅ / 4 ⚠️ / 6 ❌ → Weighted: **4/30 points** (✅=3, ⚠️=1, ❌=0)
- **With Skills:** 8 ✅ / 2 ⚠️ / 0 ❌ → Weighted: **26/30 points** (✅=3, ⚠️=1, ❌=0)
- **Improvement: 4 → 26 (+550%)**

---

## Remaining Gaps (What Skills DON'T Fix)

### 1. V1 Deprecation Warnings Don't Always Surface (Task 8)
The skill content includes `⚠️ V1 VELO token is deprecated` but the model gave correct V2 addresses without relaying the warning. The warning content is there but the model chose not to include it in a concise answer. **Not a skill gap — content is correct.**

### 2. Gas Savings Numbers Not Always Relayed (Task 10)
The "10-100x gas savings" for Stylus is in the skill but didn't make it into the response. The model summarized accurately but dropped the quantitative claim. **Minor gap — consider making key selling points more prominent.**

### 3. No Skill Coverage for Hyperliquid
If someone asks about perps trading, the skills mention Hyperliquid as competition to GMX but don't have detailed coverage (addresses, API, etc). Worth a future skill.

### 4. Missing Pendle Market-Specific Addresses
The Pendle Router address is covered, but specific market addresses (e.g., the stETH-PT market on Arbitrum) are not in the skills. The Router is sufficient for integration but market discovery requires additional lookups.

### 5. No AgentKit / x402 Implementation Details
The skills mention these exist but don't have implementation code or addresses. Relevant for AI agent use cases.

---

## Recommendations for Further Skill Improvements

1. **Add a "CRITICAL WARNINGS" section** at the top of each skill with the most dangerous failure modes (wrong addresses, deprecated protocols, shutdown chains). Models tend to surface content from the top of context.

2. **Add Hyperliquid skill** — it's the largest perps venue and the skills currently only mention it in passing.

3. **Add Pendle market discovery** — how to find specific PT/YT markets and their addresses via the Pendle API.

4. **Strengthen the "not Uniswap" message for L2 DEXes** — the baseline defaulted to Uniswap on every chain. The skill fixes this but could be even more emphatic.

5. **Add implementation examples for AgentKit/x402** — especially for the AI agent use case on Base.

6. **Consider a "Common LLM Mistakes" section** that explicitly lists what models get wrong (e.g., "Celo is NOT an L1", "Polygon zkEVM is DEAD", "LPs do NOT earn fees on Aerodrome"). This adversarial framing helps models avoid their most common hallucinations.

---

## Conclusion

The ethskills content from PRs #41 and #42 is **highly effective**. It eliminated all 6 wrong answers, upgraded 6 tasks to fully correct, and provided knowledge the model literally did not have (Unichain, Celo migration, Polygon zkEVM shutdown). 

The most impactful improvements were **safety-critical**: preventing users from deploying on a dead chain (Polygon zkEVM), giving correct contract addresses instead of hallucinated ones, and correctly explaining fee models that would affect LP strategy decisions.

**Bottom line:** These skills turn a model that would actively mislead users about Ethereum DeFi into one that gives specific, correct, actionable answers with verified addresses.
