# Additional Chains Research for ethskills.com

**Research Date:** February 15, 2026  
**Chains Analyzed:** Zora, Unichain, Celo, Scroll, Gnosis Chain, Polygon  
**Purpose:** Identify what vanilla LLMs DON'T know or get wrong about these chains

**Repository:** https://github.com/austintgriffith/ethskills-research ✅ (exists and cloneable)

---

## Executive Summary

**High-Value Additions:**
1. **Unichain** - NEW mainnet (Feb 2025), TEE-based MEV protection, Flashblocks - LLMs won't know this
2. **Celo** - JUST migrated to L2 (March 26, 2025) - LLMs will have stale L1 info
3. **Polygon** - zkEVM SHUTDOWN announced (June 2025) - LLMs will give outdated architecture info

**Moderate Value:**
4. **Scroll** - Controversial airdrop history, Canvas system - some delta but less critical
5. **Gnosis Chain** - Gnosis Pay real-world usage - modest delta

**Lower Priority:**
6. **Zora** - Standard OP Stack implementation, mostly creator-focused - limited delta

---

## 1. ZORA NETWORK

### Current State (2025-2026)
- **What it is:** OP Stack L2 focused on NFT/creator economy
- **Chain ID:** 7777777
- **Status:** Mainnet live since June 2023
- **TVL:** ~$20-30M range (low relative to other L2s)
- **Positioning:** "NFTs first" instead of DeFi-centric

### Key Technical Details
- **OP Stack:** Fault-proof rollups, EVM equivalence
- **Block time:** Inherits OP Stack 2-second blocks
- **Key contract pattern:** Zora 1155 contracts with modular minters
  - Main 1155 contract delegates to separate "minter" contracts
  - Supports ERC20 minting, fixed price minting
- **Not Stage 0 yet:** Centralized sequencer/proposer

### LLM Delta Assessment

**✅ SKIP - LLMs know this:**
- "Zora is an NFT/creator platform on an L2"
- "Built on OP Stack"
- General NFT marketplace concept

**⚠️ INCLUDE - LLMs give vague/hedged answers:**
- Specific Zora 1155 contract architecture (modular minters pattern)
- Chain ID 7777777
- Current TVL and adoption metrics

