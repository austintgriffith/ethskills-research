# Ethereum Protocol Upgrades 2025-2026

## Overview
Ethereum has transitioned to a **twice-per-year upgrade cadence**, enabling faster iteration while maintaining network stability. This document covers the major upgrades from 2025-2026 and their impact on scalability, user experience, and the L2 ecosystem.

---

## Pectra (May 7, 2025) ✅ COMPLETED

**Full Name**: Prague-Electra  
**Activation**: Epoch 364,032 | May 7, 2025 at 10:05:11 AM UTC  
**Status**: Successfully activated on Ethereum mainnet

### Overview
Pectra is Ethereum's **most ambitious hard fork since The Merge**, combining execution layer (Prague) and consensus layer (Electra) improvements. It focuses on **user experience** and **validator operations**.

### Key EIPs Included

#### 1. **EIP-7702: Set Code for EOAs** (Smart Accounts)
- **Most significant UX upgrade**: Allows Externally Owned Accounts (EOAs) to temporarily delegate execution to smart contracts
- **Enables**:
  - Batch transactions (multiple operations in one signature)
  - Gas sponsorship (meta-transactions)
  - Session keys (temporary permissions)
  - Custom authorization logic
- **Impact**: Eliminates "approval fatigue" and brings account abstraction benefits to existing wallets
- **Backward compatible**: EOAs remain EOAs, just gain superpowers when needed

**Developer Note**: EIP-7702 is a stepping stone toward native account abstraction, making it easier to build user-friendly dapps without forcing users to migrate wallets.

#### 2. **EIP-7251: Increase MAX_EFFECTIVE_BALANCE**
- Raises validator max effective balance from **32 ETH to 2048 ETH**
- **Benefits**:
  - Solo stakers can consolidate validators (reduce operational overhead)
  - Fewer beacon chain entries (better consensus efficiency)
  - Staking pools can optimize capital allocation
- **Impact**: Makes solo staking more practical for large holders

#### 3. **EIP-7691: Blob Throughput Increase**
- Increases blob capacity from **3 target / 6 max** to **6 target / 9 max**
- **L2 Impact**: **Doubles data availability capacity** for rollups
- **Cost Reduction**: Lower fees for L2 transactions (more blob competition)
- **Throughput**: Ethereum L2s can now process **>50,000 TPS collectively**

#### 4. **EIP-2537: BLS12-381 Precompiles**
- Adds native support for BLS signature operations
- **Enables**: More efficient zkSNARK verification
- **Impact**: Cheaper on-chain cryptography for privacy and zkRollups

#### 5. **EIP-2935: Serve Historical Block Hashes**
- Improves access to historical block data
- **Benefits**: Better light client support, easier state proofs

### Impact Summary
- **User Experience**: 📈📈📈 (EIP-7702 is game-changing)
- **L2 Scalability**: 📈📈 (Doubled blob capacity)
- **Validator Operations**: 📈📈 (Consolidation, better staking)
- **Developer Experience**: 📈📈 (Precompiles, smart accounts)

### Resources
- **EIP Spec**: https://ethereum.org/roadmap/pectra/
- **Galaxy Research**: https://www.galaxy.com/newsroom/navigating-ethereums-pectra-upgrade-what-to-know-and-how-galaxy-is-preparing
- **Coin Metrics Analysis**: https://coinmetrics.io/company-news/ethereums-pectra-upgrade-key-changes-metrics-impacted/

---

## Fusaka (December 3, 2025) ✅ COMPLETED

**Full Name**: Fulu-Osaka  
**Activation**: Slot 13,164,544 | December 3, 2025 at 21:49:11 UTC  
**Status**: Successfully activated on Ethereum mainnet

### Overview
Fusaka is the **biggest throughput-focused upgrade since EIP-4844** (blobs in Dencun). It introduces **PeerDAS** (data availability sampling) and faster block times, setting the stage for massive L2 scaling.

### Key EIPs Included

