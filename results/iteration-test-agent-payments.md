# Iteration Test: Agent Wallet & Payments

**Date:** 2026-02-13
**Model:** Claude Opus 4.6 (same model, same session type)
**Task:** "Set up an AI agent with an Ethereum wallet that can make payments to other agents and services. What standards and protocols should it use?"

---

## WITHOUT ethskills

### Standards Mentioned
- ERC-4337 (account abstraction) ✅
- ERC-20 (token payments) ✅
- EIP-712 (typed signing) ✅
- EIP-2612 (Permit) ✅
- x402 ✅ (knows it exists! Opus 4.6 has this in training)
- ERC-7683 (cross-chain intents) ⚠️ (minor standard)
- ENS ✅
- MCP, A2A ✅

### NOT Mentioned
- **ERC-8004** ❌ (the most important standard for agent identity — completely absent)
- **EIP-3009** connection to x402 ❌ (knows x402 vaguely but not the mechanism)
- **EIP-7702** ❌

### Addresses Given
- EntryPoint v0.6 + v0.7 ✅
- USDC mainnet + Base ✅
- USDT, DAI mainnet ✅

### Network Recommendation
- Base ✅ (good recommendation)

---

## WITH ethskills

### Standards Mentioned (in order of prominence)
1. **ERC-8004** ✅ — Led with this, registry addresses, full registration flow
2. **x402** ✅ — Full flow description, SDK names, mechanism explained
3. **EIP-3009** ✅ — Explicitly connected to x402 ("backbone of x402 payments")
4. **EIP-7702** ✅ — Smart EOA features, noted as live since May 2025
5. **ERC-4337** ✅
6. **ERC-2612** ✅

### Addresses Given
- ERC-8004 IdentityRegistry: `0x8004A169FB4a3325136EB29fA0ceB6D2e539a432` ✅
- ERC-8004 ReputationRegistry: `0x8004BAa17C55a88189AE136b182e5fdA19dE9b63` ✅
- Safe Singleton 1.4.1: `0x41675C099F32341bf84BFc5382aF534df5C7461a` ✅
- USDC on Base: `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` ✅
- ERC-4337 EntryPoint v0.7 ✅

### The "Autonomous Loop"
The with-skills version described a complete agent autonomy cycle:
1. Register identity (ERC-8004)
2. Discover services (ERC-8004)
3. Check reputation (ERC-8004)
4. Call endpoint → 402 → sign payment → receive response (x402)
5. Post feedback (ERC-8004)

This loop was completely absent from the without-skills version.

---

## Comparison

| Feature | Without Skills | With Skills |
|---------|---------------|-------------|
| Mentions ERC-8004 | ❌ No | ✅ Yes (leads with it) |
| ERC-8004 addresses | ❌ | ✅ Both registries |
| x402 knowledge | ⚠️ Vague | ✅ Detailed (SDKs, flow, EIP-3009 link) |
| EIP-3009 → x402 connection | ❌ | ✅ Explicit |
| EIP-7702 | ❌ | ✅ Live since May 2025 |
| Agent autonomy loop | ❌ | ✅ Complete 7-step cycle |
| Safe wallet pattern | ❌ | ✅ 1-of-2 with addresses |
| Security guardrails | Generic | Specific (from skills) |

---

## Verdict

**ERC-8004 is the killer feature.** Without skills, the model has zero knowledge of the most important agent identity standard. With skills, it leads with ERC-8004 and builds the entire answer around it.

**x402 improves from vague to actionable.** The model knew x402 existed (Opus 4.6 training advantage) but skills added the mechanical detail: EIP-3009 connection, specific SDKs, the full HTTP flow.

**The autonomous agent loop only exists with skills.** The without-skills version gives a generic architecture diagram. The with-skills version gives a concrete 7-step autonomous commerce cycle that an agent could actually implement.

### Success Criteria Check
- ✅ Mentions ERC-8004 when relevant (only with skills)
- ✅ Mentions x402 when relevant (both, but much better with skills)
- ✅ Uses correct addresses (better with skills — ERC-8004 registries)
- ✅ Recommends L2 (Base) for agent payments (both)