**❌ DEFINITELY INCLUDE - LLMs will be wrong:**
- (Nothing major - it's a straightforward OP Stack chain)

### Recommendation
**MINIMAL COVERAGE** - Add to L2s overview page with 2-3 sentences:
- "Creator-focused OP Stack L2 with chain ID 7777777"
- "Uses modular ERC-1155 minting contracts"
- Link to docs for developers wanting to build NFT platforms

**Why:** Standard OP Stack implementation with creator/NFT branding. No unique technical innovations that LLMs would misunderstand. Low strategic importance for agent development.

---

## 2. UNICHAIN

### Current State (2025-2026)
- **What it is:** Uniswap's own OP Stack L2, purpose-built for DeFi
- **Mainnet Launch:** February 10, 2025 (VERY RECENT)
- **Chain ID:** 130 (mainnet), 1301 (Sepolia testnet)
- **Status:** Stage 1 rollup, actively rolling out features
- **Testnet:** Live since October 2024

### Key Technical Innovations (THE BIG DELTA)

#### 1. **Verifiable Block Building (TEE-based)**
- Built in collaboration with Flashbots "Rollup-Boost"
- Uses Trusted Execution Environments (TEEs)
- **Priority ordering:** Transactions ordered by time received, NOT by gas price
- **Private encrypted mempool:** Reduces MEV extraction opportunities
- First L2 to use TEE-based block building in production

#### 2. **Flashblocks (250ms sub-blocks)**
- **Current:** 1-second block times
- **Coming soon:** 250ms "sub-blocks" for instant UX
- Roadmap includes public attestations for TEE ordering verifiability

#### 3. **Superchain Native Interoperability**
- Part of OP Superchain
- Single-block cross-chain message passing (when implemented)
- Cross-chain swapping built into Uniswap Interface

#### 4. **Validation Network (upcoming)**
- Decentralized node network to verify blocks
- Adds extra finality layer beyond L1 settlement

### Key Addresses & Details
- Sequencer: us-east-2 (Ohio)
- RPC: https://mainnet.unichain.org (rate-limited)
- Explorer: https://uniscan.xyz
- Open source: MIT licensed, can be adopted by other chains

### LLM Delta Assessment

**✅ SKIP - LLMs know this:**
- "Uniswap is building an L2" (but not details)

**⚠️ INCLUDE - LLMs give vague/hedged answers:**
- Exact mainnet launch date (Feb 2025)
- Relationship to Uniswap V4
- General concept of MEV protection

**❌ DEFINITELY INCLUDE - LLMs will be WRONG/OUTDATED:**
- **Training cutoff issue:** Most LLMs trained before Feb 2025 won't know mainnet launched
- TEE-based block building specifics (Flashbots Rollup-Boost)
- Priority ordering mechanism (time-based, not gas-based)
- 250ms Flashblocks roadmap
- Chain ID 130
- Verifiable ordering guarantees for MEV taxes

### Recommendation
**HIGH PRIORITY - FULL COVERAGE**

Why it matters for agents:
1. **MEV dynamics are different** - Priority ordering changes how agents should submit transactions
2. **Flashblocks enable new strategies** - 250ms preconfirmations change what's possible
3. **Cross-chain UX paradigm shift** - Agents need to understand Superchain interop patterns
4. **Recent launch** - Zero chance base LLMs know current state

Suggested content:
- Dedicated Unichain section or standalone page
- Explain priority ordering vs gas auctions
- TEE/Flashbots block builder architecture
- How to build on Unichain vs standard OP Stack
- MEV protection mechanisms for agent strategies

---

## 3. CELO

### Current State (2025-2026)
- **MAJOR NEWS:** Mainnet migrated to L2 on **March 26, 2025, 3:00 AM UTC** (block 31056500)
- **Previous:** Independent L1 blockchain (since 2020)
- **Now:** OP Stack L2 on Ethereum
- **Migration timeline:**
  - Alfajores testnet: Sept 26, 2024
  - Baklava testnet: Feb 20, 2025
  - Mainnet: March 26, 2025

### Key Features

#### 1. **Mobile-First / Real-World Payments Focus**
- **MiniPay:** Stablecoin wallet built into Opera Mini browser
  - Also available as standalone Android/iOS app
  - Uses phone numbers for wallet-to-wallet transfers
  - Sub-cent transaction fees
  - Auto-backup through Google
  - Mini App discovery page
  - Focused on Africa initially

#### 2. **Multi-Currency Stablecoins**
Mainnet addresses:
- **cUSD** (Celo Dollar): `0x765de816845861e75a25fca122bb6898b8b1282a`
- **cEUR** (Celo Euro): `0xd8763cba276a3738e6de85b4b3bf5fded6d6ca73`
- **cREAL** (Celo Brazilian Real): `0xe8537a3d056DA446677B9E9d6c5dB704EaAb4787`
- **CELO** (native token): `0x471ece3750da237f93b8e339c536989b8978a438`

#### 3. **OP Stack Integration**
- Standard OP Stack benefits: cheap data via blobs, EVM equivalence
- Maintains mobile/payments focus while gaining Ethereum security

### LLM Delta Assessment

**✅ SKIP - LLMs know this:**
- "Celo is mobile-focused"
- "Celo has stablecoins"

**⚠️ INCLUDE - LLMs give vague/hedged answers:**
- L2 migration status (may say "planned" instead of "completed")
- MiniPay specifics and Opera partnership
- cREAL existence

**❌ DEFINITELY INCLUDE - LLMs will be WRONG:**
- **Calling Celo an L1** - It WAS an L1, but migrated to L2 March 26, 2025
- Migration completion status and exact date
- OP Stack architecture post-migration
- Current contract addresses on L2
- MiniPay integration details

### Recommendation
**HIGH PRIORITY - SUBSTANTIAL COVERAGE**

Why it matters for agents:
1. **Architectural confusion** - LLMs will confidently call it an L1 (WRONG after March 2025)
2. **Real-world payment rails** - MiniPay is actual crypto→phone number payments at scale
3. **Multi-currency stablecoins** - Unique non-USD stablecoin ecosystem
4. **Africa focus** - Different use cases than typical DeFi chains

Suggested content:
- **Prominent correction:** "Celo completed L2 migration March 26, 2025 - no longer an independent L1"
- MiniPay integration patterns for agents
- Multi-currency stablecoin handling (cUSD, cEUR, cREAL)
- Mobile-first design considerations
- OP Stack L2 specifics post-migration

---

## 4. SCROLL

### Current State (2025-2026)
- **Type:** zkEVM (zero-knowledge rollup)
- **Status:** Mainnet since October 2023
- **TVL:** ~$748M (as of mid-2025) - 3rd largest Ethereum L2, largest zkEVM by market share
- **Token:** SCR (launched Oct 2024)

### Key Features

#### 1. **zkEVM Architecture**
- Bytecode-level EVM equivalence using ZK proofs
- No need for contract modifications (unlike zkVM approaches)
- Compression using zlib
- Chain ID: 534353

#### 2. **Scroll Canvas**
- Credential/badge system for onchain achievements
- Each Canvas is a smart contract (minted via ProfileRegistry)
- Uses Ethereum Attestation Service (EAS) for badges
- Projects issue badges as attestations
- Profile system for ecosystem activity

#### 3. **Controversial Token Launch**
- SCR token launched Oct 22, 2024
- Major controversy: Top 10 airdrop recipients got 11.7% of total drop
- Token declined 32% in first 24 hours
- Community accused team of "predatory" practices and insider dumping
- TVL declined 24% in the week following launch
- Team later announced restructuring and Session 2 airdrop

### Ecosystem
- 100+ DeFi protocols
- Over $792M TVL (varies by source/date)
- Active development community

### LLM Delta Assessment

**✅ SKIP - LLMs know this:**
- "Scroll is a zkEVM"
- "It uses zero-knowledge proofs"
- General zkEVM benefits

**⚠️ INCLUDE - LLMs give vague/hedged answers:**
- Scroll Canvas system and how it works
- EAS integration for badges
- Current TVL and adoption metrics
- zkEVM vs zkVM differences

**❌ DEFINITELY INCLUDE - LLMs will be WRONG:**
- Airdrop controversy details (SCR token launch issues)
- Canvas contracts and badge system specifics
- Current ecosystem size post-controversy
- Whether to trust governance/tokenomics

### Recommendation
**MODERATE COVERAGE - Add to zkEVM section**

Why it matters:
1. **zkEVM implementation details** - LLMs often conflate zkEVM and zkVM
2. **Canvas badge system** - Unique credential primitive
3. **Governance skepticism** - Agents should know about token controversy
4. **Largest zkEVM** - By market share, significant for developers

Suggested content:
- zkEVM equivalence explanation (vs Polygon zkEVM, zkSync)
- Scroll Canvas credential system
- Brief note on controversial token launch (context for governance)
- When to choose Scroll vs other zkEVMs

**Note:** Not as critical as Unichain/Celo because the core zkEVM tech is well-known. Delta is mostly in Canvas system and governance history.

---

## 5. GNOSIS CHAIN

### Current State (2025-2026)
- **Type:** Community-run EVM chain (NOT a sidechain, NOT an L2)
- **Formerly:** xDai Chain (rebranded to Gnosis Chain)
- **TVL:** ~$140M (as of late 2025)
- **Key feature:** Low-cost, community governance

### Key Features

#### 1. **Gnosis Pay**
- Real-world crypto debit card
- Powers actual point-of-sale payments
- Integrated with Gnosis Chain DeFi
- Multiple countries supported (expanding)
- Had transaction issues in May 2025 (resolved)

#### 2. **Low Barrier Staking**
- 1 GNO minimum (vs 32 ETH on Ethereum)
- Community validators
- Fusaka hard fork (delayed to 2026) will improve scalability

#### 3. **DeFi Ecosystem**
- sDAI integration (earns 5% yield from MakerDAO)
- Key protocols: Agave, Balancer, Curve
- gEURO stablecoin launch (2025, RaidGuild)
- Real-World Asset (RWA) focus

#### 4. **Governance**
- GnosisDAO active governance
- Community-driven development
- Multiple client teams (Erigon, Nethermind, Geth)

### Safe (formerly Gnosis Safe)
- **History:** Started as Gnosis Safe in 2018, spun off as independent "Safe" project
- **Now:** Separate ecosystem, but originated from Gnosis
- **Importance:** Most widely used smart account / multisig in Ethereum

### LLM Delta Assessment

**✅ SKIP - LLMs know this:**
- "Gnosis Chain is cheap"
- "Used to be xDai"
- "Gnosis Safe is a multisig wallet"

**⚠️ INCLUDE - LLMs give vague/hedged answers:**
- Relationship between Gnosis Chain and Safe (now independent)
- Current TVL and ecosystem size
- sDAI integration details

**❌ DEFINITELY INCLUDE - LLMs will be WRONG:**
- **"Gnosis is a sidechain"** - NOT accurate, it's a community-run EVM chain
- Gnosis Pay as actual working product
- gEURO launch and RWA strategies
- Fusaka hard fork status (delayed)
- That Safe is now independent (not "Gnosis Safe")

### Recommendation
**MINIMAL-TO-MODERATE COVERAGE**

Why it matters:
1. **Terminology correction** - Not a sidechain, not really an L2 in the rollup sense
2. **Real-world payments** - Gnosis Pay is actually used for purchases
3. **Safe origin story** - Historical context for most-used multisig

Suggested content:
- Brief explainer: community-run EVM chain (not sidechain/not L2 rollup)
- Gnosis Pay as real-world payment rail
- Safe origin and independence
- When to deploy on Gnosis (cheap execution, community values)

**Note:** Lower priority because it's a relatively straightforward chain. Main delta is correcting "sidechain" misconception and Gnosis Pay awareness.

---

## 6. POLYGON

### Current State (2025-2026)
- **MAJOR STRATEGIC SHIFT:** Announced June 2025
- **zkEVM Status:** Being **SHUT DOWN** in 2025-2026
- **New Focus:** Polygon PoS + AggLayer + payments/RWAs
- **Token:** MATIC → POL migration (85% conversion rate by mid-2025)

### The Polygon Ecosystem Today

#### 1. **Polygon PoS** (Main Chain)
- **NOT a sidechain anymore** (was originally, but evolved)
- Commit chain with its own validator set
- $500M+ monthly payment volume
- 410M+ wallets, 18.9M MAUs
- 50+ payment projects (strong in Latin America)
- **This is the focus going forward**

#### 2. **Polygon zkEVM** (Being Discontinued)
- **Announcement:** Sandeep Nailwal (new Foundation CEO, June 2025)
- **Reason:** Underutilized, resources being reallocated
- **Timeline:** Shutdown by 2026
- **Lesson:** Not every ZK experiment succeeds

#### 3. **AggLayer** (THE FUTURE)
- **Purpose:** Cross-chain interoperability and settlement
- **Vision:** "Internet of blockchains" - chains connected trustlessly
- **Mechanism:** 
  - Unified liquidity across chains
  - Sub-5 second cross-chain finality (roadmap)
  - No wrapped tokens or intermediaries
  - Bridge-and-call pattern (multi-chain txs feel like one tx)
- **POL Role:** Stake on multiple chains simultaneously, earn multi-token rewards
- **Gigagas Roadmap:** Target 100k TPS (early 2026)

#### 4. **POL Token** (Formerly MATIC)
- **Migration Status:** 85% conversion rate by mid-2025
- **New utility:** "Hyperproductive" - stake on multiple chains at once
- **Purpose:** Secure AggLayer ecosystem, not just Polygon PoS

### Strategic Positioning (2025-2026)
- **Stablecoins & Payments:** Primary focus (real-world adoption)
- **RWAs:** Real-World Assets integration
- **Enterprise:** Institutions building on PoS
- **NOT focusing on:** General-purpose zkEVM L2 competition

### LLM Delta Assessment

**✅ SKIP - LLMs know this:**
- "Polygon is a scaling solution"
- "MATIC is the token" (outdated but known)

**⚠️ INCLUDE - LLMs give vague/hedged answers:**
- MATIC → POL migration
- AggLayer concept
- Current architecture

**❌ DEFINITELY INCLUDE - LLMs will be VERY WRONG:**
- **"Polygon is a sidechain"** - Outdated (was true 2020-2021, not now)
- **"Polygon zkEVM is a major product"** - It's being shut down!
- **zkEVM status** - LLMs may recommend building on it (bad advice in 2025+)
- **POL vs MATIC** - Token migration details
- **AggLayer as core strategy** - This is the actual future
- **Multi-chain staking** - POL's "hyperproductive" model
- **Payment/RWA focus** - Not general DeFi anymore

### Recommendation
**HIGHEST PRIORITY - FULL CORRECTION NEEDED**

Why it matters:
1. **Major architectural confusion** - "Sidechain" label is outdated and misleading
2. **zkEVM shutdown is huge** - LLMs may recommend building on dead tech
3. **AggLayer is the actual innovation** - Cross-chain future, not another L2
4. **Token migration** - POL mechanics are new
5. **Strategic repositioning** - Payments/RWAs, not DeFi-first

Suggested content:
- **Big warning banner:** "Polygon zkEVM is being discontinued (2025-2026). Do not start new projects there."
- Evolution timeline: Sidechain → Commit Chain → PoS + AggLayer
- AggLayer explainer: unified liquidity, cross-chain settlement
- POL staking mechanics (multi-chain security)
- When to build on Polygon PoS (payments, RWAs, not zkEVM)
- Gigagas roadmap and 100k TPS targets

---

## Priority Ranking for ethskills.com

### Tier 1: Critical Updates (High LLM Error Rate)
1. **Polygon** - Sidechain misconception, zkEVM shutdown, AggLayer shift
2. **Unichain** - Too new for LLMs, unique MEV architecture
3. **Celo** - Recent L2 migration, LLMs will call it an L1

### Tier 2: Valuable Context (Moderate Delta)
4. **Scroll** - Canvas system, zkEVM specifics, airdrop controversy

### Tier 3: Minor Corrections (Low Delta)
5. **Gnosis Chain** - Terminology fix (not sidechain), Gnosis Pay
6. **Zora** - Standard OP Stack, minimal unique delta

---

## Verified Contract Addresses

### Celo (Mainnet, post-L2 migration)
- cUSD: `0x765de816845861e75a25fca122bb6898b8b1282a`
- cEUR: `0xd8763cba276a3738e6de85b4b3bf5fded6d6ca73`
- cREAL: `0xe8537a3d056DA446677B9E9d6c5dB704EaAb4787`
- CELO: `0x471ece3750da237f93b8e339c536989b8978a438`

### Zora
- Chain ID: 7777777
- Zora 1155 contracts: Use modular minter pattern (see docs.zora.co)

### Unichain
- Chain ID: 130 (mainnet), 1301 (testnet)
- RPC: https://mainnet.unichain.org
- Explorer: https://uniscan.xyz

### Scroll
- Chain ID: 534353
- Canvas ProfileRegistry: (see github.com/scroll-tech/canvas-contracts)

---

## Next Steps

1. **Immediate Action Items:**
   - Create Polygon correction page (zkEVM shutdown warning)
   - Add Unichain deep-dive (MEV protection, Flashblocks)
   - Update Celo entry (L1→L2 migration complete)

2. **Medium Priority:**
   - Scroll zkEVM section with Canvas explainer
   - Gnosis Chain terminology fixes

3. **Low Priority:**
   - Zora mention in OP Stack overview

4. **Testing Strategy:**
   - Ask vanilla LLMs: "What is Polygon?" → Expect wrong "sidechain" answer
   - Ask: "Should I deploy to Polygon zkEVM?" → They might say yes (wrong!)
   - Ask: "Is Celo an L1?" → They'll probably say yes (wrong as of March 2025)
   - Ask: "How does Unichain prevent MEV?" → They won't know specifics

---

## Conclusion

**Must-Cover Chains:** Polygon, Unichain, Celo  
**Should-Cover:** Scroll  
**Nice-to-Have:** Gnosis Chain, Zora

The biggest deltas are in **recent major changes** (Celo L2 migration, Polygon zkEVM shutdown, Unichain launch) and **architectural misconceptions** (Polygon "sidechain", Gnosis "sidechain"). 

AggLayer and Unichain's TEE-based block building represent genuinely new primitives that LLMs won't understand from training data alone.

Focus ethskills content on what agents will confidently get WRONG, not what they'll vaguely get right.
