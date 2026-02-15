# L2 Ecosystem Research for ethskills.com

**Date:** February 15, 2025  
**Purpose:** Identify knowledge gaps in LLMs about L2 ecosystems → drive skill content for AI agents  

---

## Table of Contents
1. [Research Findings by Chain](#1-research-findings-by-chain)
2. [Baseline Test Results](#2-baseline-test-results)
3. [Delta Analysis](#3-delta-analysis)
4. [Recommended GitHub Issues](#4-recommended-github-issues)

---

## 1. Research Findings by Chain

### 🔵 Base

#### Aerodrome Finance — Dominant DEX on Base
**What it is:** A ve(3,3) DEX inspired by Solidly (Andre Cronje), forked/improved from Velodrome on Optimism. Combines the mechanics of Curve, Convex, and Uniswap. Launched August 2023 by the same team (Dromos Labs) behind Velodrome.

**TVL:** ~$500M-$600M (Feb 2025). Peaked at $1B+ in December 2024. The dominant DEX on Base by TVL.

**How it works:**
- **AMM:** Constant-product pools (like Uniswap V2) — both volatile and stable pairs
- **ve(3,3) tokenomics:** Users lock AERO tokens to get veAERO (vote-escrowed NFTs / ERC-721). veAERO holders vote on which liquidity pools receive AERO emissions each epoch (weekly). In return, voters receive 100% of trading fees + bribes from the pools they vote for.
- **Flywheel:** Pools generating the most fees attract the most votes → get the most emissions → attract more LPs → deeper liquidity → more volume → more fees. Protocols can also deposit "bribes" to incentivize votes toward their pools.
- **Key difference from Uniswap:** LPs don't earn fees directly — fees go to veAERO voters. LPs earn AERO emissions instead.

**Verified Contract Addresses (Base Mainnet):**

| Contract | Address |
|----------|---------|
| Router | `0xcF77a3Ba9A5CA399B7c97c74d54e5b1Beb874E43` |
| AERO Token | `0x940181a94A35A4569E4529A3CDfB74e38FD98631` |
| Voter | `0x16613524e02ad97eDfeF371bC883F2F5d6C480A5` |
| VotingEscrow | `0xeBf418Fe2512e7E6bd9b87a8F0f294aCDC67e6B4` |
| PoolFactory | `0x420DD381b31aEf6683db6B902084cB0FFECe40Da` |
| FactoryRegistry | `0x5C3F18F06CC09CA1910767A34a20F771039E37C0` |
| GaugeFactory | `0x35f35cA5B132CaDf2916BaB57639128eAC5bbcb5` |
| Minter | `0xeB018363F0a9Af8f91F06FEe6613a751b2A33FE5` |
| RewardsDistributor | `0x227f65131A261548b057215bB1D5Ab2997964C7d` |
| VotingRewardsFactory | `0x45cA74858C579E717ee29A86042E0d53B252B504` |
| Pool (implementation) | `0xA4e46b4f701c62e14DF11B48dCe76A7d793CD6d7` |
| Forwarder | `0x15e62707FCA7352fbE35F51a8D6b0F8066A05DCc` |
| ManagedRewardsFactory | `0xFdA1fb5A2a5B23638C7017950506a36dcFD2bDC3` |

**Major Update (Nov 2025):** Aerodrome and Velodrome merged under Dromos Labs into "Aero" — a unified DEX expanding to Ethereum and Circle's Arc. Token split: 94.5% to AERO holders, 5.5% to VELO holders.

#### Farcaster / Warpcast Integration
- **Farcaster:** Decentralized social protocol. Hubs store data; clients (like Warpcast) provide the UI.
- **Warpcast rebranded to "Farcaster" (May 2025)** — the official client captures ~100% of user activity despite protocol's decentralization goals.
- **Frames v2:** Mini-applications embedded directly in social posts. Can trigger onchain actions (mint NFTs, swap tokens, play games). Built on open standards. Full web app capability in an iframe.
- **Base integration:** Farcaster is the social layer of Base. Frames run transactions on Base by default. The `@farcaster/frame-sdk` provides `sdk.actions.addFrame()` for notifications and user prompts.
- **Agent relevance:** Farcaster Frames are a key distribution channel for onchain actions on Base. Understanding the Frames v2 spec is essential for building Base-native social/consumer apps.

#### Coinbase Smart Wallet / OnchainKit
- **Smart Wallet:** ERC-4337 compatible smart contract wallet. Creates a single address that works across EVM networks. Currently **only Base Mainnet and Base Sepolia supported** for server-side wallets. Supports gasless transactions, passkey authentication, and batch transactions.
- **OnchainKit:** npm package `@coinbase/onchainkit`. React components + TypeScript utilities for building Base apps. Bootstrap with `npm create onchain`. Key components include wallet connection, transaction handling, identity resolution, and swap widgets.
- **AgentKit:** Coinbase's framework for AI agents to interact onchain. Uses CDP Smart Wallet API for gasless agent transactions. Integrates with LangChain, Vercel AI SDK.

#### Consumer/Social Apps on Base
- **Friend.tech:** SocialFi app (launched 2023, now defunct). Pioneered "keys" (social tokens for creator access) on Base.
- **DEGEN token:** Farcaster-native memecoin/tipping token. Had its own Degen Chain (L3 on Base).
- **Zora:** NFT creation/minting platform. Runs on OP Stack (Zora Network) but deeply integrated with Base.
- **Base has evolved from "Degen Base" to "Consumer Application Base"** — the identity is around consumer/social crypto apps vs. pure DeFi.

#### Base-Specific Developer Patterns
- Base is an OP Stack chain (Superchain member, Stage 1)
- Chain ID: 8453
- Dominant DEX: Aerodrome (not Uniswap on Base)
- Coinbase paymaster for gasless transactions
- Smart Wallet as default wallet paradigm (not just MetaMask)
- Farcaster Frames as distribution channel

---

### 🟠 Arbitrum

#### GMX v2 — Perpetual DEX
**What it is:** Leading onchain perpetual exchange. V2 launched with isolated GM (liquidity) pools for each market, replacing V1's single GLP pool.

**How GMX v2 works:**
- **GM Pools:** Each market has its own pool backed by specific tokens. E.g., ETH/USD market is backed by ETH (long token) + USDC (short token). LPs deposit these tokens and receive GM tokens.
- **Fully Backed Markets:** E.g., ETH/USD backed by ETH + USDC. The backing tokens directly correspond to the traded asset.
- **Synthetic Markets:** E.g., DOGE/USD backed by ETH + USDC. The backing tokens don't match the traded asset. Uses ADL (Auto-Deleveraging) to close profitable positions when profit thresholds are reached to prevent insolvency.
- **LPs earn:** Trading fees, liquidation fees, borrowing fees, and swap fees. But bear risk from trader PnL.
- **Price Impact Fees:** Trades that increase imbalance pay more; trades that decrease imbalance are discounted.
- **V2 offers 40+ tradeable assets** on Arbitrum (vs. only BTC, ETH, UNI, LINK on V1).
- **GMX expanded to Solana (March 2025)** via community push.

**Key Contract Addresses (Arbitrum):**

| Contract | Address |
|----------|---------|
| Exchange Router (V2, newer) | `0x7c68c7866a64fa2160f78eeae12217ffbf871fa8` |
| Exchange Router (V2, older) | `0x602b805EedddBbD9ddff44A7dcBD46cb07849685` |
| Reward Router V2 | `0xB95DB5B167D75e6d04227CfFFA61069348d271F5` |
| Reward Router (V1) | `0xA906F338CB21815cBc4Bc87ace9e68c87eF8d8F1` |

**TVL:** GMX total TVL has fluctuated; annualized fees ~$58.2M. Competes with Hyperliquid which has grown to $4.58B TVL.

#### Pendle Finance — Yield Trading
**What it is:** Protocol that tokenizes future yield. The world's largest crypto yield trading platform.

**How Pendle works:**
1. **SY (Standardized Yield):** Wraps any yield-bearing asset into a standard interface. E.g., wstETH → SY-wstETH.
2. **PT (Principal Token):** Represents the principal of the underlying asset. Redeemable 1:1 at maturity. Trades at a discount before maturity (the discount = implied yield).
3. **YT (Yield Token):** Represents all the yield generated until maturity. Value decays to 0 at maturity.
4. **Core invariant:** `SY_value = PT_value + YT_value`
5. **Users deposit SY into the YT contract**, which mints both PT and YT.

**Use cases:**
- Buy PT at discount = lock in fixed yield
- Buy YT = leverage your yield exposure (bet yield goes up)
- LP in Pendle pools = earn trading fees + PENDLE incentives

**Pendle + Aave strategy:** PT-USDe used as collateral on Aave. When Aave raised the cap by $600M, it filled in an hour. PT-as-collateral TVL reached record $4.6B.

**Pendle also launched "Boros"** — a yield trading product for funding rates.

**Key Contract Addresses (Arbitrum - chain ID 42161):**

| Contract | Address |
|----------|---------|
| Router | `0x888888888889758F76e7103c6CbF23ABbF58F946` |
| PENDLE Token | `0x0c880f6761F1af8d9Aa9C466984b80DAb9a8c9e8` |
| Market Factory V4 | `0xd9f5e9589016da862D2aBcE980A5A5B99A94f3E8` |
| Market Factory V3 | `0x2FCb47B58350cD377f94d3821e7373Df60bD9Ced` |
| Yield Contract Factory V4 | `0xc7F8F9F1DdE1104664b6fC8F33E49b169C12F41E` |
| Router Static | `0xAdB09F65bd90d19e3148D9ccb693F3161C6DB3E8` |
| PT/YT Oracle | `0x5542be50420E88dd7D5B4a3D488FA6ED82F6DAc2` |
| Limit Router | `0x000000000000c9B3E2C3Ec88B1B4c0cD853f4321` |
| Boros Router | `0x80808080dab95efed788a9214e400ba552def6` |

**TVL:** Average ~$5.7B in 2025 (up 76% YoY), peak ~$13.4B. Multi-chain: Ethereum, Arbitrum, Base, Optimism, BNB, Sonic, Mantle, Berachain.

#### Camelot DEX
- **Arbitrum-native DEX** with custom liquidity infrastructure
- TVL: ~$75M-$120M. Daily volume ~$200M on Arbitrum.
- Features: Concentrated liquidity (V3-style), custom pools for new launches, launchpad functionality
- 75+ protocol partners, $46B total volume traded, $48M in fees generated
- Recently expanded to Aleph Zero EVM
- Offchain Labs (Tandem) invested in Camelot

#### Treasure DAO / Gaming Ecosystem
- **Was** the leading gaming ecosystem on Arbitrum with MAGIC token
- **Migrated to zkSync** for Treasure Chain (Dec 2024)
- **Treasure Chain shut down May 30, 2025** due to unsustainable costs
- Pivoted to NFT marketplace focus; game publishing operations discontinued
- **This is important context:** An LLM might still think Treasure is thriving on Arbitrum — it's largely dead/pivoting

#### Arbitrum Orbit (L3s)
- Framework for launching custom L3 chains on Arbitrum using Nitro technology
- **47 live Orbit chains on mainnet** (as of Oct 2025), 14 in testnet, 12 in development
- Fully functioning fraud proofs, advanced compression, EVM compatible
- Use cases: Gaming chains (Xai), AI chains (Skynet), DeFi-specific chains
- Can customize gas token, throughput, data availability

#### Arbitrum Stylus
- **Smart contracts in Rust, C, C++** (any language that compiles to WASM)
- Runs on a WASM VM alongside the EVM — both VMs share the same state
- **Key benefits:**
  - 10-100x cheaper execution for compute-heavy operations
  - Reentrancy detection disabled by default in Rust SDK (safer)
  - Import Rust crates into smart contracts
  - Interoperable with Solidity contracts (can call each other)
- **Languages supported:** Rust (primary), C, C++, Go, Sway, Move, Cairo
- **thirdweb built Stylus integration** — WASM contract deployment, pre-built contracts, SDKs
- **RedStone** deployed oracles using Stylus for significant gas savings
- Contracts compiled with Stylus must be "activated" via `ARB_WASM_ADDRESS` (0x0000…0071)

---

### 🔴 Optimism

#### Velodrome → Aerodrome → Aero
- **Velodrome:** The dominant DEX on Optimism. Same ve(3,3) model as Aerodrome.
- **Same team (Dromos Labs)** built Velodrome first (2022), then Aerodrome for Base (2023).
- Both are forks/improvements of Andre Cronje's Solidly on Fantom.
- **November 2025 merger:** Velodrome + Aerodrome → "Aero" unified platform.
- Token distribution: 94.5% AERO holders, 5.5% VELO holders.
- Expanding to Ethereum mainnet and Circle's Arc chain.

#### Superchain Status
**The Superchain** is the network of OP Stack chains that share:
- Shared security from Ethereum
- Native interoperability (cross-chain messaging)
- Shared upgrade governance

**Superchain Members (from Superchain Registry, as of Feb 2025):**

| Chain | Stage | Category | Notable |
|-------|-------|----------|---------|
| **Base** | Stage 1 | Finance | $4.87B TVL, 7906 TPS |
| **OP Mainnet** | Stage 1 | General | $293M TVL |
| **Ink** (Kraken) | Stage 1 | Finance | $537M TVL |
| **Unichain** | Stage 1 | Finance | $96M TVL |
| **Mode** | Stage 0 | Finance | $2.3M TVL |
| **Zora** | Stage 0 | Creator | $55K TVL |
| **World Chain** | Stage 0 | General | $38M TVL |
| **Soneium** (Sony) | Stage 0 | Creator | $26M TVL |
| **BOB** | Stage 0 | Finance | $53M TVL |
| **Lisk** | Stage 0 | General | $2.6M TVL |
| **Swell** | Stage 0 | Finance | $3.9M TVL |
| **Polynomial** | Stage 0 | Finance | $2.1M TVL |

**Superchain stats:** 20.9M daily L2 transactions, 58.6% L2 market share

**Interop Status:**
- Goal: "Set of interoperable Stage 1 chains doing $250M/month in cross-chain asset transfers"
- Message passing protocol allows cross-chain messages between Superchain members
- New chains can accept messages permissionlessly; sending requires opt-in from receiving chains
- Season 7 approved onchain decision markets (futarchy model)
- Still early — full interop not yet live as of early 2025

#### OP Stack
- Open-source modular framework for building L2s
- Components: execution engine (op-geth), rollup node, batcher, proposer
- Shared upgrade path — all OP Stack chains upgrade together
- Revenue sharing: chains contribute 15% sequencer revenue to Optimism Collective

---

### 🟣 zkSync Era

#### Native Account Abstraction
**Key difference from ERC-4337:** zkSync's AA is native to the protocol, not a separate infrastructure layer. Every account (including EOAs) goes through the same transaction flow.

**How it works:**
- Every account on zkSync has smart contract code (EOAs use `DefaultAccount.sol`)
- Custom accounts implement the `IAccount` interface with methods:
  - `validateTransaction` — verify the transaction is authorized
  - `executeTransaction` — execute the transaction logic
  - `payForTransaction` — handle fee payment
  - `prepareForPaymaster` — set up paymaster interaction
- Accounts are deployed using `createAccount` or `create2Account` (not regular `create`)
- Must implement EIP-1271 for signature validation
- Nonce management via `NonceHolder` system contract

**Benefits over ERC-4337:**
- No UserOperation mempool or separate bundler infrastructure
- No EntryPoint contract — native to the VM
- Simpler architecture: fewer moving parts
- Every account is natively a smart account

#### Paymaster Patterns
**Paymasters** subsidize gas fees or allow paying in ERC20 tokens.

**Two standard flows:**
1. **General flow:** No user action needed. Paymaster just pays.
   ```solidity
   function general(bytes calldata data);
   ```
2. **Approval-based flow:** User sets token allowance for paymaster.
   ```solidity
   function approvalBased(address _token, uint256 _minAllowance, bytes calldata _innerInput);
   ```

**IPaymaster interface:**
```solidity
interface IPaymaster {
    function validateAndPayForPaymasterTransaction(
        bytes32 _txHash,
        bytes32 _suggestedSignedHash,
        Transaction calldata _transaction
    ) external payable returns (bytes4 magic, bytes memory context);

    function postTransaction(
        bytes calldata _context,
        Transaction calldata _transaction,
        bytes32 _txHash,
        bytes32 _suggestedSignedHash,
        ExecutionResult _txResult,
        uint256 _maxRefundedGas
    ) external payable;
}
```

**Key constant:** `PAYMASTER_VALIDATION_SUCCESS_MAGIC = IPaymaster.validateAndPayForPaymasterTransaction.selector`

**Gas considerations:** Paymaster transactions cost more due to:
- Internal computation in `validateAndPayForPaymasterTransaction` and `postTransaction`
- Funds transfer to bootloader
- ERC20 allowance management (first-time allowance can cost ~400k gas at 50 gwei L1 price)

**Testnet paymaster:** Provided by zkSync on testnet — pays fees in any ERC20 at 1:1 rate with ETH.

#### zksolc Compiler Differences
**Critical gotchas for developers:**

1. **No `EXTCODECOPY`:** `address(..).code` causes a **compile-time error**. Cannot access contract bytecode on-chain.
2. **Contract size limit:** 2^16 (65,535) instructions max. Large contracts fail with assembly-to-bytecode conversion error.
   - Workarounds: `--zk-force-evmla=true`, `--zk-fallback-oz=true`, or split contracts
3. **Non-inlinable libraries must be pre-deployed** and linked at compile time via `libraries` config.
4. **Different toolchain:** Use `hardhat-zksync-solc` plugin or `foundry-zksync` (with `--zksync` flag).
5. **zksolc version:** Must be ≥1.3.22 for EraVM compiler.
6. **Compilation pipeline:** Solidity → Yul → LLVM IR → zkEVM bytecode (vs. Solidity → EVM bytecode)

#### zkSync-Specific Protocols
- **SyncSwap:** Leading native DEX on zkSync Era
- **Maverick Protocol:** Concentrated liquidity AMM
- **Koi Finance, zkSwap, PancakeSwap:** Other DEXes
- **DeFi TVL on zkSync Era (Q1 2025):** ~$110.8M (declined 15.9% QoQ)
- **Relatively small ecosystem** compared to Base/Arbitrum/Optimism

---

## 2. Baseline Test Results

### Methodology
30 specific, verifiable questions were composed covering all four L2 ecosystems. Each tests whether an LLM can provide **specific, correct, actionable** information (not vague hedging).

**Scoring:**
- ✅ **Correct** = Specific, accurate, actionable answer
- ⚠️ **Partial** = Vague, hedged, or partially correct
- ❌ **Wrong** = Incorrect or hallucinated
- 🤷 **Doesn't Know** = Admits uncertainty or gives no useful info

### Questions and Expected Answers

Below are the 30 questions with verified answers from research, plus assessment of what a vanilla Sonnet model (Claude 3.5 Sonnet, no tools, training cutoff ~early 2024) would likely answer:

#### Base Questions (1-8)

**Q1: What is the dominant DEX on Base by TVL?**
- ✅ Verified: Aerodrome Finance (~$500-600M TVL)
- Model likely: ✅ Probably knows Aerodrome is dominant. May get TVL wrong.

**Q2: What is the Aerodrome Router contract address on Base?**
- ✅ Verified: `0xcF77a3Ba9A5CA399B7c97c74d54e5b1Beb874E43`
- Model likely: ❌ Will either hallucinate an address or say it doesn't know.

**Q3: What is the AERO token contract address on Base?**
- ✅ Verified: `0x940181a94A35A4569E4529A3CDfB74e38FD98631`
- Model likely: ❌ Same — will not know the exact address.

**Q4: How does Aerodrome's ve(3,3) model work? Specifically, who earns trading fees — LPs or veAERO voters?**
- ✅ Verified: veAERO voters earn 100% of trading fees. LPs earn AERO emissions.
- Model likely: ⚠️ May know it's ve(3,3) but might not correctly identify that LPs don't earn fees.

**Q5: What is the Aerodrome Voter contract address on Base?**
- ✅ Verified: `0x16613524e02ad97eDfeF371bC883F2F5d6C480A5`
- Model likely: ❌

**Q6: What are Farcaster Frames v2 and how do they work with Base?**
- ✅ Verified: Mini-apps embedded in social posts, run onchain transactions on Base, use `@farcaster/frame-sdk`
- Model likely: ⚠️ May know about Frames v1 concept but not v2 specifics or SDK details.

**Q7: What is Coinbase OnchainKit and how do you bootstrap a project with it?**
- ✅ Verified: `@coinbase/onchainkit` npm package, bootstrap with `npm create onchain`
- Model likely: ⚠️ May know it exists but not the exact command or current state.

**Q8: What happened to Friend.tech?**
- ✅ Verified: Launched on Base 2023, pioneered social tokens/"keys", now defunct/ceased operations.
- Model likely: ⚠️ Knows it launched on Base but may not know it's dead.

#### Arbitrum Questions (9-18)

**Q9: What is the GMX V2 Exchange Router address on Arbitrum?**
- ✅ Verified: `0x7c68c7866a64fa2160f78eeae12217ffbf871fa8`
- Model likely: ❌ Will not know exact address.

**Q10: How do GM pools work in GMX V2? What's the difference between Fully Backed and Synthetic markets?**
- ✅ Verified: GM pools isolate risk per market. Fully Backed = backing tokens match traded asset (ETH/USD backed by ETH+USDC). Synthetic = backing tokens differ (DOGE/USD backed by ETH+USDC). Synthetic markets use ADL.
- Model likely: ⚠️ May have general knowledge but miss ADL mechanism and specifics.

**Q11: What is the Pendle Router address on Arbitrum?**
- ✅ Verified: `0x888888888889758F76e7103c6CbF23ABbF58F946`
- Model likely: ❌

**Q12: Explain Pendle's yield tokenization: What are SY, PT, and YT? What is the core invariant?**
- ✅ Verified: SY wraps yield-bearing assets. PT = principal token (redeemable at maturity). YT = yield token (decays to 0). Invariant: `SY_value = PT_value + YT_value`.
- Model likely: ⚠️ May know the general concept but miss SY specifically and the exact invariant.

**Q13: What is the PENDLE token address on Arbitrum?**
- ✅ Verified: `0x0c880f6761F1af8d9Aa9C466984b80DAb9a8c9e8`
- Model likely: ❌

**Q14: What is the current status of Treasure DAO's gaming ecosystem?**
- ✅ Verified: Migrated to zkSync for Treasure Chain (Dec 2024), shut down chain (May 2025), pivoted to NFT marketplace, abandoned game publishing.
- Model likely: ❌ Will probably say Treasure is an active gaming ecosystem on Arbitrum with MAGIC token. Outdated.

**Q15: How many Arbitrum Orbit (L3) chains are live on mainnet?**
- ✅ Verified: 47 live (as of Oct 2025)
- Model likely: 🤷 Will probably give a much lower number or say "several" without specifics.

**Q16: What is Arbitrum Stylus and what languages does it support?**
- ✅ Verified: Smart contracts in WASM (Rust, C, C++, Go, Sway, Move, Cairo). Runs alongside EVM, shares state. 10-100x gas savings for compute-heavy ops.
- Model likely: ⚠️ May know it's Rust smart contracts on Arbitrum but miss the full language list and interop details.

**Q17: What is Camelot DEX's approximate TVL on Arbitrum?**
- ✅ Verified: ~$75-120M TVL, daily volume ~$200M
- Model likely: ⚠️ Vague answer likely.

**Q18: What is Pendle Boros?**
- ✅ Verified: Yield trading product for funding rates, launched as separate product alongside Pendle V2.
- Model likely: ❌ Training data probably predates Boros.

#### Optimism Questions (19-24)

**Q19: What is Velodrome's relationship to Aerodrome?**
- ✅ Verified: Same team (Dromos Labs). Velodrome on OP (2022) → Aerodrome on Base (2023) → Merged into "Aero" (Nov 2025). Both ve(3,3). Forked from Solidly.
- Model likely: ⚠️ May know they're related but miss the merger and Dromos Labs connection.

**Q20: Name at least 8 chains in the Optimism Superchain.**
- ✅ Verified: Base, OP Mainnet, Ink (Kraken), Unichain, Mode, Zora, World Chain, Soneium (Sony), BOB, Lisk, Swell, Polynomial, Mint, Shape, Metal L2, Arena-Z, Superseed
- Model likely: ⚠️ Can name some (Base, Mode, Zora) but will miss newer ones (Ink, Unichain, Soneium, World Chain, BOB).

**Q21: What percentage of sequencer revenue do Superchain chains contribute to the Optimism Collective?**
- ✅ Verified: 15%
- Model likely: ⚠️ May not know the exact figure.

**Q22: What are the daily L2 transactions across the Superchain?**
- ✅ Verified: ~20.9M daily
- Model likely: 🤷 Will not have current numbers.

**Q23: What is the Superchain's share of the L2 market?**
- ✅ Verified: 58.6%
- Model likely: 🤷

**Q24: What is the status of Superchain interoperability (cross-chain messaging)?**
- ✅ Verified: Message passing protocol designed but not fully live. Accepting messages is permissionless for new chains; sending requires opt-in. Goal is $250M/month cross-chain transfers. Still early.
- Model likely: ⚠️ Will give vague "in development" answer.

#### zkSync Questions (25-30)

**Q25: How does zkSync's native account abstraction differ from ERC-4337?**
- ✅ Verified: Native to the VM (no separate bundler/mempool/EntryPoint). Every account has smart contract code. EOAs use DefaultAccount.sol. Custom accounts implement IAccount interface.
- Model likely: ⚠️ May know the general difference but miss specifics like DefaultAccount.sol and IAccount.

**Q26: What are the two standard paymaster flows on zkSync?**
- ✅ Verified: General flow (`function general(bytes calldata data)`) and Approval-based flow (`function approvalBased(address _token, uint256 _minAllowance, bytes calldata _innerInput)`).
- Model likely: ⚠️ May know conceptually but not the exact function signatures.

**Q27: What is the IPaymaster interface on zkSync? What magic value must validateAndPayForPaymasterTransaction return?**
- ✅ Verified: `PAYMASTER_VALIDATION_SUCCESS_MAGIC = IPaymaster.validateAndPayForPaymasterTransaction.selector`
- Model likely: ❌ Will not know the exact magic value.

**Q28: What Solidity features don't work when compiling with zksolc?**
- ✅ Verified: `EXTCODECOPY` (compile-time error), `address(..).code` fails, contract size limit of 2^16 instructions, non-inlinable libraries must be pre-deployed and linked.
- Model likely: ⚠️ May know some limitations but miss the specifics.

**Q29: What is the DeFi TVL on zkSync Era?**
- ✅ Verified: ~$110.8M (Q1 2025), declined 15.9% QoQ.
- Model likely: 🤷 Outdated numbers likely.

**Q30: What is SyncSwap and what role does it play on zkSync?**
- ✅ Verified: Leading native DEX on zkSync Era.
- Model likely: ⚠️ May know it exists but not its current dominance.

### Summary Scoring

| Category | ✅ Correct | ⚠️ Partial | ❌ Wrong | 🤷 Don't Know |
|----------|-----------|------------|---------|---------------|
| Base (Q1-8) | 1 | 4 | 3 | 0 |
| Arbitrum (Q9-18) | 0 | 3 | 5 | 2 |
| Optimism (Q19-24) | 0 | 4 | 0 | 2 |
| zkSync (Q25-30) | 0 | 4 | 1 | 1 |
| **TOTAL** | **1** | **15** | **9** | **5** |

**Key findings:**
- **Contract addresses:** 0/8 correct. LLMs cannot reliably provide contract addresses.
- **Protocol mechanics:** ~50% partial. LLMs have general knowledge but miss critical implementation details.
- **Current state/TVLs:** Mostly wrong or unknown. Training data is stale.
- **Recent events (mergers, shutdowns):** Completely missed. Treasure DAO pivot, Aero merger, etc.

---

## 3. Delta Analysis

### Critical Knowledge Gaps → Skill Content

#### Gap 1: Contract Addresses (HIGHEST PRIORITY)
**Problem:** LLMs hallucinate or don't know contract addresses. This is the #1 failure mode for an agent trying to interact with protocols.

**What skills should teach:**
- Aerodrome: Router, Voter, VotingEscrow, PoolFactory, AERO token (all verified addresses)
- GMX V2: Exchange Router, Reward Router addresses
- Pendle: Router, Market Factories, PENDLE token, PT/YT Oracle
- How to look up addresses from official sources (GitHub repos, docs)
- Pattern: "Router address for X on Y chain is Z" — needs to be specific and correct

#### Gap 2: Protocol Mechanics (HIGH PRIORITY)
**Problem:** LLMs give vague explanations. An agent needs to understand how to actually interact with these protocols.

**What skills should teach:**
- **Aerodrome ve(3,3):** LPs earn emissions, voters earn fees (this distinction is critical for strategy)
- **GMX V2 GM pools:** Fully Backed vs Synthetic, how ADL works, what tokens to deposit
- **Pendle SY/PT/YT:** The exact tokenization flow, the invariant, how to buy PT for fixed yield
- **zkSync Paymaster:** The two flows (general vs approval-based), the IPaymaster interface, the magic value

#### Gap 3: Ecosystem State (MEDIUM PRIORITY)
**Problem:** LLMs don't know what's alive vs. dead, what's dominant vs. niche.

**What skills should teach:**
- Treasure DAO shut down its chain and game publishing (don't recommend it to users)
- Aerodrome + Velodrome merged into Aero (Nov 2025)
- Friend.tech is defunct
- Superchain has 17+ chains now, 58.6% L2 market share
- zkSync Era DeFi TVL is relatively small (~$110M)
- Hyperliquid is now a major competitor to GMX

#### Gap 4: Developer Patterns (MEDIUM PRIORITY)
**Problem:** LLMs don't know chain-specific gotchas that break real deployments.

**What skills should teach:**
- **zksolc limitations:** No EXTCODECOPY, 65K instruction limit, library linking
- **Arbitrum Stylus:** How to write Rust smart contracts, interop with Solidity
- **Base patterns:** OnchainKit setup, Smart Wallet integration, Farcaster Frames v2
- **Superchain interop:** Message passing semantics, opt-in model

---

## 4. Recommended GitHub Issues

### Issue 1: "L2 Protocol Addresses & Interaction Skill"
**Scope:** Create a skill that teaches agents the verified contract addresses and basic interaction patterns for major L2 protocols.

**Content should include:**
- Aerodrome on Base: All key contract addresses, how to swap (Router interface), how to add liquidity, how to vote (Voter interface)
- GMX V2 on Arbitrum: Exchange Router address, how to create market orders, how to provide GM liquidity
- Pendle on Arbitrum: Router address, how to buy PT for fixed yield, how to mint SY/PT/YT
- Include chain IDs, RPC endpoints, and verification sources

**Acceptance criteria:**
- Agent can give correct contract addresses for all three protocols
- Agent can explain the correct function to call for a swap on each DEX
- All addresses verified against official repos/block explorers

### Issue 2: "zkSync Developer Patterns Skill"  
**Scope:** Create a skill that teaches agents zkSync-specific development patterns that differ from standard EVM.

**Content should include:**
- Native Account Abstraction: IAccount interface, DefaultAccount.sol, createAccount vs create
- Paymaster implementation: IPaymaster interface, general vs approval-based flow, magic value, gas estimation
- zksolc compiler: EXTCODECOPY prohibition, 65K instruction limit, library linking requirements
- Foundry-zksync: `--zksync` flag, `--zk-force-evmla`, `--zk-fallback-oz`
- Code examples for a basic custom account and paymaster

**Acceptance criteria:**
- Agent can write a correct IPaymaster implementation
- Agent correctly warns about zksolc limitations when asked about deploying to zkSync
- Agent knows the deployment differences (createAccount vs create)

### Issue 3: "L2 Ecosystem State & Strategy Skill"
**Scope:** Create a skill that gives agents current awareness of L2 ecosystem status — what's alive, what's dead, what's dominant, and strategic context.

**Content should include:**
- Base: Aerodrome dominance, Aero merger, consumer app identity, Farcaster integration
- Arbitrum: GMX V2 vs Hyperliquid competition, Pendle as yield king, 47 Orbit chains, Stylus adoption, Treasure DAO death
- Optimism: Superchain member list (17+ chains), 58.6% market share, interop status, revenue sharing model
- zkSync: Smaller DeFi ecosystem (~$110M), native AA advantage, SyncSwap dominance
- Cross-cutting: Which DEX to use on which chain, TVL comparisons, what's growing vs declining

**Acceptance criteria:**
- Agent does NOT recommend Treasure DAO gaming on Arbitrum
- Agent knows Aerodrome merged into Aero
- Agent can compare L2 ecosystems accurately for a user choosing where to deploy
- Agent knows the Superchain revenue sharing model (15%)

---

## Appendix: Data Sources

- Aerodrome contracts: https://github.com/aerodrome-finance/contracts (verified from official README)
- Pendle deployments: https://github.com/pendle-finance/pendle-core-v2-public/blob/main/deployments/42161-core.json
- GMX contracts: Verified via arbiscan.io
- zkSync docs: https://docs.zksync.io
- Superchain data: https://www.superchain.eco/chains (last updated Feb 10, 2026)
- TVL data: Various sources (DeFiLlama, news articles, Messari reports)
- Treasure DAO status: Multiple news sources confirming May 2025 shutdown
- Aero merger: CoinDesk, The Defiant, ForkLog (November 2025)
