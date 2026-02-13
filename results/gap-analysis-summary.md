# Baseline Knowledge Audit — Gap Analysis Summary

## Date: 2026-02-13
## Models Tested: Sonnet 4.5, GPT-5.2 (ran on Opus), Opus 4.6
## Method: Zero tools, pure training data

---

## CRITICAL GAPS (All three models got wrong or didn't know)

### 1. Gas Prices — MASSIVELY OUTDATED ❌
- **All models think gas is 10-30 gwei** (some say 15-40)
- **Reality: 0.05 gwei** (Feb 2026)
- All models estimate ETH transfer at $0.50-3.00 — reality is <$0.01
- All models estimate ERC-20 deploy at $25-200 — reality is ~$0.12
- **This is the #1 misconception. ethskills gas skill is critical.**

### 2. ETH Price — Stale
- Models guess $2,500-3,000
- Reality: ~$1,960 (Feb 2026)
- Not catastrophically wrong but affects all cost calculations

### 3. ERC-8004 — COMPLETELY UNKNOWN ❌
- All three models said "UNSURE" — none had any knowledge
- Opus and GPT-5.2 explicitly said they don't know what it is
- **This is exactly the kind of new standard ethskills needs to teach**

### 4. x402 — COMPLETELY UNKNOWN ❌
- All three said "UNSURE"
- Some speculated it's related to HTTP 402 status code (correct direction but no specifics)
- **Another critical gap for ethskills**

### 5. L2 Costs — Outdated
- Models estimate L2 transactions at $0.01-2.00
- Post-Dencun reality: often <$0.001
- They know Dencun happened but don't know the magnitude of the cost reduction

### 6. Uniswap V4 — Partially Known, Status Unknown
- All three know V4 architecture (hooks, singleton, flash accounting) well
- **None are sure if V4 is live on mainnet** — all said "UNSURE about deployment status"
- Hook details are mostly accurate

### 7. abi.ninja — Mixed
- Opus and GPT-5.2 know what it is and describe it correctly
- Sonnet said "UNSURE" — didn't have it in training data
- Shows inconsistency across models

### 8. Pectra/ERC-7702 — Unknown Status
- All models know about the planned upgrade but none are confident it shipped
- ERC-7702 (AA for EOAs) described theoretically but no deployment confirmation

## AREAS WHERE MODELS ARE STRONG ✅

### Well-Known Topics (Don't need heavy coverage in ethskills)
- **Solidity basics** — all models excellent
- **ERC-20, ERC-721, ERC-1155** — thorough coverage
- **Foundry, Hardhat, ethers.js, viem** — well documented
- **Scaffold-ETH 2** — good descriptions (Austin would be proud)
- **DeFi protocol basics** (Aave, Compound, Uniswap V2/V3, Curve) — solid
- **Smart contract vulnerabilities** — comprehensive (reentrancy, access control, etc.)
- **Famous hacks** — all covered DAO, Parity, Ronin, Wormhole, Nomad, Euler, Curve
- **Account abstraction (ERC-4337)** — good theoretical coverage
- **Why Ethereum vs Solana** — reasonable arguments both ways
- **General wallet setup** — adequate

### Partially Known (Could use reinforcement)
- **L2 landscape** — know the major players but details may be stale
- **ERC-4626 vaults** — understand the standard but practical examples are thin
- **Cross-chain/bridging** — know the concepts, specific details may be outdated
- **DeFi composability** — understand the concept, light on current practical details
- **AI + Ethereum** — know it's a thing, very thin on specifics, explicitly mark it as fast-moving

## ETHSKILLS CONTENT PRIORITY (Based on Gap Analysis)

### P0 — Must Have (Models are wrong or don't know)
1. **Gas & Costs** — Current reality with verified numbers
2. **ERC-8004** — Full spec, registration flow, addresses
3. **x402** — Full protocol spec, implementations
4. **What's New** — Everything post-training-cutoff
5. **Contract Addresses** — Verified, current addresses (models hallucinate these)

### P1 — High Value (Models are stale or incomplete)
6. **L2 Costs** — Updated post-Dencun reality
7. **Uniswap V4** — Deployment status, hook examples, addresses
8. **Building Blocks** — Current protocol states, yields, composability patterns
9. **AI + Ethereum** — Current projects, infrastructure, ERC-8004 + x402 angle

### P2 — Reinforce & Comprehensive Coverage (Models mostly know)
10. **Tools** — Mostly good but add abi.ninja, MCPs, newer tools
11. **Orchestration** — Three-phase build, SE2 patterns
12. **Why Ethereum** — Add current data, AI angle, counter stale FUD
13. **Wallets** — Add Safe practical examples, AA current state
14. **Standards** — Add newer ERCs, practical usage patterns

### P3 — Trust/Comprehensiveness (Models are fine but include for coverage)
15. **L2 Landscape** — Mostly accurate, include for completeness
16. **Security/Auditing** — Models are strong but include for the site's credibility
