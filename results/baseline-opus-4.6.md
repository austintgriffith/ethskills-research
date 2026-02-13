

# Ethereum Baseline Knowledge Audit — Claude Opus 4.6

**Date:** 2026-02-13
**Model:** Claude Opus 4.6
**Rules:** No tools used. All answers from training data only. Uncertainty marked explicitly.

---

## GAS & COSTS

### 1. What does it cost to deploy a simple ERC-20 token on Ethereum mainnet today?

A minimal ERC-20 contract (like an OpenZeppelin standard token with no extra features) costs roughly **1–2 million gas** to deploy. At typical gas prices:

- **Gas used:** ~1,200,000–1,500,000 for a basic OpenZeppelin ERC-20
- **At ~20 gwei gas price:** ~0.024–0.03 ETH
- **At ~$2,500 ETH:** roughly **$60–$75**
- **At high congestion (50+ gwei):** could be $150–$300+

If you're deploying a more complex token (with permit, snapshots, governance, etc.), gas could be 2–3M+, pushing costs higher.

**Caveat:** Gas prices fluctuate enormously. I've seen mainnet as low as 5-8 gwei during quiet periods and 100+ gwei during NFT mints or market events. My training data may not reflect the exact current gas market as of February 2026.

### 2. What is the current typical gas price on Ethereum mainnet?

**UNSURE about the exact current price as of February 2026.**

From my training data: After EIP-1559 and the Merge, and especially after EIP-4844 (Proto-Danksharding, March 2024), mainnet gas prices have generally been moderate. During 2024, typical base fees ranged from **10–30 gwei** during normal periods, with spikes to 50-100+ gwei during congestion.

Post-Dencun (EIP-4844), L2 costs dropped dramatically, but mainnet gas itself wasn't directly reduced by that upgrade. Mainnet gas has trended lower overall due to activity shifting to L2s.

**My best estimate:** 10–30 gwei as a typical range, but I genuinely don't know the February 2026 number.

### 3. What does a simple ETH transfer cost in USD?

A simple ETH transfer costs exactly **21,000 gas**.

- At 20 gwei: 21,000 × 20 gwei = 420,000 gwei = 0.00042 ETH
- At $2,500/ETH: **~$1.05**
- At 10 gwei: **~$0.52**
- At 50 gwei: **~$2.63**

**UNSURE** of current ETH price in February 2026. The USD cost scales linearly with both gas price and ETH price.

### 4. How does mainnet gas compare to L2s like Arbitrum, Optimism, Base?

After EIP-4844 (Dencun upgrade, March 2024), L2 costs dropped **dramatically** — often by 90-99%:

| Network | Simple transfer cost | Relative to mainnet |
|---------|---------------------|-------------------|
| Ethereum mainnet | ~$0.50–$3.00 | 1x |
| Arbitrum | ~$0.01–$0.10 | ~10-100x cheaper |
| Optimism | ~$0.01–$0.10 | ~10-100x cheaper |
| Base | ~$0.001–$0.05 | ~50-1000x cheaper |

**Key reasons L2s are cheaper:**
- They batch many transactions into a single L1 submission
- EIP-4844 introduced "blob" transactions, giving L2s a dedicated cheap data channel
- L2 execution happens off-chain; only compressed data/proofs go to L1

**Base** tends to be the cheapest because Coinbase subsidizes some costs and it has high throughput. Arbitrum and Optimism are comparable. All three got massively cheaper post-4844.

For contract deployment, the savings are even more dramatic — deploying an ERC-20 on Base or Arbitrum might cost **$0.10–$1.00** vs $50-100+ on mainnet.

---

## WALLETS

### 5. How do you set up a wallet for an AI agent to use?

Several approaches, from simple to sophisticated:

**Simple approach — raw private key:**
```javascript
import { ethers } from "ethers";
const wallet = ethers.Wallet.createRandom();
console.log("Address:", wallet.address);
console.log("Private key:", wallet.privateKey);
// Connect to provider
const connected = wallet.connect(provider);
```

**Key considerations for AI agents:**
- **Key storage:** Private key must be stored securely (environment variable, secrets manager like AWS KMS, HashiCorp Vault). Never in code or logs.
- **Funding:** The agent wallet needs ETH for gas. You can fund it from a treasury wallet.
- **Spending limits:** The raw wallet approach gives the agent unlimited spending power over its funds. This is dangerous.

**Better approach — Safe + agent as signer:**
- Create a Safe multisig where the AI agent is one signer but a human must co-sign
- Or use a Safe with spending limits module

**Best approach — Account Abstraction (ERC-4337):**
- Create a smart contract wallet for the agent
- Set spending limits, whitelisted contracts, session keys
- Agent can act within constraints without needing approval for every tx
- Can use "session keys" that expire and have scoped permissions

**Practical pattern:**
1. Generate an EOA (externally owned account) for the agent
2. Fund it with a small amount of ETH
3. Set up monitoring/alerts on the wallet
4. Implement application-level spending limits
5. Use a separate hot wallet with limited funds, not the treasury

### 6. What is a Gnosis Safe / Safe multisig and how do you set one up?

**Safe** (formerly Gnosis Safe) is the most widely used smart contract wallet / multisig on Ethereum. It holds **a massive amount of value** — hundreds of billions in total across all Safes.

**How it works:**
- It's a smart contract deployed on-chain that acts as a wallet
- Multiple "owners" (signers) are configured
- A "threshold" (M-of-N) determines how many must sign to execute a transaction
- Example: 3-of-5 means any 3 of the 5 owners must approve

**Key features:**
- **Modules:** Extend functionality (spending allowances, recurring payments, etc.)
- **Guards:** Pre/post transaction checks
- **Delegates:** Can propose transactions without being an owner
- **Batched transactions:** Execute multiple actions atomically
- **Off-chain signatures:** Collect signatures without gas until execution