#### 1. **EIP-7594: PeerDAS (Peer Data Availability Sampling)** 🌟
**The most important EIP in Fusaka**—fundamentally changes how Ethereum handles data availability.

**What it does:**
- Nodes only need to sample **1/8 of blob data** (instead of downloading everything)
- Uses **Reed-Solomon encoding** + **KZG commitments** for data recovery
- Enables horizontal scaling of data availability

**How it works:**
1. Blobs are split into **128 columns**
2. Each node samples **16 random columns** (1/8 of data)
3. Probabilistic guarantee: If >50% is available, full data is recoverable
4. **Network effect**: More nodes = higher data availability confidence

**Impact:**
- **Short-term**: Supports current L2 ecosystem until further scaling
- **Medium-term** (2026): Path to **16 MB/block** (~10x current capacity)
- **Long-term**: Enables **1 GB/block** data availability (far future)
- **Decentralization**: Lower hardware requirements for full nodes

**Why it matters:**
> "PeerDAS is probably the biggest upgrade since The Merge" — Ethereum researchers

Combined with zkEVMs (2026), this shifts Ethereum from:
- ❌ **Decentralized + Consensus + Replicated data** (slow)
- ✅ **Decentralized + Consensus + High bandwidth** (fast)

#### 2. **EIP-7781: Faster Slots (Decrease Slot Time)**
- Reduces slot time from **12 seconds → 8 seconds**
- **Impact**: 50% faster block confirmations
- **L2 Benefit**: Faster finality for rollup batches
- **Trade-off**: Slightly higher validator requirements (acceptable)

#### 3. **EIP-7742: Uncouple Blob Count Between CL/EL**
- Allows dynamic adjustment of blob limits without consensus layer changes
- **Flexibility**: Can quickly respond to network conditions

#### 4. **Blob Throughput Increases**
- Further optimizations to blob gas mechanics
- **Target**: Smoother blob pricing, better L2 predictability

### Network Stats Post-Fusaka
- **L2 Transaction Volume**: >10 million/day (L1 does ~2 million/day)
- **Blob Utilization**: Consistently hitting capacity pre-Fusaka, healthier post
- **L2 Fee Reduction**: ~20-30% average cost decrease for rollup users

### Impact Summary
- **L2 Scalability**: 📈📈📈📈 (PeerDAS is revolutionary)
- **Block Times**: 📈📈 (33% faster confirmations)
- **Data Availability**: 📈📈📈 (Sampling enables massive scale)
- **Decentralization**: 📈 (Lower node requirements)

### Resources
- **EIP Spec**: https://ethereum.org/roadmap/fusaka/
- **EF Announcement**: https://blog.ethereum.org/2025/11/06/fusaka-mainnet-announcement
- **PeerDAS Deep-Dive**: https://ethereum.org/roadmap/fusaka/peerdas/
- **Fidelity Analysis**: https://www.fidelitydigitalassets.com/research-and-insights/fusaka-upgrade-scaling-meets-value-accrual

---

## Glamsterdam (Planned: Q2 2026)

**Full Name**: TBD (Execution + Consensus layer names)  
**Expected**: First half of 2026 (likely May/June)  
**Status**: In development, EIPs being finalized

### Overview
Glamsterdam continues Ethereum's scaling and UX roadmap, with a focus on **MEV mitigation**, **validator improvements**, and **cross-L2 interoperability**.

### Proposed EIPs (Tentative)

#### 1. **Inclusion Lists** (Fork-Choice Inclusion Lists)
- **Problem**: Validators/builders can censor transactions
- **Solution**: Validators propose a mandatory list of transactions that must be included
- **Impact**: Censorship resistance, MEV fairness

#### 2. **Blob Increases (Continued)**
- Target: **12 blobs target / 18 blobs max** (another 2x from Pectra)
- **L2 Impact**: Further cost reductions, more rollup capacity

