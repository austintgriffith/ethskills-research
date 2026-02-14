# Iteration Test: Does ethskills improve LLM output?

**Date:** 2026-02-13
**Model:** Claude Opus 4.6 (same model, same session type)
**Task:** "Deploy an ERC-20 token to Ethereum mainnet and estimate the cost. What addresses would you use for Uniswap if you wanted to add liquidity?"

---

## WITHOUT ethskills (pure training data)

### Cost Estimate
- Gas: 1,200,000-1,500,000 (✅ correct range)
- Gas price assumed: **20-50 gwei** ❌ (reality: 0.05 gwei)
- USD estimate: **$50-200** ❌ (reality: ~$0.12)
- **400-1600x too expensive**

### Uniswap Addresses
| Contract | Given | Correct? |
|----------|-------|----------|
| V2 Router | `0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D` | ✅ |
| V2 Factory | `0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f` | ✅ |
| V3 SwapRouter | `0xE592427A0AEce92De3Edee1F18E0157C05861564` | ✅ |
| V3 SwapRouter02 | `0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45` | ✅ |
| V3 Factory | `0x1F98431c8aD98523631AE4a59f267346ea31F984` | ✅ |
| V3 Position Manager | `0xC36442b4a4522E871399CD717aBDD847Ab11FE88` | ✅ |
| WETH | `0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2` | ✅ |

**Addresses: 7/7 correct** — model already knows these well-known addresses.

---

## WITH ethskills loaded

### Cost Estimate
- Gas: ~1,200,000 (✅ correct)
- Gas price used: **0.05 gwei** ✅ (from skills)
- ETH price used: **$1,960** ✅ (from skills)
- USD estimate: **~$0.118** ✅ (correct!)
- Also gave spike estimates: $2.35 at 1 gwei, $23.52 at 10 gwei ✅
- Practical fee settings: maxFeePerGas 0.5-1 gwei, priority 0.01-0.05 ✅
- Total flow cost (deploy + pool + liquidity): **under $1.00** ✅

### Uniswap Addresses
Same addresses as without skills — all 7/7 correct. Also added:
- Quoter V2: `0x61fFE014bA17989E743c5F6cB21bF9697530B21e` ✅
- Universal Router: `0x3fC91A3afd70395Cd496C647d5a6CC9D4B2b7FAD` ✅

---

## Comparison

| Metric | Without Skills | With Skills | Improvement |
|--------|---------------|-------------|-------------|
| Deploy cost estimate | $50-200 | $0.12 | **417-1667x more accurate** |
| Gas price used | 20-50 gwei | 0.05 gwei | ✅ Correct |
| ETH price used | Not specified | $1,960 | ✅ Current |
| Fee settings | Not provided | maxFee 0.5-1 gwei | ✅ Practical |
| Uniswap addresses | 7/7 correct | 9/9 correct | +2 addresses |
| Total flow cost | Not calculated | < $1.00 | ✅ Realistic |
| Mentioned L2 alternative | No | No* | — |

*Neither version suggested deploying on L2 instead — this could be improved in the orchestration skill.

---

## Verdict

**Cost estimates improved by 400-1600x.** The model went from quoting $50-200 (which would scare off builders) to $0.12 (which is the actual reality). This is the single most important correction ethskills provides.

**Addresses were already correct** for well-known contracts (Uniswap V2/V3). Skills added 2 more useful addresses (Quoter V2, Universal Router) but the model's training data already had the core ones right.

**Practical guidance improved dramatically:** Fee settings, spike detection, total flow costing — none of this appeared without skills.

### Success Criteria Check
- ✅ Cost estimates within 10x of reality (was 100x+ off, now exact)
- ✅ Contract addresses correct (already were, but skills added more)
- ❌ Did not mention ERC-8004/x402 (not relevant to this task)
- ❌ Did not recommend L2 for cost-sensitive deployment (could improve)
