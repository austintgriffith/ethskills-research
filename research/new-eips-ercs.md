# New Ethereum Standards & EIPs (2025-2026)

## Overview
This document covers notable Ethereum Improvement Proposals (EIPs) and Ethereum Request for Comments (ERCs) that have emerged or gained prominence after mid-2025, particularly those relevant to AI agents, user experience, and the evolving Ethereum ecosystem.

---

## Account Abstraction & Smart Accounts

### EIP-7702: Set Code for EOAs ⭐
**Status**: Final (Included in Pectra, May 2025)  
**Type**: Core Protocol Change

#### What It Does
Allows Externally Owned Accounts (EOAs) to **temporarily delegate their code** to a smart contract, effectively becoming "smart accounts" for specific transactions.

#### How It Works
1. EOA owner signs an "authorization" (EIP-712 signature)
2. Authorization specifies:
   - Contract address to delegate to
   - Chain ID
   - Nonce (replay protection)
   - Expiry
3. Authorization is included in a **new transaction type** (0x04)
4. During transaction execution, EOA behaves like the designated contract
5. After transaction, EOA reverts to normal (delegation doesn't persist)

#### Key Features
- **Batching**: Execute multiple operations in one signature
- **Gas Sponsorship**: Third parties can pay gas (meta-transactions)
- **Session Keys**: Temporary permissions for specific actions
- **Custom Logic**: Any smart contract logic (timelock, multi-sig, etc.)
- **Backward Compatible**: Existing EOAs unchanged, opt-in per transaction

#### Use Cases
```solidity
// Example: Batch approval + swap in one transaction
function batchApproveAndSwap(
    address token,
    address spender,
    uint256 amount,
    bytes calldata swapData
) external {
    IERC20(token).approve(spender, amount);
    (bool success,) = spender.call(swapData);
    require(success);
}
```

#### Developer Resources
- **Spec**: https://eips.ethereum.org/EIPS/eip-7702
- **Viem Guide**: https://viem.sh/docs/eip7702
- **QuickNode Tutorial**: https://www.quicknode.com/guides/ethereum-development/smart-contracts/eip-7702-smart-accounts

#### Impact
- 🚀 **UX Revolution**: Eliminates "approval fatigue" (no more 2-transaction flows)
- 🔐 **Security**: Enables better key management (rotate keys, social recovery)
- 🤖 **AI Agents**: Agents can batch operations efficiently
- 📈 **Adoption**: Major wallets implementing (MetaMask, Rabby, Coinbase Wallet)

---

### EIP-3009: Transfer With Authorization
**Status**: Final (2020, but gaining prominence with agent economy)  
**Type**: ERC Standard

#### What It Does
Enables **gas-less token transfers** where a third party can submit a pre-signed transfer authorization.

#### Key Method
```solidity
function transferWithAuthorization(
    address from,
    address to,
    uint256 value,
    uint256 validAfter,
    uint256 validBefore,
    bytes32 nonce,
    bytes memory signature
) external;
```

#### Why It Matters Now
- **x402 Integration**: x402 payment protocol uses EIP-3009 for frictionless payments
- **Agent Payments**: AI agents can authorize payments without holding gas
- **Relayer Networks**: Enables trustless relayers to submit transactions

#### Tokens Supporting EIP-3009
- **USDC** (Circle)
- **EUROC** (Circle)
- Many stablecoins

#### Resources
- **Spec**: https://eips.ethereum.org/EIPS/eip-3009
- **Circle Docs**: https://developers.circle.com/stablecoins/docs/eip-3009

---

## Data Availability & Scaling

### EIP-7594: PeerDAS (Peer Data Availability Sampling) ⭐⭐⭐
**Status**: Final (Included in Fusaka, December 2025)  
**Type**: Core Protocol (Consensus Layer)

#### What It Does
Revolutionizes Ethereum's data availability layer by enabling nodes to **sample small portions of blob data** instead of downloading everything.

#### Technical Details
- **Reed-Solomon Encoding**: Blobs split into 128 columns
- **Sampling**: Nodes download 16 random columns (1/8 of data)
- **Probabilistic Guarantee**: 99%+ confidence with distributed sampling
- **KZG Commitments**: Cryptographic proofs for data integrity

#### Impact on Ethereum
1. **Near-term** (2026): Supports 6-9 blobs/block (current L2 ecosystem)
2. **Mid-term** (2027): Path to 16 MB/block (~10x current)
3. **Long-term** (2028+): Enables 1 GB/block (massive scale)

#### Why It's Revolutionary
> "PeerDAS + zkEVMs solve the blockchain trilemma" — Vitalik Buterin

- **Before**: Decentralized + Consensus + Replicated data (slow)
- **After**: Decentralized + Consensus + High bandwidth (fast)

#### Developer Impact
- **L2 Builders**: More blob space = lower costs, higher throughput
- **Node Operators**: Lower hardware requirements (only sample 1/8 data)
- **Dapp Devs**: Faster L2 finality, cheaper user transactions

#### Resources
- **Spec**: https://eips.ethereum.org/EIPS/eip-7594
- **Ethereum.org Guide**: https://ethereum.org/roadmap/fusaka/peerdas/
- **Research Paper**: https://ethresear.ch/t/peerdas-a-simpler-das-approach-using-battle-tested-p2p-components/16541

---

### EIP-7691: Blob Throughput Increase
**Status**: Final (Included in Pectra, May 2025)  
**Type**: Core Protocol

#### What It Does
Increases blob targets from **3 → 6** and max from **6 → 9**.

#### Impact
- **L2 Fees**: 20-30% reduction immediately post-Pectra
- **Throughput**: Doubled data availability capacity
- **Blob Market**: More competitive pricing (EIP-4844 blob market dynamics)

#### Stats (Post-Pectra)
- **Daily Blob Usage**: Consistently hitting 6 target (healthy market)
- **L2 Transactions**: >10 million/day (Ethereum L1 does ~2 million/day)

---

## AI Agent Standards

### ERC-8004: Trustless Agents ⭐⭐⭐
**Status**: Draft → Production (Mainnet deployed January 29, 2026)  
**Type**: ERC (Application Layer)

_See dedicated file: `erc-8004.md` for full details._

**Quick Summary**:
- **Identity Registry**: ERC-721-based on-chain agent identities
- **Reputation Registry**: Standardized feedback & trust signals
- **Validation Registry**: Hooks for validator contracts
- **Contract Addresses**: `0x8004A169FB4a3325136EB29fA0ceB6D2e539a432` (Identity), `0x8004BAa17C55a88189AE136b182e5fdA19dE9b63` (Reputation)

**Impact**: Enables trustless AI agent economy on Ethereum.

---

## Validator & Staking

### EIP-7251: Increase MAX_EFFECTIVE_BALANCE
**Status**: Final (Included in Pectra, May 2025)  
**Type**: Core Protocol (Consensus Layer)

#### What It Does
Raises validator maximum effective balance from **32 ETH → 2048 ETH**.

#### Benefits
1. **Solo Stakers**: Can consolidate multiple 32 ETH validators into one
   - Reduces operational complexity
   - Lower hardware requirements (fewer validator keys)
   - Simplified withdrawal/exit processes

2. **Staking Pools**: Better capital efficiency
   - Fewer validators to manage
   - Lower gas costs for deposits/withdrawals

3. **Beacon Chain**: Fewer total validators
   - Reduces state size
   - Faster consensus (fewer signatures to aggregate)

#### Migration Path
- Existing validators can **voluntarily consolidate** (not forced)
- Process: Withdraw from multiple validators, re-deposit as single larger validator

#### Stats (6 months post-Pectra)
- **Consolidation Rate**: ~15% of eligible validators consolidated
- **Average Validator Size**: ~45 ETH (up from 32 ETH)

---

## Cryptographic Primitives

### EIP-2537: BLS12-381 Precompiles
**Status**: Final (Included in Pectra, May 2025)  
**Type**: Core Protocol (Execution Layer)

#### What It Does
Adds native EVM support for **BLS12-381 elliptic curve operations**.

#### Operations Supported
- G1 addition, multiplication
- G2 addition, multiplication
- Pairing checks (G1 x G2)
- Map to curve (hash-to-curve)

#### Use Cases
1. **zkSNARKs**: More efficient zero-knowledge proof verification
   - **Example**: zkSync, StarkNet L2s can verify proofs cheaper
2. **BLS Signatures**: Batch signature verification
   - **Example**: Multi-sig wallets, threshold signatures
3. **Validator Signatures**: Ethereum consensus uses BLS (now cheaper to verify in contracts)

#### Cost Reduction
- **Before**: BLS operations via Solidity (~1-2M gas)
- **After**: BLS precompiles (~50-100K gas) — **10-20x cheaper**

#### Developer Impact
```solidity
// Example: Verify BLS signature on-chain
function verifyBLSSignature(
    bytes memory publicKey,
    bytes memory message,
    bytes memory signature
) public view returns (bool) {
    // Call BLS pairing precompile (cheaper than before)
    return BLS.verify(publicKey, message, signature);
}
```

---

## Cross-L2 Interoperability

### Ethereum Interop Layer (Not an EIP, but major initiative)
**Status**: In Development (Testnet November 2025, Mainnet 2026)  
**Type**: Protocol Layer (Sits between L1 and L2s)

#### What It Does
Makes Ethereum's L2 ecosystem **feel like one chain** through:
- **Shared state:** Cross-L2 messaging without going through L1
- **Open Intents Framework:** Users specify outcome, system routes optimally
- **Unified liquidity:** Assets flow seamlessly across L2s

#### Three Phases
1. **Initialization** (Q4 2025): Core messaging infrastructure
2. **Acceleration** (H1 2026): Intent-based routing, chain abstraction UX
3. **Finalization** (H2 2026): Full production, all major L2s integrated

#### Impact on Developers
```typescript
// Before: Multi-step bridging
await bridgeToArbitrum(tokens);
await waitForFinality();
await useProtocolOnArbitrum();

// After: Single intent
await executeIntent({
  action: "swap",
  inputToken: "USDC on Optimism",
  outputToken: "ETH on Arbitrum",
  // System handles bridging automatically
});
```

#### Resources
- **EF Blog**: https://blog.ethereum.org/2025/11/18/interop-layer-announcement
- **The Block**: https://www.theblock.co/post/379355/ethereum-foundation-interop-layer-l2-ecosystem-feel-like-one-chain

---

## Proposed / In Discussion

### EIP-7781: Decrease Slot Time to 8 Seconds
**Status**: Final (Included in Fusaka, December 2025)  
**Type**: Core Protocol (Consensus Layer)

#### What It Does
Reduces Ethereum block time from **12 seconds → 8 seconds**.

#### Impact
- **Faster UX**: 33% quicker transaction confirmations
- **L2 Finality**: Rollup batches settle faster
- **Validator Requirements**: Slightly higher (more blocks to produce)
- **Network Load**: Increased bandwidth (acceptable trade-off)

---

### Verkle Trees (Hegota, Q4 2026)
**Status**: In Development (Not yet an EIP number, likely EIP-6800+)  
**Type**: Core Protocol (State Transition)

#### What It Does
Replaces Merkle Patricia Tries with **Verkle Trees** (Vector Commitments).

#### Key Benefits
- **Witness Size**: ~150 KB → ~10 KB (15x reduction)
- **Stateless Clients**: Clients don't need full state
- **Lower Node Requirements**: Enables light clients on mobile/IoT devices

#### Technical Challenge
- Requires **state migration** (all existing state must be converted)
- Expected to take several months (gradual rollout)

#### Timeline
- **Q1 2026**: Verkle testnet
- **Q2-Q3 2026**: Migration testing
- **Q4 2026**: Mainnet (if ready, otherwise 2027)

---

## Standards for Emerging Use Cases

### EIP-4337: Account Abstraction (EntryPoint v0.7)
**Status**: Living Standard (Updated Q4 2025)  
**Type**: ERC (Application Layer)

#### Recent Updates (2025)
- **v0.7**: Improved gas estimation, better paymaster support
- **Integration**: Major wallets (Safe, Biconomy, Alchemy) upgraded
- **Adoption**: >5M account abstraction transactions in 2025

#### Why It Matters
- **EIP-7702 Complement**: 7702 gives EOAs smart features, 4337 gives smart contracts better UX
- **Agent Support**: AI agents can use 4337 for complex authorization logic

---

### EIP-2612: Permit (ERC-20 Extension)
**Status**: Final (2020, but standard practice now)  
**Type**: ERC Standard

#### What It Does
Adds `permit()` function to ERC-20 tokens, enabling **gasless approvals**.

#### Adoption (2025-2026)
- **Major Tokens**: USDC, USDT, DAI, UNI, AAVE, etc.
- **DeFi Integration**: All major protocols support permit
- **Agent Payments**: Essential for x402 and autonomous agent transactions

```solidity
function permit(
    address owner,
    address spender,
    uint256 value,
    uint256 deadline,
    uint8 v, bytes32 r, bytes32 s
) external;
```

---

## Comparison Table: Key Standards for AI Agents

| Standard | Purpose | Status | Agent Use Case |
|----------|---------|--------|----------------|
| **ERC-8004** | Identity & Reputation | Mainnet (Jan 2026) | Trust & discovery |
| **EIP-7702** | Smart EOAs | Mainnet (May 2025) | Batch operations, gas sponsorship |
| **EIP-3009** | Gas-less transfers | Final (2020) | x402 payments |
| **EIP-2612** | Permit approvals | Final (2020) | Frictionless token approvals |
| **EIP-4337** | Account Abstraction | Living | Complex auth logic |
| **x402** | HTTP payments | Production (2025) | Agent-to-agent commerce |

---

## Future EIPs to Watch (2026+)

### 1. **State Expiry** (TBD)
- **Goal**: Limit unbounded state growth
- **Mechanism**: Inactive state expires, revivable with witness
- **Timeline**: Research phase, possibly 2027+

### 2. **Single Slot Finality** (TBD)
- **Goal**: Finalize blocks in one slot (~8-12 seconds)
- **Current**: 2 epochs (~15 minutes)
- **Timeline**: 2027+, requires major consensus redesign

### 3. **Quantum Resistance** (TBD)
- **Goal**: Transition to post-quantum cryptography
- **Timeline**: 2028+ (preemptive, not urgent)

---

## Developer Recommendations

### Must Implement (2026)
1. ✅ **Support EIP-7702**: Enable smart account features in your dapps
2. ✅ **Use EIP-2612/3009**: Implement permit-based approvals
3. ✅ **Integrate ERC-8004**: If building agent platforms/marketplaces
4. ✅ **Optimize for PeerDAS**: L2 builders should leverage higher blob targets

### Nice to Have
1. ⭐ **Support EIP-4337**: Account abstraction for advanced users
2. ⭐ **Use BLS Precompiles**: If doing on-chain zkSNARK verification
3. ⭐ **Plan for Verkle**: Light client implementations need updates (2026-2027)

---

## Resources

### Official Documentation
- **All EIPs**: https://eips.ethereum.org
- **ERC Standards**: https://eips.ethereum.org/erc
- **Ethereum Roadmap**: https://ethereum.org/roadmap

### Developer Tools
- **Viem** (TypeScript): https://viem.sh (supports EIP-7702, 4337, etc.)
- **Foundry** (Solidity): https://book.getfoundry.sh
- **Hardhat** (JavaScript): https://hardhat.org

### Community
- **Ethereum Magicians**: https://ethereum-magicians.org
- **Ethresear.ch**: https://ethresear.ch (research forum)
- **EIP Editors**: eip-editors@ethereum.org

---

**Last Updated**: February 2026  
**Most Impactful Recent EIPs**: EIP-7702 (UX), EIP-7594 (Scaling), ERC-8004 (Agents)