#### 3. **Validator Set Improvements**
- Potential changes to validator economics
- Staking yield adjustments based on network conditions

#### 4. **Interop Layer Enhancements**
- Tighter integration with Ethereum's **Interop Layer** (cross-L2 messaging)
- Enables "one Ethereum" UX (chain abstraction)

### Expected Impact
- **MEV**: 📈📈 (Inclusion lists reduce builder power)
- **L2 Scaling**: 📈📈 (More blob capacity)
- **Censorship Resistance**: 📈📈📈 (Forced inclusion)

### Timeline
- **January 2026**: EIP finalization
- **March 2026**: Testnet deployments (Sepolia, Hoodi)
- **Q2 2026**: Mainnet activation (likely May/June)

---

## Hegota (Planned: Q4 2026)

**Full Name**: Heze + Bogota (Consensus + Execution)  
**Expected**: Second half of 2026 (likely November/December)  
**Status**: Roadmap confirmed, EIPs under discussion

### Overview
Hegota tackles Ethereum's **long-term state bloat problem** with the introduction of **Verkle Trees**, plus further L2 optimizations.

### Proposed EIPs (Tentative)

#### 1. **Verkle Trees** 🌟
**The most significant change**—replaces Merkle Patricia Tries with Verkle Trees.

**What are Verkle Trees?**
- New cryptographic data structure using **vector commitments**
- **Smaller witness sizes**: ~150 KB → ~10 KB (15x reduction)
- **Enables stateless clients**: Clients don't need full state, just witnesses

**Why it matters:**
- **Problem**: Ethereum state is growing unbounded (~100 GB and growing)
- **Solution**: Witnesses become so small that light clients can verify any transaction
- **Impact**: Dramatically lowers hardware requirements for full nodes

**Challenges:**
- Requires state migration (complex, time-consuming)
- Breaking change (old proofs incompatible)
- Expected implementation: Gradual rollout in 2026

#### 2. **Historical Data Management**
- Better pruning of old blocks/receipts
- **Impact**: Reduces storage burden on nodes

#### 3. **State Expiry / Rent (Exploratory)**
- Potential mechanisms to limit state growth
- May defer to post-Hegota if not ready

### Expected Impact
- **State Management**: 📈📈📈📈 (Verkle Trees are huge)
- **Node Requirements**: 📈📈📈 (Enables lower-spec hardware)
- **Decentralization**: 📈📈📈 (More nodes can participate)
- **Developer Workflow**: 📈 (Simpler light clients)

### Timeline
- **Q1 2026**: Verkle Tree testnet
- **Q2-Q3 2026**: State migration testing
- **Q4 2026**: Mainnet activation (if ready, otherwise defers to 2027)

---

## Future Roadmap (Post-2026)

### Confirmed Direction
1. **PeerDAS Expansion** (2027+)
   - Increase blob targets to 16 MB/block
   - Further reduce sampling requirements (1/16 or 1/32)

2. **ZK-EVM Validation** (2026-2027)
   - Portions of network validate execution with zkSNARKs
   - **Goal**: Full Ethereum state validity proofs

3. **Single Slot Finality** (2027+)
   - Reduce finality from ~15 minutes to ~12 seconds
   - **Requires**: Major consensus layer changes

4. **Quantum Resistance** (2028+)
   - Transition to post-quantum cryptography
   - **Preemptive**: Quantum computers not yet a threat, but planning ahead

### Twice-a-Year Cadence
Ethereum has settled into a **predictable upgrade cycle**:
- **H1 (May/June)**: Spring upgrade
- **H2 (November/December)**: Fall upgrade

**Benefits:**
- Developers can plan around fixed dates
- Reduces coordination overhead
- Faster feature delivery than previous 12-18 month cycles

---

## Upgrade Comparison