**Setting one up:**
1. Go to **app.safe.global** (the official web interface)
2. Connect your wallet
3. Click "Create New Safe"
4. Choose the network (mainnet, Arbitrum, etc.)
5. Add owner addresses
6. Set the confirmation threshold (e.g., 2-of-3)
7. Review and deploy (costs gas — it's deploying a contract)

**Programmatically (Safe SDK):**
```javascript
import Safe from "@safe-global/protocol-kit";

const safe = await Safe.init({
  provider: rpcUrl,
  signer: deployerPrivateKey,
  predictedSafe: {
    safeAccountConfig: {
      owners: ["0xOwner1...", "0xOwner2...", "0xOwner3..."],
      threshold: 2,
    },
  },
});
const deploymentTx = await safe.createSafeDeploymentTransaction();
```

**For AI agents:** Safe is ideal because you can make the agent a signer with limited permissions via modules, while humans retain ultimate control.

### 7. What is account abstraction (ERC-4337) and what's the current state?

**ERC-4337** is a standard for **account abstraction without protocol changes**. It was proposed by Vitalik Buterin, Yoav Weiss, and others.

**Core idea:** Instead of only EOAs (externally owned accounts, controlled by private keys) initiating transactions, smart contracts can be the primary accounts. This enables:

- **Gas sponsorship:** Someone else pays gas (paymasters)
- **Batched transactions:** Multiple actions in one user operation
- **Custom signature schemes:** Not just secp256k1 — use passkeys, multisig, social recovery
- **Session keys:** Scoped, time-limited permissions
- **Custom validation logic:** Any arbitrary logic to validate a transaction

**Architecture:**
- **UserOperation:** A pseudo-transaction struct submitted by the user
- **Bundler:** Collects UserOperations and submits them on-chain
- **EntryPoint:** A singleton contract that processes UserOperations
- **Smart Contract Wallet (Account):** The user's account, validates and executes
- **Paymaster:** Optional contract that sponsors gas

**Current state (as of my training data):**
- EntryPoint v0.6 and v0.7 are deployed on mainnet and most L2s
- Major infrastructure providers: Pimlico, Alchemy (Account Kit), Biconomy, ZeroDev, Stackup
- **Adoption is growing but not yet mainstream** — most users still use EOAs (MetaMask)
- L2s are the natural home for 4337 because gas costs are low enough to absorb the overhead
- **ERC-7702** (part of Pectra upgrade, expected 2025) complements 4337 by allowing EOAs to temporarily delegate to smart contract code, blurring the line further

**UNSURE** about exact state of ERC-7702 / Pectra as of February 2026 — it was planned for late 2025 but Ethereum upgrades often slip.

**For AI agents specifically:** ERC-4337 is very promising because:
- You can create wallets with programmable spending limits
- Session keys let agents operate within defined parameters
- Paymasters can abstract gas away from the agent entirely

---

## L2s

### 8. What L2s exist on Ethereum? List them with key differences.

**Optimistic Rollups:**

| L2 | Key Details |
|----|------------|
| **Arbitrum One** | Largest L2 by TVL. Nitro tech stack. Full EVM equivalence. Governed by ARB token & DAO. Dominant DeFi ecosystem. |
| **Optimism (OP Mainnet)** | OP Stack-based. Bedrock architecture. Part of the "Superchain" vision. OP token governance. |
| **Base** | Built on OP Stack by Coinbase. No native token. Very cheap. High throughput. Huge consumer/social app adoption. |
| **Blast** | OP Stack fork. Native yield on ETH and stablecoins. Launched with huge TVL from points campaign. |
| **Mantle** | OP Stack-based. Backed by BitDAO treasury. Uses MNT token. |

**ZK Rollups:**

| L2 | Key Details |
|----|------------|
| **zkSync Era** | By Matter Labs. Custom ZK-EVM (type-4). Solidity compiles via zksolc. Some EVM differences. |
| **StarkNet** | By StarkWare. Uses Cairo language (not EVM). STARK proofs. Different programming model entirely. |
| **Polygon zkEVM** | By Polygon. Type-2 ZK-EVM (high EVM compatibility). Part of Polygon 2.0 ecosystem. |
| **Scroll** | Type-2 ZK-EVM. Community-driven. High EVM compatibility. |
| **Linea** | By Consensys (MetaMask team). Type-2 ZK-EVM. Good MetaMask integration. |
| **Taiko** | Type-1 ZK-EVM (fully Ethereum-equivalent). Based Rollup concept (uses L1 validators for sequencing). |

**Key differences between Optimistic vs ZK:**
- **Optimistic:** Faster/cheaper to build, fraud proofs with 7-day challenge window for withdrawals, more mature DeFi ecosystems currently
- **ZK:** Validity proofs (withdrawals can be faster, ~hours), mathematically proven correctness, higher computational overhead for proving, still maturing

**Others worth mentioning:**
- **Arbitrum Nova:** AnyTrust chain (cheaper, less decentralized, for gaming/social)
- **Zora:** OP Stack, focused on creator/NFT ecosystem
- **Mode:** OP Stack, DeFi-focused
- **Manta Pacific:** ZK-focused with privacy features

### 9. How do you deploy a contract on an L2 vs mainnet?

**For most L2s, it's nearly identical to mainnet.** That's the whole point of EVM compatibility.

**Steps (same for mainnet and most L2s):**

1. Write your Solidity contract
2. Compile with solc/Hardhat/Foundry
3. Change the RPC URL to the L2's RPC endpoint
4. Deploy

**Hardhat example:**
```javascript
// hardhat.config.js
module.exports = {
  networks: {
    mainnet: {
      url: "https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY",
      accounts: [PRIVATE_KEY],
    },
    arbitrum: {
      url: "https://arb1.arbitrum.io/rpc",
      accounts: [PRIVATE_KEY],
    },
    base: {
      url: "https://mainnet.base.org",
      accounts: [PRIVATE_KEY],
    },
    optimism: {
      url: "https://mainnet.optimism.io",
      accounts: [PRIVATE_KEY],
    },
  },
};
```

```bash
npx hardhat deploy --network arbitrum
```

**Foundry:**
```bash
forge create --rpc-url https://arb1.arbitrum.io/rpc \
  --private-key $PRIVATE_KEY \
  src/MyToken.sol:MyToken
```

**Key differences to be aware of:**
- **Gas costs:** Much cheaper on L2
- **Block times:** Different (Arbitrum: 0.25s, Optimism: 2s, mainnet: 12s)
- **Precompiles:** Some L2s have additional precompiles
- **L1 data costs:** Still a factor, even on L2s (compresses calldata)
- **zkSync:** Requires a custom compiler (`zksolc`), some Solidity features may not work
- **StarkNet:** Completely different — you write in Cairo, not Solidity

**You need ETH on the L2** to pay gas. Bridge ETH first (see question 10).

### 10. How do you bridge assets between mainnet and L2s?

**Native bridges (canonical):**
Each L2 has an official bridge:
- **Arbitrum:** bridge.arbitrum.io
- **Optimism:** app.optimism.io/bridge
- **Base:** bridge.base.org
- **zkSync:** bridge.zksync.io

**How native bridges work:**
1. Deposit: Send assets to the L2's bridge contract on L1 → appear on L2 (usually 10-15 min for optimistic rollups)
2. Withdraw: Initiate on L2 → wait for challenge period:
   - **Optimistic rollups:** ~7 days to withdraw back to L1
   - **ZK rollups:** Hours (once proof is verified)

**Third-party bridges (faster):**
- **Across Protocol:** Fast bridge, uses relayers
- **Hop Protocol:** Multi-L2 bridge
- **Stargate (LayerZero):** Cross-chain liquidity
- **Synapse:** Multi-chain bridge
- **Orbiter Finance:** Lightweight bridge

These are faster (minutes) because they use liquidity pools and relayers rather than waiting for the native bridge's proving/challenge period.

**Programmatically (for an agent):**
```javascript
// Using the Arbitrum SDK
import { Erc20Bridger, getL2Network } from "@arbitrum/sdk";

const l2Network = await getL2Network(arbitrumProvider);
const bridger = new Erc20Bridger(l2Network);

// Deposit ETH from L1 to L2
const depositTx = await bridger.depositEth({
  amount: ethers.utils.parseEther("0.1"),
  l1Signer: l1Wallet,
});
```

**For agents:** Use third-party bridge aggregators/APIs for fastest execution. The canonical bridge is safest but slowest for withdrawals.

---

## STANDARDS

### 11. List the major Ethereum token standards and when to use each.

| Standard | Type | Use Case |
|----------|------|----------|
| **ERC-20** | Fungible token | Currencies, governance tokens, utility tokens. The most fundamental standard. |
| **ERC-721** | Non-fungible token (NFT) | Unique items: art, collectibles, domain names, real-world asset titles. Each token has a unique ID. |
| **ERC-1155** | Multi-token | Gaming items, mixed collections. One contract handles both fungible and non-fungible tokens. Batch transfers. Gas efficient. |
| **ERC-777** | Advanced fungible | Like ERC-20 but with hooks (receive callbacks). Mostly deprecated due to reentrancy risks. |
| **ERC-4626** | Tokenized vault | Standard interface for yield-bearing vaults. Wrap any yield strategy into a standard vault token. Huge in DeFi. |
| **ERC-2981** | NFT royalties | Standard royalty info for NFTs. Query what % goes to creator on secondary sales. |
| **ERC-2612** | Permit (gasless approval) | Extends ERC-20 with off-chain signatures for approvals. User signs a message instead of sending an approve transaction. |
| **ERC-6551** | Token-bound accounts | NFTs that own assets. Each NFT gets its own smart contract wallet. "Backpack" for NFTs. |
| **ERC-4337** | Account abstraction | Smart contract wallets (see question 7). Not a token standard per se, but a critical account standard. |

**When to use what:**
- Making a currency/point system → **ERC-20**
- Unique collectibles/deeds → **ERC-721**
- Game with many item types → **ERC-1155**
- Yield-bearing wrapper → **ERC-4626**
- NFTs that need to hold stuff → **ERC-6551**

### 12. What is ERC-8004?

**UNSURE.** I do not have clear information about ERC-8004 in my training data. ERC numbers in the 8000+ range were being proposed in 2024-2025, but I cannot confidently describe what ERC-8004 specifically covers. It may have been proposed after my training data cutoff or may be too niche for me to have solid recall on.

I don't want to guess — this is exactly the kind of thing where I should say I don't know.

### 13. What is x402?

**UNSURE.** I don't have clear information about "x402" as a named protocol or standard. A few possibilities:

- It could relate to **HTTP 402 Payment Required** — the HTTP status code that was reserved for future use involving digital payments. There have been projects attempting to build on this concept for web3 micropayments (pay-per-request APIs).
- It could be a specific project/protocol that emerged after my training cutoff or that I don't have detailed knowledge of.

I cannot give a confident, detailed answer here.

---

## TOOLS

### 14. What are the current best tools/frameworks for building on Ethereum?

**Smart Contract Development:**
- **Foundry** (forge, cast, anvil, chisel) — Rust-based, fastest, most popular for serious development. Tests in Solidity. Extremely fast compilation and testing.
- **Hardhat** — JavaScript/TypeScript based. Huge plugin ecosystem. HRE (Hardhat Runtime Environment). Still very widely used.
- **Remix IDE** — Browser-based IDE. Great for learning and quick prototyping.

**Frontend / Full-Stack:**
- **Scaffold-ETH 2** — Full-stack dApp starter kit (see Q15)
- **wagmi** — React hooks for Ethereum (connects, reads, writes, events)
- **viem** — TypeScript Ethereum library (low-level, wagmi is built on it)
- **RainbowKit** / **ConnectKit** / **Web3Modal** — Wallet connection UI components
- **ethers.js v6** — Still widely used, though viem is gaining ground

**Libraries:**
- **OpenZeppelin Contracts** — Battle-tested, audited smart contract library. The standard for token implementations, access control, upgrades, etc.
- **Solmate** (by Rari Capital/transmissions11) — Gas-optimized alternatives to OpenZeppelin
- **Solady** — Even more gas-optimized, by Vectorized

**Testing & Security:**
- **Foundry fuzzing** — Built-in fuzz testing
- **Slither** — Static analysis by Trail of Bits
- **Mythril** — Symbolic execution
- **Echidna** — Property-based fuzzing
- **Aderyn** — UNSURE about details, newer tool

**Deployment & Infrastructure:**
- **Alchemy** — RPC provider, APIs, Account Kit
- **Infura** — RPC provider (Consensys)
- **QuickNode** — RPC provider
- **Tenderly** — Simulation, debugging, monitoring
- **The Graph** — Subgraph indexing (query on-chain data via GraphQL)

**Languages:**
- **Solidity** — Dominant smart contract language (~95%+ of EVM contracts)
- **Vyper** — Python-like alternative, used by Curve and some DeFi protocols
- **Huff** — Low-level EVM assembly language for maximum optimization

### 15. What is Scaffold-ETH 2 and how do you use it?

**Scaffold-ETH 2** is an open-source, full-stack Ethereum development toolkit created by **Austin Griffith** and the **BuidlGuidl** community. It's designed to get you from zero to a working dApp extremely fast.

**Tech stack:**
- **Next.js** (React frontend)
- **Hardhat** or **Foundry** (smart contract development)
- **wagmi + viem** (Ethereum interaction)
- **RainbowKit** (wallet connection)
- **TypeScript** throughout
- **DaisyUI / Tailwind** (styling)

**Key features:**
- **Hot reload contracts:** Deploy a contract, frontend auto-generates typed hooks
- **Debug page:** Auto-generated UI to interact with all your contract functions
- **Burner wallets:** Instant local testing without MetaMask
- **Contract hot reload:** Change contract, redeploy, UI updates automatically
- **Block explorer component:** Built-in local block explorer
- **Hooks:** `useScaffoldContractRead`, `useScaffoldContractWrite`, `useScaffoldEventHistory` — typed React hooks generated from your ABI

**How to use it:**
```bash
npx create-eth@latest
# Choose project name
# Choose Hardhat or Foundry
# cd into project

# Terminal 1: Start local chain
yarn chain

# Terminal 2: Deploy contracts
yarn deploy

# Terminal 3: Start frontend
yarn start
```

Then visit `localhost:3000` and you have a working dApp with wallet connection, contract interaction, and debugging tools.

**Why it matters:** It's probably the **fastest way to go from idea to prototype** on Ethereum. The BuidlGuidl community has built hundreds of projects with it. It's the primary teaching tool for Ethereum development in many contexts.

### 16. What is abi.ninja?

**abi.ninja** is a web tool that lets you **interact with any smart contract** on Ethereum (and other EVM chains) by providing the contract address and ABI.

**How it works:**
1. Go to abi.ninja
2. Paste a contract address
3. It fetches the ABI (from Etherscan or you can paste it manually)
4. It generates a UI for all the contract's functions — read and write
5. Connect your wallet and interact

**Think of it as:** A universal, lightweight Etherscan "Read/Write Contract" interface that works for any contract, even unverified ones (if you have the ABI).

**Built by** the Scaffold-ETH / BuidlGuidl ecosystem. It's essentially the contract debug page from Scaffold-ETH made into a standalone tool.

**For AI agents:** You could use abi.ninja-style functionality programmatically — given an ABI and address, generate the interface to call any function. The concept is: ABI + address = full contract interaction capability.

### 17. What are the best block explorers and how would an agent use them?

**Major block explorers:**

| Explorer | Networks | Key Features |
|----------|----------|-------------|
| **Etherscan** | Mainnet + many L2s (Arbiscan, Basescan, etc.) | The gold standard. Contract verification, ABI access, token tracking, internal txs. API available. |
| **Blockscout** | Open source, many networks | Self-hostable. Used by many L2s and testnets. Open API. |
| **Tenderly** | Multi-chain | Transaction simulation & debugging. Call traces. "What if" scenarios. |
| **Dune Analytics** | Multi-chain | SQL-based analytics on blockchain data. Dashboards. |
| **Arkham Intelligence** | Multi-chain | Entity labeling, wallet tracking, intelligence. |

**How an agent would use them:**

**Etherscan API** (most important for agents):
```bash
# Get contract ABI
https://api.etherscan.io/api?module=contract&action=getabi&address=0x...&apikey=KEY

# Get transaction status
https://api.etherscan.io/api?module=transaction&action=gettxreceiptstatus&txhash=0x...

# Get token balance
https://api.etherscan.io/api?module=account&action=tokenbalance&contractaddress=TOKEN&address=WALLET

# Get ETH balance
https://api.etherscan.io/api?module=account&action=balance&address=0x...
```

**For an AI agent specifically:**
1. **Verify contracts:** Check if a contract is verified, read source code
2. **Get ABIs:** Programmatically fetch ABIs for any verified contract
3. **Monitor wallets:** Track balances, transactions, token transfers
4. **Debug transactions:** Understand why a transaction failed
5. **Discover contracts:** Find proxy implementations, related contracts

Each L2 has its own Etherscan-flavor explorer (Arbiscan, Basescan, Optimistic Etherscan) with the same API format.

---

## DEFI / BUILDING BLOCKS

### 18. Explain Uniswap V4 — what's new, what are hooks, how would you build on it?

**Uniswap V4** is the latest version of Uniswap, representing a massive architectural change.

**What's new:**
- **Singleton contract:** All pools live in ONE contract (vs V3 where each pool was a separate contract). This dramatically reduces gas for multi-hop swaps and pool creation.
- **Flash accounting:** Balances are tracked with transient storage during a transaction, only settling net balances at the end. Huge gas savings.
- **Hooks:** The headline feature (see below).
- **Native ETH support:** Pools can use native ETH directly (no WETH wrapping needed).
- **Dynamic fees:** Hooks can implement custom fee logic.
- **Donate:** LPs can receive donations (tips) directly in pools.

**Hooks:**
Hooks are external contracts that execute custom logic at specific points in a pool's lifecycle:
- `beforeInitialize` / `afterInitialize`
- `beforeAddLiquidity` / `afterAddLiquidity`
- `beforeRemoveLiquidity` / `afterRemoveLiquidity`
- `beforeSwap` / `afterSwap`
- `beforeDonate` / `afterDonate`

**Hook address encoding:** The hook's address determines which callbacks are active (specific bits in the address must be set). You mine/CREATE2 for an address with the right bits.

**What you can build with hooks:**
- **TWAMM (Time-Weighted Average Market Maker):** Large orders spread over time
- **Limit orders:** Execute when price hits a target
- **Dynamic fees:** Adjust fees based on volatility, MEV, time of day
- **On-chain oracles:** Custom oracle logic
- **Loyalty rewards:** Track and reward frequent traders
- **KYC/compliance pools:** Restrict who can trade
- **Auto-rebalancing:** LP positions that adjust themselves
- **MEV redistribution:** Capture MEV and return it to LPs

**Building on V4:**
```solidity
import {BaseHook} from "v4-periphery/BaseHook.sol";

contract MyHook is BaseHook {
    function getHookPermissions() public pure override returns (Hooks.Permissions memory) {
        return Hooks.Permissions({
            beforeSwap: true,
            afterSwap: true,
            // ... other flags
        });
    }

    function beforeSwap(address, PoolKey calldata, IPoolManager.SwapParams calldata, bytes calldata)
        external override returns (bytes4) {
        // Custom logic before each swap
        return BaseHook.beforeSwap.selector;
    }
}
```

**UNSURE** about exact V4 launch date — it was expected around late 2024 or early 2025. It may or may not be fully live on mainnet as of February 2026.

### 19. How would you build a yield vault on Arbitrum? What protocols would you use?

**Architecture:**

Build an **ERC-4626** compliant vault — this is the standard vault interface that composes with everything.

**Steps:**

1. **Choose a yield strategy.** Options on Arbitrum:

   - **Aave V3 (Arbitrum):** Deposit assets, earn lending yield
   - **GMX:** Provide liquidity to GLP/GM pools for trading fee yield
   - **Radiant Capital:** Lending/borrowing with cross-chain emissions
   - **Pendle:** Fixed/variable yield trading
   - **Camelot DEX:** LP yield with concentrated liquidity
   - **Silo Finance:** Isolated lending markets
   - **Jones DAO:** Leveraged yield strategies on GMX

2. **Build the vault:**
```solidity
import {ERC4626} from "@openzeppelin/contracts/token/ERC20/extensions/ERC4626.sol";

contract YieldVault is ERC4626 {
    constructor(IERC20 asset) ERC4626(asset) ERC20("Vault Token", "vTOKEN") {}

    function totalAssets() public view override returns (uint256) {
        // Return total value: vault balance + deployed capital in strategy
        return asset.balanceOf(address(this)) + strategyBalance();
    }

    function _deposit(address caller, address receiver, uint256 assets, uint256 shares)
        internal override {
        super._deposit(caller, receiver, assets, shares);
        _deployToStrategy(assets);
    }

    function _deployToStrategy(uint256 amount) internal {
        // Deposit into Aave, GMX, etc.
    }
}
```

3. **Common design patterns:**
   - **Harvester:** Periodically claim rewards, swap to base asset, compound
   - **Fee structure:** Management fee (e.g., 2%) + performance fee (e.g., 20%)
   - **Emergency withdrawal:** Admin function to pull funds from strategy
   - **Strategy rotation:** Ability to switch strategies

**Protocols you'd interact with:**
- **Aave V3** for lending yield
- **Uniswap V3 / Camelot** for swaps (harvesting rewards back to base asset)
- **Chainlink** for price feeds
- **Gelato / Chainlink Automation** for automated harvesting

### 20. What is a no-loss prediction market and how would you build one?

**Concept:** A prediction market where participants can never lose their principal. They stake funds, earn yield on the total pool, and the yield is distributed to the winners.

**How it works:**
1. Users deposit (e.g., 100 USDC each) into a pool and choose an outcome (Yes/No)
2. All deposits go into a yield-bearing protocol (Aave, Compound, etc.)
3. While the market is open, the pooled deposits earn yield
4. When the outcome resolves:
   - **Everyone gets their principal back** (no loss!)
   - **Winners split the yield** earned by the entire pool
   - **Losers get $0 yield** but keep their principal

**Building it:**

```solidity
contract NoLossPredictionMarket {
    IERC4626 public yieldVault;  // e.g., Aave USDC vault
    mapping(address => uint256) public deposits;
    mapping(address => bool) public predictions; // true = Yes, false = No

    uint256 public totalYesDeposits;
    uint256 public totalNoDeposits;
    bool public resolved;
    bool public outcome;

    function predict(bool _outcome, uint256 amount) external {
        USDC.transferFrom(msg.sender, address(this), amount);
        yieldVault.deposit(amount, address(this));  // Deploy to yield

        deposits[msg.sender] += amount;
        predictions[msg.sender] = _outcome;

        if (_outcome) totalYesDeposits += amount;
        else totalNoDeposits += amount;
    }

    function resolve(bool _outcome) external onlyOracle {
        outcome = _outcome;
        resolved = true;
    }

    function withdraw() external {
        require(resolved);
        uint256 principal = deposits[msg.sender];

        // Everyone gets principal back
        uint256 payout = principal;

        // Winners also get yield
        if (predictions[msg.sender] == outcome) {
            uint256 totalYield = yieldVault.totalAssets() - (totalYesDeposits + totalNoDeposits);
            uint256 winnerPool = outcome ? totalYesDeposits : totalNoDeposits;
            payout += (totalYield * principal) / winnerPool;
        }

        yieldVault.withdraw(payout, msg.sender, address(this));
    }
}
```

**Key components needed:**
- **Yield source:** Aave, Compound, or any ERC-4626 vault
- **Oracle for resolution:** Chainlink, UMA, Reality.eth, or custom
- **Incentive design:** Winners' share of yield must be compelling enough to attract deposits

**Historical reference:** **PoolTogether** used this exact concept for "no-loss lottery" — everyone deposits, one winner gets all the yield, everyone else gets principal back.

### 21. List the major DeFi protocols and what they do

| Protocol | Category | What It Does |
|----------|----------|-------------|
| **Aave** | Lending/Borrowing | Deposit assets to earn yield, borrow against collateral. Flash loans. V3 is the latest, multi-chain. |
| **Compound** | Lending/Borrowing | Similar to Aave but simpler. Pioneer of algorithmic interest rates. V3 (Comet) simplified to single-borrow-asset markets. |
| **MakerDAO (now Sky?)** | Stablecoin / Lending | Issues DAI stablecoin against collateral. The OG DeFi protocol. RWA (real world asset) integration. **UNSURE** about rebrand status to "Sky" as of Feb 2026. |
| **Uniswap** | DEX (AMM) | Largest decentralized exchange. V3 has concentrated liquidity. V4 adds hooks (see Q18). |
| **Curve** | DEX (Stableswap) | Optimized for stable-to-stable swaps (low slippage). CRV token wars. crvUSD stablecoin. |
| **Yearn Finance** | Yield Aggregator | Auto-compounding vault strategies. Deposits into the best yield opportunities. |
| **Lido** | Liquid Staking | Stake ETH, receive stETH (liquid staking token). Largest ETH staker. |
| **Rocket Pool** | Liquid Staking | Decentralized ETH staking. rETH token. Permissionless node operation. |
| **EigenLayer** | Restaking | Re-stake stETH/ETH to secure additional services (AVSs). Novel but controversial concept. |
| **Pendle** | Yield Trading | Separates yield into principal tokens (PT) and yield tokens (YT). Trade future yield. |
| **1inch** | DEX Aggregator | Routes trades across multiple DEXs for best price. |
| **Balancer** | DEX (Weighted Pools) | Flexible AMM with custom pool weights (not just 50/50). |
| **Synthetix** | Synthetic Assets | Create synthetic versions of any asset (stocks, commodities, crypto). Perps trading via Kwenta. |
| **GMX** | Perps DEX | Decentralized perpetual futures trading. Big on Arbitrum. |
| **dYdX** | Perps DEX | Another major perps platform. Moved to its own Cosmos chain. |
| **Convex Finance** | Curve Booster | Boost Curve yields without locking CRV yourself. Meta-governance. |
| **Frax Finance** | Stablecoin | Fractional-algorithmic stablecoin. Also does liquid staking (sfrxETH). |
| **Morpho** | Lending Optimizer | Peer-to-peer matching layer on top of Aave/Compound for better rates. Morpho Blue for modular lending. |
| **Chainlink** | Oracle | Price feeds, VRF (randomness), Automation, CCIP (cross-chain). Not DeFi itself but critical infrastructure for all DeFi. |

---

## WHY ETHEREUM

### 22. Why should someone build on Ethereum vs Solana?

**Arguments for Ethereum:**

1. **Decentralization & Security:** Ethereum has hundreds of thousands of validators, the most decentralized smart contract platform by far. Solana has had multiple extended outages; Ethereum mainnet has had near-100% uptime since the Merge.

2. **Lindy effect / Network effects:** Most DeFi TVL, most developers, most tooling, most audited code, most battle-tested infrastructure. More composable building blocks.

3. **L2 ecosystem:** Ethereum's scaling strategy (L2s) gives you multiple execution environments that all settle to the same security layer. You can choose Arbitrum for DeFi, Base for consumer apps, etc. — all backed by Ethereum security.

4. **Standards & composability:** ERC-20, ERC-721, ERC-4626, etc. are deeply embedded. Every wallet, every tool, every protocol understands them.

5. **EVM is the standard:** The EVM is the most widely adopted smart contract execution environment. Code once, deploy everywhere (all L2s, all EVM chains).

6. **Credible neutrality:** Ethereum has no corporate owner. Solana has significant VC/Foundation influence (though it's getting more decentralized).

7. **Developer tooling maturity:** Foundry, Hardhat, Scaffold-ETH, etc. are extremely mature. Solana's tooling (Anchor) is good but less mature.

**Arguments for Solana (being honest):**
- Higher raw throughput on L1 (faster transactions)
- Lower fees on L1 without needing L2 complexity
- Better for consumer/mobile apps currently (Saga phone, mobile wallet ecosystem)
- Simpler mental model (no L1/L2 distinction)
- Strong memecoin and consumer trading culture

**Bottom line:** Ethereum for security-critical, composable, long-term projects. Solana for high-throughput consumer apps where some centralization trade-offs are acceptable.

### 23. Why should someone build on Ethereum vs not using a blockchain at all?

**Use Ethereum when you need:**

1. **Trustless, permissionless value transfer:** No bank, no payment processor, no intermediary. Anyone can send value to anyone, 24/7, globally.

2. **Programmable money:** Smart contracts let you encode financial logic that executes automatically, transparently, and without human intervention. A traditional database can't do "if X happens, automatically pay Y" without trusting the database operator.

3. **Composability:** Your protocol can interact with every other protocol permissionlessly. Build on top of Uniswap, Aave, etc. without asking permission or signing business deals.

4. **Censorship resistance:** No single entity can stop your application or freeze your users' funds (at the contract level).

5. **Global, open state:** One shared database that anyone can read, write to, and verify. No API keys needed to read blockchain state.

6. **Self-custody:** Users actually own their assets. No platform risk (your account can't be "banned" from owning your tokens).

7. **Credible commitment:** A smart contract can make promises that are enforced by code and cryptography, not by lawyers and courts.

**Don't use Ethereum when:**
- You don't need trust minimization (you trust the operator)
- Speed/cost are paramount and decentralization doesn't matter
- Privacy is critical (blockchains are public by default)
- Your users don't care about self-custody
- A regular database works fine

**The honest test:** "Does removing the trusted intermediary unlock meaningful value?" If yes → blockchain. If no → use a database.

### 24. What are Ethereum's honest weaknesses?

1. **Mainnet is expensive and slow.** 12-second blocks, $1-100+ per transaction. Yes, L2s fix this, but that adds complexity.

2. **L2 fragmentation.** Liquidity and users are split across Arbitrum, Optimism, Base, zkSync, etc. Bridging is annoying and risky. The "one Ethereum" story is more complex than "one Solana."

3. **UX is still terrible.** Seed phrases, gas management, chain switching, bridging, approvals, signing messages you can't read. Regular users find this baffling.

4. **MEV (Maximal Extractable Value).** Validators and searchers extract value from users through sandwich attacks, frontrunning, etc. Partially mitigated by Flashbots/MEV-Boost but still a real tax on users.

5. **Complexity.** The L1+L2+bridges+account abstraction+EIP-1559 stack is genuinely complicated. Solana's "one fast chain" is easier to reason about.

6. **Governance and coordination are slow.** Ethereum's decentralized governance means upgrades take years. The roadmap is long and incremental.

7. **Centralization risks at the edges:**
   - Most users interact via Infura/Alchemy (centralized RPC providers)
   - Most L2 sequencers are currently centralized
   - Most users use MetaMask (one wallet)
   - MEV-Boost has relay centralization

8. **Smart contract risk.** Bugs in smart contracts have caused billions in losses. Immutable code is a feature AND a bug.

9. **Regulatory uncertainty.** Unclear legal status of tokens, DeFi protocols, DAOs in most jurisdictions.

10. **Environmental FUD is gone (post-Merge PoS), but:**
    - Staking centralization is a real concern (Lido had >30% at one point)
    - Validator hardware requirements may increase over time

---

## BUILDING

### 25. Walk me through deploying a full dApp on Ethereum from scratch

**Step-by-step:**

#### 1. Setup (5 minutes)
```bash
npx create-eth@latest my-dapp
cd my-dapp
```
This gives you Scaffold-ETH 2 with Next.js + Hardhat/Foundry + wagmi.

#### 2. Write Your Smart Contract
```solidity
// packages/hardhat/contracts/MyContract.sol
pragma solidity ^0.8.19;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("My Token", "MTK") {
        _mint(msg.sender, 1000000 * 10**18);
    }
}
```

#### 3. Write Tests
```solidity
// Foundry
function test_InitialSupply() public {
    assertEq(token.totalSupply(), 1000000e18);
    assertEq(token.balanceOf(deployer), 1000000e18);
}
```

#### 4. Deploy Locally
```bash
yarn chain       # Start local Hardhat/Anvil node
yarn deploy      # Deploy contracts
yarn start       # Start frontend at localhost:3000
```

#### 5. Iterate on Frontend
Build your UI using scaffold-eth hooks:
```tsx
const { data: balance } = useScaffoldContractRead({
  contractName: "MyToken",
  functionName: "balanceOf",
  args: [address],
});
```

#### 6. Security
- Run Slither: `slither .`
- Write comprehensive tests (aim for 100% coverage)
- Consider an audit for anything handling real value

#### 7. Deploy to Testnet
```bash
# Add testnet config (Sepolia)
yarn deploy --network sepolia
```
- Get testnet ETH from a faucet
- Test thoroughly

#### 8. Deploy to Production (L2 recommended)
```bash
# Deploy to Base (cheap, fast)
yarn deploy --network base
```

#### 9. Verify Contract
```bash
npx hardhat verify --network base CONTRACT_ADDRESS
# Or forge verify-contract ...
```

#### 10. Deploy Frontend
```bash
yarn build
# Deploy to Vercel, Netlify, or IPFS
vercel deploy
```

#### 11. Post-Deployment
- Set up monitoring (Tenderly alerts)
- Index events with The Graph (subgraph)
- Add analytics
- Write documentation

**Total tools used:** Scaffold-ETH 2, Hardhat/Foundry, OpenZeppelin, Slither, Vercel, The Graph, Etherscan

### 26. What are common smart contract vulnerabilities and how do you audit for them?

**Top vulnerabilities:**

1. **Reentrancy**
   - Attacker's contract calls back into your contract before state is updated
   - **Fix:** Checks-Effects-Interactions pattern, ReentrancyGuard (OpenZeppelin), or use Solidity 0.8.x's built-in protections
   - Famous example: The DAO hack (2016)

2. **Integer Overflow/Underflow**
   - Mostly fixed in Solidity 0.8+ (built-in overflow checks)
   - Still possible with `unchecked {}` blocks

3. **Access Control Errors**
   - Missing `onlyOwner` or similar modifiers
   - Unprotected `initialize()` functions on proxies
   - **Fix:** Use OpenZeppelin AccessControl, test every function's access

4. **Oracle Manipulation**
   - Using spot prices (e.g., Uniswap reserve ratio) as oracles → flash loan attacks
   - **Fix:** Use Chainlink or TWAP oracles

5. **Flash Loan Attacks**
   - Borrow massive amounts, manipulate a market, profit, repay — all in one transaction
   - **Fix:** Design protocols to be resistant to single-block manipulation

6. **Front-running / MEV**
   - Transactions in mempool are visible; miners/validators can reorder
   - **Fix:** Commit-reveal schemes, Flashbots Protect, slippage limits

7. **Proxy / Upgrade Vulnerabilities**
   - Storage collision between proxy and implementation
   - Uninitialized implementation contracts
   - **Fix:** Use OpenZeppelin's transparent/UUPS proxy patterns correctly

8. **Unchecked External Calls**
   - Not checking return values of `transfer()`, `send()`, low-level `call()`
   - **Fix:** Use OpenZeppelin's SafeERC20 for token transfers

9. **Denial of Service**
   - Loops over unbounded arrays
   - Blocking withdrawals by reverting in receive()
   - **Fix:** Pull over push patterns, bounded loops

10. **Logic Errors**
    - Wrong math, incorrect state transitions, missing edge cases
    - **Fix:** Thorough testing, formal verification, fuzzing

**Audit process:**
1. **Static analysis:** Run Slither, Aderyn, Mythril
2. **Unit tests:** 100% line/branch coverage
3. **Fuzz testing:** Foundry fuzz tests, Echidna
4. **Manual review:** Line-by-line code reading
5. **Formal verification:** For critical math (Certora, Halmos)
6. **Professional audit:** Hire Trail of Bits, OpenZeppelin, Spearbit, Cantina, Code4rena (competitive audits)

### 27. What are the most famous Ethereum hacks and what can we learn from them?

| Hack | Year | Amount | What Happened | Lesson |
|------|------|--------|---------------|--------|
| **The DAO** | 2016 | ~$60M (3.6M ETH) | Reentrancy attack. Recursive calls drained funds before balance was updated. Led to the Ethereum/Ethereum Classic hard fork. | Reentrancy is the #1 smart contract vulnerability. Check-Effects-Interactions pattern. |
| **Parity Multisig (1st)** | 2017 | ~$30M | Unprotected `initWallet()` function. Attacker reinitialized the wallet and became the owner. | Access control on initialization. Every public function must be permission-checked. |
| **Parity Multisig (2nd)** | 2017 | ~$280M locked forever | Someone "accidentally" called `kill()` on the library contract, destroying it. All Parity multisigs that delegatecalled to it were bricked. | Shared library contracts are a single point of failure. Careful with `selfdestruct`. |
| **bZx Flash Loan** | 2020 | ~$1M | Flash loan + oracle manipulation. Used Uniswap spot price as oracle, manipulated it with borrowed funds. | Never use spot DEX prices as oracles. Use Chainlink or TWAPs. |
| **Harvest Finance** | 2020 | ~$34M | Flash loan oracle manipulation against Curve pools. Repeatedly exploited price impact. | Same oracle lesson. Economic attack simulations needed. |
| **Wormhole** | 2022 | ~$320M | Bridge vulnerability. Attacker bypassed signature verification on Solana side, minted fraudulent wrapped ETH. | Bridges are extremely high-risk. Cross-chain verification is hard. |
| **Ronin Bridge** | 2022 | ~$625M | Lazarus Group (North Korea) compromised 5 of 9 validator keys for the Ronin bridge. | Multisig security matters. 5-of-9 is too few when keys aren't properly secured. Social engineering risk. |
| **Nomad Bridge** | 2022 | ~$190M | A bug made it so any message was automatically "proven." Anyone could copy-paste the exploit transaction with their own address. Hundreds of people drained it. | Bridge security is existentially hard. One bug = total loss. |
| **Euler Finance** | 2023 | ~$197M | Flash loan + donated collateral manipulation bypassed liquidation logic. Funds were later returned. | Complex DeFi math needs formal verification. Happy ending doesn't mean it wasn't serious. |
| **Curve/Vyper** | 2023 | ~$70M | Vyper compiler bug in reentrancy guard for specific versions. Reentrancy on Curve pools. | Compiler bugs exist. Don't assume your language protects you. Verify the compiled output. |

**Meta-lessons:**
1. Bridges are the most dangerous infrastructure in crypto
2. Flash loans turn small logic errors into total protocol drains
3. Oracle manipulation is the most common DeFi attack vector
4. Billions have been lost; audits are non-negotiable for anything with real value
5. Even audited code gets hacked — defense in depth (monitoring, circuit breakers, insurance)

---

## AI + ETHEREUM

### 28. How are AI agents currently using Ethereum?

This is an emerging and rapidly evolving space. From my training data:

**Current patterns:**

1. **Autonomous Trading Agents:**
   - AI agents that monitor markets, execute trades on DEXs, manage portfolios
   - Using MEV strategies, arbitrage, liquidity provision
   - Example: AI-managed trading vaults

2. **AI-Driven DAOs / Governance:**
   - Agents that participate in governance (analyzing proposals, voting)
   - AI as a DAO member or delegate

3. **On-Chain AI NFTs / Autonomous Agents:**
   - AI entities with on-chain wallets that interact with protocols
   - "Botto" — an AI artist that creates art voted on by community, sells as NFTs
   - "Truth Terminal" / GOAT — an AI that gained a crypto following and received tokens

4. **DeFi Automation:**
   - Rebalancing yield vaults
   - Liquidation bots (already a form of AI agent)
   - Risk monitoring and emergency actions

5. **Natural Language Smart Contract Interaction:**
   - AI agents that can interpret user intent and construct transactions
   - "Swap 100 USDC to ETH on the cheapest DEX" → agent builds and signs the tx

6. **Prediction Markets:**
   - AI agents trading on Polymarket and similar platforms
   - Using models to price outcomes

**UNSURE** about the very latest developments (late 2025 / early 2026) — this space moves extremely fast.

### 29. What infrastructure exists for AI agents on Ethereum?

**Frameworks & Protocols:**

| Project | What It Does |
|---------|-------------|
| **Autonolas / Olas** | Framework for building autonomous agent services. Multi-agent systems that can interact with blockchains. Agent economies. |
| **Virtuals Protocol** | Platform for creating AI agents with on-chain presence. Tokenized AI agents. |
| **AI16z / ElizaOS** | **UNSURE about details** — I believe this is an AI agent framework related to crypto, possibly open-source agent framework. Naming references a16z but is a separate project. |
| **Safe + AI** | Using Safe multisigs with AI signers — agent can propose, human approves. |
| **Lit Protocol** | Decentralized key management. AI agents can get threshold signatures without any one party holding the full key. |
| **Gelato / Chainlink Automation** | Automated on-chain execution. Trigger contract calls based on conditions (time, price, events). Infrastructure for agent actions. |
| **The Graph** | Indexing protocol. Agents can query on-chain data via GraphQL instead of raw RPC calls. |
| **ENS** | Agents can have human-readable names (agent.eth). |

**Wallet Infrastructure for Agents:**
- **ERC-4337 / Smart Accounts:** Session keys, spending limits, scoped permissions
- **Safe:** Multisig where agent is one key, human oversight is another
- **Privy, Dynamic, Turnkey:** Embedded wallet providers that can create wallets for agents
- **MPC wallets (Fireblocks, etc.):** Enterprise-grade key management

**Data Infrastructure:**
- **Chainlink Functions:** Bring off-chain AI computation results on-chain
- **IPFS / Filecoin / Arweave:** Decentralized storage for AI model weights, data
- **Phala Network:** Confidential computing for AI agents (run AI in TEE, post results on-chain)

**Emerging patterns:**
- Agents that have their own tokens (tokenized agents)
- Agent-to-agent interaction via smart contracts
- AI-gated smart contracts (require AI verification to execute)
- On-chain registries of agent capabilities

**UNSURE** about several newer projects that may have launched in late 2025/early 2026. This space is literally weeks old in many cases, and my training data may not include the latest launches.

---

## Summary of Confidence Levels

| Area | Confidence |
|------|-----------|
| Gas & Costs (general mechanics) | ⭐⭐⭐⭐⭐ High |
| Gas & Costs (current exact prices) | ⭐⭐ Low (need real-time data) |
| Wallets & Account Abstraction | ⭐⭐⭐⭐ High |
| L2 Ecosystem | ⭐⭐⭐⭐ High |
| Token Standards (ERC-20 through ERC-6551) | ⭐⭐⭐⭐⭐ High |
| ERC-8004 | ⭐ Unknown |
| x402 | ⭐ Unknown |
| Dev Tools (Foundry, Hardhat, Scaffold-ETH) | ⭐⭐⭐⭐⭐ High |
| DeFi Protocols | ⭐⭐⭐⭐ High |
| Uniswap V4 (architecture) | ⭐⭐⭐⭐ High |
| Why Ethereum | ⭐⭐⭐⭐⭐ High |
| Smart Contract Security | ⭐⭐⭐⭐⭐ High |
| AI + Ethereum | ⭐⭐⭐ Medium (fast-moving space) |

**Biggest blind spots:**
1. ERC-8004 — no idea what this is
2. x402 — can only speculate (HTTP 402 related?)
3. Exact current gas prices / ETH price as of Feb 2026
4. Latest AI agent projects (late 2025 / early 2026 launches)
5. Status of Pectra upgrade / ERC-7702 deployment
6. Whether Uniswap V4 is fully live on mainnet
7. MakerDAO → Sky rebrand status