| Upgrade | Date | Key Focus | Biggest EIP | L2 Impact | User Impact |
|---------|------|-----------|-------------|-----------|-------------|
| **Pectra** | May 2025 | UX, Staking | EIP-7702 (Smart EOAs) | 📈📈 (2x blobs) | 📈📈📈 (Better wallets) |
| **Fusaka** | Dec 2025 | Scaling, DA | EIP-7594 (PeerDAS) | 📈📈📈📈 (10x future) | 📈 (Faster blocks) |
| **Glamsterdam** | Q2 2026 | MEV, Interop | Inclusion Lists | 📈📈 (More blobs) | 📈📈 (Censorship resist.) |
| **Hegota** | Q4 2026 | State, Storage | Verkle Trees | 📈 (Indirect) | 📈📈 (Lighter nodes) |

---

## Developer Impact

### What Changed with Pectra
✅ **Use EIP-7702 for smart accounts**
- Batch transactions, gas sponsorship, session keys
- Libraries: Viem, Ethers.js support EIP-7702

✅ **More blob space for L2s**
- Rollups can post more data per block
- Cheaper L2 fees

### What Changed with Fusaka
✅ **PeerDAS networking**
- Node operators: Upgrade clients to support DAS
- L2 builders: Higher blob targets available

✅ **Faster blocks**
- 8-second slots instead of 12
- Adjust dapp UI for faster confirmations

### Preparing for Glamsterdam
🔜 **Inclusion Lists**
- May affect MEV strategies
- Builders/relayers need to respect forced inclusion

🔜 **More blobs**
- L2s should optimize for 12-18 blob targets

### Preparing for Hegota
🔜 **Verkle Tree Migration**
- State proofs will change format
- Light client implementations need updates
- **Timeline**: Gradual, not instant cutover

---

## Key Metrics

### Pre-Pectra (Q1 2025)
- **Blob Targets**: 3 target / 6 max
- **Slot Time**: 12 seconds
- **Validator Max Balance**: 32 ETH
- **L2 TPS (Combined)**: ~30,000

### Post-Fusaka (Q1 2026)
- **Blob Targets**: 6 target / 9 max
- **Slot Time**: 8 seconds
- **Validator Max Balance**: 2048 ETH
- **L2 TPS (Combined)**: ~50,000+
- **PeerDAS**: Live on mainnet

### Post-Glamsterdam (Projected Q3 2026)
- **Blob Targets**: 12 target / 18 max (estimated)
- **L2 TPS (Combined)**: ~100,000+
- **Inclusion Lists**: Active (MEV mitigation)

### Post-Hegota (Projected Q1 2027)
- **Verkle Trees**: Live
- **Witness Size**: ~10 KB (down from 150 KB)
- **State Growth**: Managed/limited
- **Full Node Requirements**: Lower (enables more decentralization)

---

## Resources & References

### Official Ethereum Docs
- **Roadmap**: https://ethereum.org/roadmap/
- **Pectra**: https://ethereum.org/roadmap/pectra/
- **Fusaka**: https://ethereum.org/roadmap/fusaka/
- **PeerDAS**: https://ethereum.org/roadmap/fusaka/peerdas/

### Research & Analysis
- **The Block**: https://www.theblock.co/post/383451/how-ethereums-protocol-changed-2025
- **Fidelity**: https://www.fidelitydigitalassets.com/research-and-insights/fusaka-upgrade-scaling-meets-value-accrual
- **Coin Metrics**: https://coinmetrics.io/company-news/ethereums-pectra-upgrade-key-changes-metrics-impacted/

### EIP Specifications
- **EIP-7702**: https://eips.ethereum.org/EIPS/eip-7702
- **EIP-7594 (PeerDAS)**: https://eips.ethereum.org/EIPS/eip-7594
- **EIP-7781**: https://eips.ethereum.org/EIPS/eip-7781

### Community Discussions
- **Ethereum Magicians**: https://ethereum-magicians.org
- **Ethresear.ch**: https://ethresear.ch

---

**Last Updated**: February 2026  
**Next Major Upgrade**: Glamsterdam (Q2 2026)
