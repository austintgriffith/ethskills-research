

# Baseline Knowledge Audit: What Claude Opus 4 Knows About Ethereum

*Conducted: 2026-02-13 | Model: Claude Opus 4 (anthropic claude-opus-4-6)*
*Rules: No tools used. Pure training data only. Uncertainty explicitly marked.*

---

## GAS & COSTS

### 1. What does it cost to deploy a simple ERC-20 token on Ethereum mainnet today?

A minimal ERC-20 contract (basic OpenZeppelin implementation) typically costs around **1–2 million gas** to deploy. At a gas price of ~10-30 gwei and ETH around $2,500-3,000 (ranges I recall from my training data), that puts deployment cost roughly at:

- ~1.5M gas × 20 gwei = 0.03 ETH ≈ **$75-90**

But this varies wildly. During low-congestion periods, gas can drop to 5-10 gwei, making it $20-40. During spikes it can be $200+. A more feature-rich token (with pausable, burnable, access control, etc.) will use more gas.

**Honest caveat:** I'm not confident about "today's" exact gas prices or ETH price as of February 2026. The above is based on patterns from my training data which has a knowledge cutoff. The general mechanics are right but the dollar figures may be off.

### 2. What is the current typical gas price on Ethereum mainnet?

Post-Dencun upgrade (March 2024), base fees on L1 have generally been lower than the pre-EIP-1559 era. Typical ranges I've seen in training data:

- **Low activity:** 5-15 gwei
- **Normal:** 15-30 gwei
- **High congestion/NFT mints/hype events:** 50-200+ gwei

Post-Dencun, a lot of activity migrated to L2s, which has kept L1 base fees somewhat depressed. I recall periods in 2024 where L1 gas was consistently under 10 gwei, which was historically unusual.

**UNSURE** about the exact "current" (Feb 2026) typical gas price.

### 3. What does a simple ETH transfer cost in USD?

A simple ETH transfer costs exactly **21,000 gas**. At various gas prices:

- 10 gwei: 21,000 × 10 gwei = 0.00021 ETH
- 20 gwei: 0.00042 ETH
- 50 gwei: 0.00105 ETH

If ETH is ~$2,500-3,000:
- At 10 gwei: **$0.50-0.63**
- At 20 gwei: **$1.05-1.26**
- At 50 gwei: **$2.63-3.15**

So roughly **$0.50-$3.00** in typical conditions. Can be much higher during congestion spikes.

**UNSURE** about current ETH price in Feb 2026.

### 4. How does mainnet gas compare to L2s like Arbitrum, Optimism, Base?

Dramatically cheaper on L2s. Order of magnitude comparisons:

- **Arbitrum:** Transactions typically cost **$0.01-0.10**. Simple transfers can be fractions of a cent. Post-Dencun (which introduced blob transactions / EIP-4844), L2 data posting costs dropped ~10-100x.
- **Optimism:** Similar to Arbitrum, **$0.01-0.10** for simple transactions. Uses the OP Stack.
- **Base:** Similar cost profile to Optimism (it's built on the OP Stack). Often among the cheapest because Coinbase subsidizes sequencer costs and it has high throughput. **$0.001-0.05** for simple txs.

**Key insight:** EIP-4844 (part of Dencun, March 2024) introduced "blob" transactions that L2s use to post data to L1 cheaply. This was a game-changer — L2 fees dropped by 10-100x overnight. Before Dencun, L2 fees were $0.10-0.50; after, they're often sub-cent.

The ratio is roughly: **L1 costs 100-1000x more than L2s** for equivalent operations.

---

## WALLETS

### 5. How do you set up a wallet for an AI agent to use?

Several approaches:

**Simple approach — Programmatic EOA:**
```javascript
import { ethers } from 'ethers';
const wallet = ethers.Wallet.createRandom();
// Store wallet.privateKey securely
// Connect to a provider
const connected = wallet.connect(new ethers.JsonRpcProvider(RPC_URL));
```

The agent holds a private key and signs transactions directly. Simple but risky — if the key leaks, funds are gone.

**Better approach — Safe + Agent as signer:**
Create a Safe (multisig) where the agent is one signer but a human co-signs or the agent operates within spending limits. This adds human oversight.

**Best approach — Account Abstraction (ERC-4337):**
Use a smart account with:
- Spending limits per transaction/time period
- Whitelisted contract interactions only
- Human override/pause capability
- Session keys that expire

**Key considerations for AI agent wallets:**
- Never hardcode private keys; use environment variables or secret managers
- Fund with only what's needed (don't keep large balances)
- Use testnets (Sepolia) for development
- Consider a "hot wallet" pattern: small operational balance, refilled from a more secure source
- Log all transactions for auditability

**Infrastructure options:**
- Privy, Dynamic, Turnkey — MPC-based wallet providers
- Fireblocks — institutional-grade key management
- AWS KMS / GCP KMS — cloud-managed signing keys

### 6. What is a Gnosis Safe / Safe multisig and how do you set one up?

**Safe** (formerly Gnosis Safe) is the most widely used multisig wallet on Ethereum. It's a smart contract wallet that requires M-of-N signatures to execute transactions.

**Key features:**
- **M-of-N signing:** e.g., 2-of-3 signers must approve a transaction
- **Module system:** Extensible — you can add modules for allowances, recurring payments, etc.
- **Guard system:** Pre/post-transaction checks
- **Batched transactions:** Execute multiple operations atomically
- **Off-chain signing:** Collect signatures without on-chain gas until execution
- **Cross-chain:** Deployed on most EVM chains

**How to set one up:**
1. Go to **app.safe.global**
2. Connect your wallet
3. Choose a network
4. Add owner addresses (the signers)
5. Set the threshold (how many must sign)
6. Deploy the Safe contract (costs gas)
7. Fund the Safe with ETH/tokens

**Programmatically (Safe SDK):**
```javascript
import Safe from '@safe-global/protocol-kit';
import { SafeFactory } from '@safe-global/protocol-kit';

const safeFactory = await SafeFactory.init({
  provider: RPC_URL,
  signer: DEPLOYER_PRIVATE_KEY
});

const safeAccountConfig = {
  owners: ['0xOwner1...', '0xOwner2...', '0xOwner3...'],
  threshold: 2
};

const safe = await safeFactory.deploySafe({ safeAccountConfig });
```

**Why it matters:** Safe holds a massive amount of value on Ethereum — hundreds of billions of dollars in TVL across all Safes. It's the standard for DAO treasuries, team wallets, and protocol funds.

### 7. What is account abstraction (ERC-4337) and what's the current state?

**ERC-4337** decouples the "account" from the "signer." Instead of EOAs (externally owned accounts) controlled by a single private key, you get **smart contract accounts** with programmable validation logic.

**Core concepts:**

- **UserOperation:** Instead of a transaction, users submit a "UserOperation" (a struct describing what they want to do)
- **Bundler:** Aggregates UserOperations and submits them as a single transaction to the EntryPoint contract
- **EntryPoint:** A singleton contract that validates and executes UserOperations
- **Paymaster:** A contract that can pay gas on behalf of users (gasless transactions / sponsored gas)
- **Account Factory:** Creates smart contract accounts

**What this enables:**
- **Gas sponsorship:** Apps pay gas for users (Paymasters)
- **Batched transactions:** Approve + swap in one step
- **Social recovery:** Recover account via trusted contacts
- **Session keys:** Temporary, scoped permissions
- **Custom validation:** Sign with passkeys, Face ID, etc. instead of secp256k1
- **Spending limits:** Cap what can be spent per day/tx

**Current state (as of my training data):**
- ERC-4337 is live and deployed on mainnet and most L2s
- The EntryPoint contract (v0.6, then v0.7) is deployed
- Major wallets building on it: **ZeroDev, Biconomy, Alchemy (Account Kit), Pimlico, Stackup**
- Safe has added ERC-4337 compatibility
- Adoption is growing, especially on L2s where bundler gas costs are lower
- **ERC-7702** (part of the Pectra upgrade) brings some AA features to EOAs natively — allows EOAs to temporarily delegate to smart contract code. This is a complement to, not replacement for, 4337.

**UNSURE** about the exact state of ERC-7702/Pectra deployment as of Feb 2026 — I believe Pectra was planned for 2025 but am not confident it shipped.

---

## L2s

### 8. What L2s exist on Ethereum? List them with key differences.

**Optimistic Rollups** (assume transactions valid, fraud proofs if challenged):

| L2 | Key Differences |
|---|---|
| **Arbitrum One** | Largest L2 by TVL. Nitro tech stack. Full EVM equivalence. Strong DeFi ecosystem. Governed by ARB token & DAO. |
| **Arbitrum Nova** | Arbitrum's AnyTrust chain — cheaper, uses a Data Availability Committee instead of posting all data to L1. Good for gaming/social. |
| **Optimism** | OP Stack — open-source, modular rollup framework. Governed by OP token with bicameral governance (Token House + Citizens' House). Retroactive public goods funding. |
| **Base** | Built on OP Stack by Coinbase. No native token. Huge onboarding from Coinbase user base. Very cheap fees. Part of the "Superchain" vision with Optimism. |
| **Mantle** | OP Stack fork with a modular data availability approach (uses EigenDA). MNT token. |
| **Blast** | Optimistic rollup with native yield (ETH staking yield + USDB yield baked into the chain). |
| **Zora** | OP Stack chain focused on NFTs/creator economy. |
| **Mode** | OP Stack. DeFi-focused. Part of Superchain. |

**ZK Rollups** (use validity proofs — mathematically prove every batch is correct):

| L2 | Key Differences |
|---|---|
| **zkSync Era** | By Matter Labs. Custom zkEVM. LLVM-based compiler. Native account abstraction. ZK token. |
| **StarkNet** | By StarkWare. Uses STARKs (not SNARKs). Cairo language (not Solidity-native). Very different developer experience. STRK token. |
| **Polygon zkEVM** | By Polygon. Type-2 zkEVM — high EVM compatibility. Aims to be a direct drop-in for Ethereum contracts. |
| **Linea** | By ConsenSys. zkEVM. Integrated with MetaMask. |
| **Scroll** | Community-driven zkEVM. Type-2 EVM equivalence. |
| **Taiko** | Based rollup (uses L1 validators for sequencing). Type-1 zkEVM — maximum Ethereum equivalence. |

**Key distinctions:**
- Optimistic rollups are simpler and more EVM-compatible but have a ~7-day withdrawal window (fraud proof period)
- ZK rollups have faster finality (minutes, not days) but are more complex to build and maintain
- The "Superchain" vision unites OP Stack chains (Optimism, Base, Zora, Mode, etc.) with shared interoperability

### 9. How do you deploy a contract on an L2 vs mainnet?

**Short answer: It's almost identical.** The main difference is the RPC URL and chain ID.

For **Optimistic Rollups** (Arbitrum, Optimism, Base): deployment is exactly the same as mainnet. Same Solidity, same tools, same bytecode. Just change:

```javascript
// Hardhat config
networks: {
  mainnet: {
    url: "https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY",
    chainId: 1,
  },
  arbitrum: {
    url: "https://arb-mainnet.g.alchemy.com/v2/YOUR_KEY",
    chainId: 42161,
  },
  base: {
    url: "https://mainnet.base.org",
    chainId: 8453,
  },
  optimism: {
    url: "https://mainnet.optimism.io",
    chainId: 10,
  }
}
```

Then: `npx hardhat deploy --network arbitrum`

**For ZK Rollups** — it depends:
- **Polygon zkEVM, Scroll, Linea:** Same as above — high EVM compatibility, just change RPC
- **zkSync Era:** Requires a different compiler (`zksolc`) and slightly different deployment tooling. Has its own hardhat plugin (`@matterlabs/hardhat-zksync-solc`). Some Solidity features behave differently.
- **StarkNet:** Completely different — you write in **Cairo**, not Solidity. Different tools (`starkli`, Scarb). Not EVM-compatible at all.

**Key gotcha:** Some opcodes behave slightly differently on L2s (e.g., `block.number`, `block.timestamp` may refer to L2 blocks, not L1). `msg.sender` in cross-chain messages needs special handling.

### 10. How do you bridge assets between mainnet and L2s?

**Native bridges** (canonical, trustless):
Each L2 has its own official bridge:
- **Arbitrum Bridge:** bridge.arbitrum.io
- **Optimism Bridge:** app.optimism.io/bridge
- **Base Bridge:** bridge.base.org
- **zkSync Bridge:** bridge.zksync.io

**How it works (optimistic rollups):**
- **L1 → L2 (deposit):** Lock tokens on L1, they're minted on L2. Takes ~10-15 minutes.
- **L2 → L1 (withdrawal):** Initiate on L2, wait for fraud proof period (~7 days for optimistic rollups), then claim on L1.

**How it works (ZK rollups):**
- **L1 → L2:** Similar deposit, ~10-30 minutes
- **L2 → L1:** Faster than optimistic — once a ZK proof is posted and verified, you can claim. Can be minutes to hours instead of 7 days.

**Third-party bridges (faster but trust assumptions):**
- **Across Protocol:** Fast bridging using relayer network, very popular
- **Stargate (LayerZero):** Cross-chain liquidity protocol
- **Hop Protocol:** Uses "bonders" for fast L2-to-L2 transfers
- **Synapse:** AMM-based cross-chain bridge
- **Connext:** Trust-minimized bridging using noncustodial liquidity

**Programmatically:**
```javascript
// Example: Using Arbitrum SDK to bridge ETH
import { EthBridger, getArbitrumNetwork } from '@arbitrum/sdk';

const arbNetwork = await getArbitrumNetwork(42161);
const ethBridger = new EthBridger(arbNetwork);

const depositTx = await ethBridger.deposit({
  amount: ethers.parseEther("0.1"),
  l1Signer: l1Wallet,
});
```

**For AI agents:** Third-party bridges like Across are practical because they're faster and the agent doesn't need to wait 7 days. The trade-off is additional trust assumptions.

---

## STANDARDS

### 11. List the major Ethereum token standards and when to use each.

| Standard | Type | Use When |
|---|---|---|
| **ERC-20** | Fungible tokens | Currencies, governance tokens, utility tokens. The most common standard. |
| **ERC-721** | Non-fungible tokens (NFTs) | Unique items: art, collectibles, domain names, gaming items. Each token has a unique ID. |
| **ERC-1155** | Multi-token | Gaming items, mixed fungible/non-fungible collections. One contract for both fungible and non-fungible tokens. More gas-efficient for batch operations. |
| **ERC-777** | Advanced fungible | Improved ERC-20 with hooks (send/receive callbacks). Largely deprecated due to reentrancy concerns. |
| **ERC-4626** | Tokenized vaults | Yield vaults, lending positions. Standardized interface for vault deposits/withdrawals/accounting. The "yield-bearing token standard." |
| **ERC-2981** | NFT royalties | Royalty information for NFT marketplaces. Specifies who gets paid and how much on secondary sales. |
| **ERC-6551** | Token-bound accounts | NFTs that own assets. Each NFT gets its own smart contract wallet. Useful for gaming characters, identities. |
| **ERC-5169** | Script association | Link off-chain scripts to tokens. |
| **ERC-3525** | Semi-fungible | Financial instruments, bonds, options. Tokens with ID + value + slot dimensions. |
| **ERC-404** | Experimental hybrid | Combined ERC-20 + ERC-721 (fractional NFTs). Experimental, not officially standardized. |

### 12. What is ERC-8004?

**UNSURE.** I don't have confident knowledge of an ERC-8004 standard from my training data. Given the numbering, this is likely a relatively recent proposal (ERCs in the 8000 range would be from 2024-2025).

Possible guesses I won't make because the instructions say to say UNSURE rather than guess.

**UNSURE** — I cannot reliably describe this standard from my training data.

### 13. What is x402?

**UNSURE.** I'm not confident about what "x402" refers to in the Ethereum context. 

A few possibilities I'm aware of but not confident about:
- HTTP status code 402 is "Payment Required" — there has been discussion about protocols that use this for web-native payments
- There may be a protocol or standard called x402 related to machine-to-machine payments on Ethereum

**UNSURE** — I cannot give a reliable description from my training data alone.

---

## TOOLS

### 14. What are the current best tools/frameworks for building on Ethereum?

**Smart Contract Development:**
- **Foundry (forge, cast, anvil, chisel):** Rust-based toolkit. Fastest test execution. Tests written in Solidity. Industry standard for serious contract development. By Paradigm.
- **Hardhat:** JavaScript/TypeScript toolkit. Huge plugin ecosystem. Most tutorials use it. Good for JS-heavy teams.
- **Remix IDE:** Browser-based. Great for learning, quick prototyping. No setup required.

**Frontend / Full-Stack:**
- **Scaffold-ETH 2:** Full-stack Ethereum starter kit. Next.js + Hardhat/Foundry + wagmi/viem.
- **wagmi:** React hooks for Ethereum. The standard for React dApp frontends.
- **viem:** TypeScript Ethereum library. Low-level, type-safe. Used by wagmi under the hood. Replacing ethers.js in many projects.
- **ethers.js (v6):** Still widely used. More batteries-included than viem. Large existing codebase.
- **RainbowKit / ConnectKit / Web3Modal:** Wallet connection UI components.

**Testing & Security:**
- **Slither:** Static analysis (by Trail of Bits)
- **Mythril:** Symbolic execution
- **Echidna:** Fuzzing (property-based testing, by Trail of Bits)
- **Medusa:** Fuzzing (by Cryticcompile/Trail of Bits)
- **Certora Prover:** Formal verification

**Infrastructure:**
- **Alchemy, Infura, QuickNode:** RPC providers
- **The Graph:** Indexing/querying blockchain data via GraphQL
- **Tenderly:** Simulation, debugging, monitoring
- **OpenZeppelin Contracts:** Battle-tested, audited contract libraries
- **OpenZeppelin Defender:** Operations, monitoring, transaction automation

### 15. What is Scaffold-ETH 2 and how do you use it?

**Scaffold-ETH 2** is an open-source, full-stack Ethereum development toolkit created by **Austin Griffith** (and the BuidlGuidl community). It's designed to make it fast and easy to prototype and build dApps.

**Tech stack:**
- **Next.js** (React) frontend
- **Hardhat** or **Foundry** for smart contracts
- **wagmi + viem** for Ethereum interactions
- **RainbowKit** for wallet connection
- **TypeScript** throughout
- **Tailwind CSS + DaisyUI** for styling

**Key features:**
- **Hot contract reload:** Deploy a contract, frontend auto-generates UI
- **Debug contracts page:** Auto-generated UI for every contract function (read/write)
- **Block explorer:** Built-in local block explorer
- **Burner wallets:** Auto-generated wallets for quick testing
- **Network switching:** Easy mainnet/testnet/local toggling
- **Contract hooks:** Auto-generated React hooks for every contract (useScaffoldReadContract, useScaffoldWriteContract)

**How to use it:**
```bash
npx create-eth@latest
# Choose project name, Hardhat or Foundry
cd my-project
yarn install

# Terminal 1: Start local chain
yarn chain

# Terminal 2: Deploy contracts
yarn deploy

# Terminal 3: Start frontend
yarn start
```

Then open `localhost:3000` — you have a working dApp with hot-reloading contracts.

**Why it matters:** It's the fastest way to go from idea to working prototype. Heavily used in hackathons and by the BuidlGuidl community. Austin built it because getting a full Ethereum dev environment set up was too painful — Scaffold-ETH 2 gives you everything wired up.

### 16. What is abi.ninja?

**abi.ninja** is a web tool that lets you interact with any smart contract on Ethereum by just providing its address (or pasting an ABI). It auto-detects verified contract ABIs from block explorers and generates a UI for reading/writing to the contract.

**How it works:**
1. Go to abi.ninja
2. Paste a contract address
3. It fetches the ABI from Etherscan (or you paste one manually)
4. You get a UI for every function — read state, call write functions

**Why it's useful:**
- Quick contract interaction without writing code
- Doesn't require a frontend — just the contract address
- Works across multiple chains
- Great for debugging, testing, and exploring contracts

I believe it was built by the BuidlGuidl community / associated with the Scaffold-ETH ecosystem.

### 17. What are the best block explorers and how would an agent use them?

**Major block explorers:**

- **Etherscan** (etherscan.io) — The gold standard for Ethereum mainnet. Also runs Arbiscan, Basescan, Optimistic Etherscan (OP), etc.
- **Blockscout** — Open-source explorer. Used by many L2s and custom chains.
- **Otterscan** — Self-hostable, fast Ethereum explorer.
- **Once Upon** — Human-readable transaction explorer (translates raw tx data into plain English).

**How an AI agent would use them (APIs):**

**Etherscan API:**
```
# Get contract ABI
https://api.etherscan.io/api?module=contract&action=getabi&address=0x...&apikey=KEY

# Get token balance
https://api.etherscan.io/api?module=account&action=tokenbalance&contractaddress=0x...&address=0x...&apikey=KEY

# Get transaction list
https://api.etherscan.io/api?module=account&action=txlist&address=0x...&startblock=0&endblock=99999999&apikey=KEY

# Get gas price oracle
https://api.etherscan.io/api?module=gastracker&action=gasoracle&apikey=KEY
```

**Agent use cases:**
- **Verify contracts:** Check if a contract is verified, read its source code
- **Monitor addresses:** Watch for incoming/outgoing transactions
- **Decode transactions:** Understand what a transaction did
- **Check gas prices:** Before submitting transactions
- **Token discovery:** Find what tokens an address holds

**Rate limits:** Free Etherscan API keys get 5 calls/sec. For agents, you'd want a Pro key or use an RPC provider with built-in tracing capabilities.

---

## DEFI / BUILDING BLOCKS

### 18. Explain Uniswap V4 — what's new, what are hooks, how would you build on it?

**Uniswap V4** is the latest version of the Uniswap DEX protocol. Major changes from V3:

**Key innovations:**

1. **Singleton Architecture:** All pools live in ONE contract (the `PoolManager`). V3 had a separate contract per pool. This dramatically reduces gas for multi-hop swaps and pool creation.

2. **Hooks:** The killer feature. Hooks are external contracts that execute custom logic at specific points in the pool lifecycle:
   - `beforeInitialize` / `afterInitialize`
   - `beforeAddLiquidity` / `afterAddLiquidity`
   - `beforeRemoveLiquidity` / `afterRemoveLiquidity`
   - `beforeSwap` / `afterSwap`
   - `beforeDonate` / `afterDonate`

3. **Flash Accounting:** Uses transient storage (EIP-1153) — tokens aren't moved until the end of an entire operation. Internal accounting tracks balances, and only the net difference is settled. This saves gas massively.

4. **Native ETH Support:** Can use ETH directly without wrapping to WETH.

5. **Custom Fee Structures:** Dynamic fees set by hooks (instead of fixed tiers like V3's 0.01%, 0.05%, 0.3%, 1%).

**What you can build with hooks:**
- **TWAMM (Time-Weighted AMM):** Large orders executed over time
- **Dynamic fees:** Adjust fees based on volatility, volume, or oracle data
- **Limit orders:** On-chain limit orders within the AMM
- **Customized oracles:** Better/different price oracle implementations
- **KYC/access control:** Permissioned pools
- **Auto-compounding:** Reinvest fees automatically
- **MEV mitigation:** Auction-based ordering, batch auctions

**Building on V4:**
```solidity
// A simple hook that charges higher fees during volatile markets
import {BaseHook} from "v4-periphery/BaseHook.sol";

contract VolatilityFeeHook is BaseHook {
    function getHookPermissions() public pure override returns (Hooks.Permissions memory) {
        return Hooks.Permissions({
            beforeSwap: true,
            afterSwap: false,
            // ... other flags
        });
    }
    
    function beforeSwap(address, PoolKey calldata key, IPoolManager.SwapParams calldata, bytes calldata)
        external override returns (bytes4, BeforeSwapDelta, uint24)
    {
        // Custom fee logic here
        uint24 dynamicFee = calculateFeeBasedOnVolatility();
        return (BaseHook.beforeSwap.selector, BeforeSwapDeltaLibrary.ZERO_DELTA, dynamicFee);
    }
}
```

**UNSURE** about the exact deployment status of V4 as of Feb 2026 — it was in development/audit through 2024. I believe it launched on mainnet in late 2024 or early 2025 but am not 100% certain of the exact date.

### 19. How would you build a yield vault on Arbitrum? What protocols would you use?

**Architecture: ERC-4626 Vault on Arbitrum**

**Step 1: Build an ERC-4626 vault contract**
```solidity
import "@openzeppelin/contracts/token/ERC20/extensions/ERC4626.sol";

contract YieldVault is ERC4626 {
    constructor(IERC20 asset) ERC4626(asset) ERC20("Vault Share", "vSHARE") {}
    
    // Implement strategy: deposit into yield protocol, harvest, compound
    function _deposit(address caller, address receiver, uint256 assets, uint256 shares) internal override {
        super._deposit(caller, receiver, assets, shares);
        // Deploy assets into yield strategy
        _deployToStrategy(assets);
    }
}
```

**Step 2: Choose yield sources on Arbitrum**

- **Aave V3 (on Arbitrum):** Lend USDC/ETH for variable yield. Widely used, battle-tested.
- **GMX:** Perpetual DEX — provide liquidity as GLP/GM for trading fees + esGMX rewards. Higher yield, more risk.
- **Pendle:** Yield tokenization — split yield-bearing tokens into PT (principal) and YT (yield). Enables yield trading.
- **Radiant Capital:** Cross-chain lending (though it was hacked — be cautious).
- **Silo Finance:** Isolated lending markets.
- **Camelot DEX:** Native Arbitrum DEX with concentrated liquidity.
- **Compound V3 (on Arbitrum):** Single-asset lending.

**Step 3: Strategy examples**

**Simple (low risk):** Deposit USDC into Aave V3 on Arbitrum → earn supply APY (2-5%)

**Medium:** Deposit into Pendle to buy discounted PT tokens → lock in fixed yield

**Complex (higher risk/reward):**
1. Deposit ETH into the vault
2. Stake as wstETH (Lido liquid staking)
3. Supply wstETH to Aave V3 as collateral
4. Borrow USDC against it
5. Supply USDC to GMX GM pools for trading fees
6. Auto-compound rewards

**Key protocols:**
- **Yearn Finance (on Arbitrum):** Can study their vault strategies for inspiration
- **Beefy Finance:** Multi-chain yield optimizer, already does auto-compounding on Arbitrum

### 20. What is a no-loss prediction market and how would you build one?

**Concept:** A prediction market where participants cannot lose their principal. They deposit funds, the principal generates yield, and that yield is distributed as prizes to winners. Losers get their principal back.

**Inspiration:** PoolTogether was the OG "no-loss lottery" — deposit DAI, it goes into Compound/Aave, interest becomes the prize pool, all depositors get principal back.

**How to build one:**

**Architecture:**
1. **Deposit phase:** Users deposit USDC/ETH into the market contract, choosing an outcome
2. **Yield generation:** All deposits go into a yield protocol (Aave, ERC-4626 vault)
3. **Resolution:** An oracle (Chainlink, UMA, reality.eth) resolves the outcome
4. **Distribution:**
   - Everyone gets their principal back
   - Yield is distributed proportionally to those who predicted correctly
   - Optionally, a small fee goes to the protocol

```solidity
contract NoLossPredictionMarket {
    IERC4626 public vault;  // Yield source
    
    mapping(address => mapping(uint8 => uint256)) public positions; // user => outcome => amount
    mapping(uint8 => uint256) public totalPerOutcome;
    uint256 public totalDeposits;
    uint8 public winningOutcome;
    
    function predict(uint8 outcome, uint256 amount) external {
        // Take deposit
        asset.transferFrom(msg.sender, address(this), amount);
        // Deposit into yield vault
        vault.deposit(amount, address(this));
        // Track position
        positions[msg.sender][outcome] += amount;
        totalPerOutcome[outcome] += amount;
        totalDeposits += amount;
    }
    
    function claim() external {
        uint256 principal = positions[msg.sender][/* any outcome */];
        uint256 yieldShare = 0;
        
        if (positions[msg.sender][winningOutcome] > 0) {
            uint256 totalYield = vault.maxWithdraw(address(this)) - totalDeposits;
            yieldShare = totalYield * positions[msg.sender][winningOutcome] / totalPerOutcome[winningOutcome];
        }
        
        // Return principal + any yield winnings
        vault.withdraw(principal + yieldShare, msg.sender, address(this));
    }
}
```

**Challenges:**
- Yield rates are low (2-5%), so prizes are small relative to deposits
- Time-locking funds reduces capital efficiency
- Need reliable oracles for outcome resolution
- Smart contract risk on the yield source

### 21. List the major DeFi protocols and what they do

| Protocol | Category | What It Does |
|---|---|---|
| **Aave** | Lending/Borrowing | Deposit collateral, borrow assets. Flash loans. V3 is multi-chain with efficiency mode. Largest lending protocol. |
| **Compound** | Lending/Borrowing | Pioneer of DeFi lending. V3 (Comet) simplified to single-borrowable-asset markets. |
| **MakerDAO / Sky** | CDP/Stablecoin | Deposit collateral (ETH, etc.) to mint DAI stablecoin. Rebranded to "Sky" with USDS stablecoin. Core DeFi primitive. |
| **Uniswap** | DEX (AMM) | Automated market maker. V2 (constant product), V3 (concentrated liquidity), V4 (hooks). Largest DEX by volume. |
| **Curve** | DEX (Stableswap) | Optimized for same-peg assets (USDC↔DAI, stETH↔ETH). Low slippage for stable pairs. CRV token with veTokenomics. |
| **Yearn Finance** | Yield Aggregator | Automated yield strategies (Vaults). Deposits funds into optimal yield opportunities. |
| **Lido** | Liquid Staking | Stake ETH, receive stETH (liquid staking derivative). Largest liquid staking protocol. ~28-30% of all staked ETH. |
| **Rocket Pool** | Liquid Staking | Decentralized liquid staking. rETH token. More decentralized than Lido (permissionless node operators). |
| **Pendle** | Yield Trading | Tokenize future yield. Split yield-bearing tokens into principal (PT) and yield (YT) components. Trade yield. |
| **EigenLayer** | Restaking | Re-stake ETH/LSTs to secure additional services (AVSs). Extends Ethereum's security model. |
| **Morpho** | Lending Optimizer | Peer-to-peer matching layer on top of Aave/Compound for better rates. Also Morpho Blue for permissionless lending. |
| **GMX** | Perpetual DEX | Decentralized perpetual futures. GLP/GM liquidity model. Dominant on Arbitrum. |
| **dYdX** | Perpetual DEX | Order-book perpetual futures. Migrated to its own Cosmos chain. |
| **Balancer** | DEX (Weighted Pools) | Generalized AMM — pools with custom token weightings (not just 50/50). Boosted pools, composable pools. |
| **1inch** | DEX Aggregator | Finds best swap rates across multiple DEXes. Fusion mode for gasless swaps. |
| **Chainlink** | Oracle | Price feeds, VRF (randomness), automation, CCIP (cross-chain). The dominant oracle network. |
| **The Graph** | Indexing | Query blockchain data via GraphQL subgraphs. Essential infrastructure for dApp frontends. |
| **Convex Finance** | Yield/Governance | Boost Curve LP rewards without locking CRV yourself. Dominates Curve governance. |
| **Frax** | Stablecoin/LST | Fractional-algorithmic stablecoin (FRAX). Also frxETH for liquid staking. |
| **Ethena** | Synthetic Dollar | USDe — synthetic dollar backed by staked ETH hedged with short perp positions. Controversial but large TVL. |
| **Fluid (Instadapp)** | Lending/DEX | Smart lending with integrated DEX. Combines lending and trading liquidity. |

---

## WHY ETHEREUM

### 22. Why should someone build on Ethereum vs Solana?

**Arguments for Ethereum:**

1. **Security & Decentralization:** Ethereum has ~900,000+ validators. Solana has ~1,800 validators. Ethereum's network is far more decentralized and resistant to censorship/attacks.

2. **Composability & Ecosystem:** DeFi on Ethereum has deeper liquidity, more composable building blocks, and more battle-tested contracts. The "money lego" ecosystem is unmatched.

3. **L2 Scaling Strategy:** Ethereum's L2s (Arbitrum, Base, Optimism) give you low fees AND Ethereum security. You get cheap execution backed by the most secure settlement layer.

4. **Standards & Tooling:** ERC-20, ERC-721, ERC-4626, etc. are the standards everyone else copies. Foundry, Hardhat, Solidity — the tooling is mature.

5. **Lindy Effect:** Ethereum has been running since 2015. It's survived multiple crises. The smart contract security knowledge base is enormous.

6. **Developer Community:** Largest smart contract developer community by far. More auditors, more resources, more open source code to learn from.

7. **Institutional Adoption:** ETH ETFs exist. Banks, enterprises, and governments building on Ethereum/L2s. Enterprise Ethereum Alliance.

8. **EVM Dominance:** Most chains are EVM-compatible. Learn Ethereum once, deploy everywhere.

**Honest counterpoints (Solana advantages):**
- Solana's L1 is faster and cheaper than Ethereum L1 (though L2s close this gap)
- Solana has better UX for some consumer apps (single chain, no bridging)
- Solana's Firedancer validator client could improve reliability
- Solana has strong retail/consumer traction (memecoins, Phantom wallet UX)

### 23. Why should someone build on Ethereum vs not using a blockchain at all?

**Use Ethereum when:**

1. **Trust minimization matters:** No single party should control the system. Smart contracts execute deterministically — no one can change the rules after deployment.

2. **Global, permissionless access:** Anyone with internet can participate. No bank account needed. No KYC for basic financial operations. No geographic restrictions.

3. **Composability:** Your contract can interact with every other contract on the network. Build on top of Uniswap, Aave, Chainlink without asking permission. This is genuinely impossible in traditional finance.

4. **Programmable money:** Escrow, streaming payments, conditional transfers, multi-party financial agreements — all without intermediaries.

5. **Censorship resistance:** No government, company, or individual can shut down an Ethereum smart contract or freeze funds in a fully decentralized protocol.

6. **Transparency & Auditability:** All transactions are public and verifiable. Great for public goods, governance, charitable organizations.

7. **Asset tokenization:** Real-world assets, digital art, credentials — represented as tokens with standardized interfaces.

8. **Interoperability:** Ethereum tokens work across thousands of wallets, exchanges, and dApps automatically.

**Don't use a blockchain when:**
- Speed and cost are paramount (databases are 1000x faster and free)
- Privacy is critical (blockchain data is public by default)
- Users don't care about decentralization
- You trust the central party (and so does everyone else)
- Regulatory compliance requires central control
- Your use case doesn't involve value transfer or coordination among distrustful parties

**The honest test:** If you can replace "blockchain" with "database" and the product is the same, you don't need a blockchain.

### 24. What are Ethereum's honest weaknesses?

1. **L1 is expensive:** Even at 10 gwei, complex transactions cost $5-50. This prices out most casual users from mainnet. The answer is "use L2s" but that adds UX complexity.

2. **UX is still bad:** Seed phrases, gas estimation, transaction confirmation, bridging, chain switching — it's getting better but still far worse than traditional apps. Normies are confused.

3. **Fragmentation (L2 problem):** Having 50+ L2s means liquidity is fragmented, bridging is necessary, and users need to understand which chain they're on. The "Superchain" and interop solutions are works in progress.

4. **Finality is slow:** ~12 minutes for finality on L1. L2 withdrawals to L1 take 7 days (optimistic) or hours (ZK). Solana finalizes in ~400ms.

5. **MEV:** Miners/validators can extract value from users through sandwich attacks, frontrunning, etc. Flashbots and MEV-protected RPC endpoints help, but it's still a tax on users.

6. **Smart contract risk:** Immutable code means bugs are permanent. Billions have been lost to hacks. Audits help but aren't foolproof.

7. **Regulatory uncertainty:** SEC/regulatory posture toward ETH, DeFi, and tokens remains unclear in many jurisdictions.

8. **Scaling isn't solved yet:** L2s help but introduce trust assumptions (centralized sequencers for now), bridging risks, and complexity.

9. **Gas price volatility:** Hard to predict costs. A transaction might cost $1 now and $50 during an NFT mint.

10. **Solidity limitations:** Solidity is improving but is still a young language with footguns (reentrancy, overflow pre-0.8, etc.). Formal verification tooling is improving but not mainstream.

---

## BUILDING

### 25. Walk me through deploying a full dApp on Ethereum from scratch

**Step 1: Environment Setup**
```bash
# Install Node.js (v18+), Git
# Install Foundry
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

**Step 2: Scaffold Your Project**
```bash
# Option A: Scaffold-ETH 2 (fastest for full-stack)
npx create-eth@latest my-dapp
cd my-dapp && yarn install

# Option B: Manual setup
mkdir my-dapp && cd my-dapp
forge init contracts
npx create-next-app@latest frontend
```

**Step 3: Write Smart Contracts**
```solidity
// contracts/src/MyContract.sol
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1000000 * 10**18);
    }
}
```

**Step 4: Test Thoroughly**
```bash
# Foundry tests
forge test -vvv

# With fuzzing
forge test --fuzz-runs 10000

# Gas snapshot
forge snapshot
```

**Step 5: Deploy to Testnet (Sepolia)**
```bash
# Get Sepolia ETH from faucet (sepoliafaucet.com, etc.)
forge create --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY src/MyToken.sol:MyToken

# Or with script:
forge script script/Deploy.s.sol --rpc-url $SEPOLIA_RPC --broadcast
```

**Step 6: Verify Contract**
```bash
forge verify-contract $CONTRACT_ADDRESS src/MyToken.sol:MyToken \
  --etherscan-api-key $ETHERSCAN_KEY --chain sepolia
```

**Step 7: Build Frontend**
```typescript
// Using wagmi + viem
import { useReadContract, useWriteContract } from 'wagmi';
import { abi } from './MyToken.abi';

function App() {
  const { data: balance } = useReadContract({
    address: CONTRACT_ADDRESS,
    abi,
    functionName: 'balanceOf',
    args: [userAddress],
  });
  
  const { writeContract } = useWriteContract();
  
  const transfer = () => writeContract({
    address: CONTRACT_ADDRESS,
    abi,
    functionName: 'transfer',
    args: [recipientAddress, amount],
  });
}
```

**Step 8: Deploy Frontend**
```bash
# Vercel (easiest)
vercel deploy

# Or IPFS for full decentralization
npx ipfs-deploy build/
```

**Step 9: Deploy to Mainnet (or L2)**
```bash
# Same process, different RPC URL
forge script script/Deploy.s.sol --rpc-url $MAINNET_RPC --broadcast --verify
```

**Step 10: Post-Deployment**
- Set up monitoring (Tenderly, OpenZeppelin Defender)
- Create subgraph (The Graph) for indexing if needed
- Security monitoring for unusual activity
- Documentation

### 26. What are common smart contract vulnerabilities and how do you audit for them?

**Critical Vulnerabilities:**

1. **Reentrancy:** Attacker calls back into your contract before state updates complete.
   - Fix: Checks-Effects-Interactions pattern. Use OpenZeppelin's `ReentrancyGuard`.
   - Famous example: The DAO hack (2016)

2. **Integer Overflow/Underflow:** (Pre-Solidity 0.8 — now checked by default)
   - Fix: Use Solidity ≥0.8.0 or SafeMath library
   
3. **Access Control Failures:** Missing `onlyOwner` or incorrect permission checks.
   - Fix: Use OpenZeppelin's AccessControl or Ownable. Audit every external/public function.

4. **Oracle Manipulation:** Using spot AMM prices that can be manipulated in a single transaction.
   - Fix: Use Chainlink price feeds or TWAP oracles.

5. **Flash Loan Attacks:** Borrowing massive amounts to manipulate protocol state within one transaction.
   - Fix: Use time-weighted values, not spot values.

6. **Front-running / MEV:** Transactions visible in mempool before execution.
   - Fix: Commit-reveal schemes, Flashbots Protect.

7. **Delegatecall to Untrusted Contracts:** Storage layout mismatches or malicious code execution.
   - Fix: Only delegatecall to trusted, verified contracts.

8. **Unchecked External Calls:** Not checking return values of `.call()`, `.transfer()`, `.send()`.
   - Fix: Always check return values. Use `require(success)`.

9. **Denial of Service (DoS):** Loops over unbounded arrays, block gas limit attacks.
   - Fix: Avoid unbounded iterations. Use pull-over-push patterns.

10. **Signature Replay:** Reusing signed messages across chains or after nonce changes.
    - Fix: Include chain ID, nonce, contract address in signed data. Use EIP-712.

11. **Storage Collision (Proxy Patterns):** Proxy and implementation have overlapping storage slots.
    - Fix: Use EIP-1967 storage slots. Use OpenZeppelin's transparent/UUPS proxy patterns.

12. **Insufficient Randomness:** Using `block.timestamp` or `blockhash` for randomness.
    - Fix: Use Chainlink VRF.

**Audit Process:**
1. **Automated tools first:** Slither (static analysis), Mythril (symbolic execution)
2. **Fuzzing:** Echidna or Foundry's built-in fuzzer
3. **Manual review:** Line-by-line, focusing on state changes, access control, external calls
4. **Formal verification:** Certora Prover for critical invariants
5. **Professional audit:** Firms like Trail of Bits, OpenZeppelin, Consensys Diligence, Spearbit, Code4rena (competitive audits)

### 27. What are the most famous Ethereum hacks and what can we learn from them?

1. **The DAO Hack (2016) — $60M**
   - **What:** Reentrancy vulnerability in The DAO's split function
   - **How:** Attacker recursively called withdraw before balance was updated
   - **Result:** Ethereum hard forked to recover funds → Ethereum (ETH) vs Ethereum Classic (ETC)
   - **Lesson:** Checks-Effects-Interactions pattern. Reentrancy is the #1 vulnerability to watch.

2. **Parity Multisig Hack #1 (2017) — $30M**
   - **What:** Wallet initialization function was public, attacker called it to become owner
   - **Lesson:** Always restrict initialization. Use proper access control.

3. **Parity Multisig Freeze (2017) — $150M locked forever**
   - **What:** A library contract was destroyed via `selfdestruct`, bricking all wallets that delegated to it
   - **Lesson:** Library contracts need protection. Immutable dependencies are risky.

4. **bZx Flash Loan Attacks (2020) — $8M+**
   - **What:** Flash loans used to manipulate oracle prices
   - **Lesson:** Don't use spot prices from AMMs. Use time-weighted or external oracles.

5. **Wormhole Bridge Hack (2022) — $320M**
   - **What:** Verification bypass in the Solana-side bridge contract
   - **Lesson:** Cross-chain bridges are extremely high-risk. Verify all inputs rigorously.

6. **Ronin Bridge (Axie Infinity) Hack (2022) — $625M**
   - **What:** Attacker compromised 5 of 9 validator keys (social engineering)
   - **Lesson:** Multisig threshold security. Key management. Don't have 4/9 keys controlled by one entity.

7. **Nomad Bridge Hack (2022) — $190M**
   - **What:** A faulty upgrade made the zero hash a valid root, allowing anyone to drain funds
   - **Lesson:** Upgrade procedures and testing. This was a "crowd hack" — anyone could copy the exploit.

8. **Euler Finance (2023) — $197M (returned)**
   - **What:** Flash loan + donation attack exploiting liquidation logic
   - **Result:** Attacker negotiated and returned most funds
   - **Lesson:** Complex DeFi interactions create unexpected attack surfaces.

9. **Curve/Vyper Reentrancy (2023) — $70M+**
   - **What:** A bug in Vyper compiler's reentrancy lock affected multiple Curve pools
   - **Lesson:** Compiler bugs exist. Even non-Solidity languages have risks. Test at every level.

10. **Mango Markets (2022) — $114M (Solana, but relevant)**
    - **What:** Oracle manipulation via self-trading to inflate token price, then borrowing against it
    - **Lesson:** Oracle design and liquidity-based manipulations.

**Meta-lessons:**
- Bridges are the most attacked infrastructure (~$2B+ stolen)
- Flash loans enable "capital-free" attacks — any vulnerability involving price manipulation is amplifiable
- Upgrades introduce risk — most hacks happen right after upgrades
- Audits reduce but don't eliminate risk — many hacked protocols were audited
- Key management is as important as code security

---

## AI + ETHEREUM

### 28. How are AI agents currently using Ethereum?

This is a rapidly evolving space. What I know from training data:

1. **Trading Agents:** AI agents that execute DeFi strategies — arbitrage, yield farming, liquidity provision. Using DEX aggregators, lending protocols, etc.

2. **Autonomous Agents with Wallets:** Projects giving AI agents their own wallets to transact:
   - Pay for compute resources
   - Hire other agents/services
   - Earn revenue for completed tasks
   - Manage treasury

3. **Agent-to-Agent Payments:** AI agents paying each other for services using crypto — the "machine economy" thesis. Crypto is the natural payment rail because agents can't open bank accounts.

4. **On-chain Data Analysis:** AI reading blockchain state, analyzing transactions, identifying patterns (MEV detection, wallet profiling, risk scoring).

5. **Smart Contract Generation/Auditing:** AI tools that write and analyze Solidity code.

6. **Prediction Markets:** AI agents participating in prediction markets (e.g., Polymarket) — this got significant attention in 2024.

7. **Social/Trading Agents:** Agents like Truth Terminal that gained attention for influencing crypto markets/tokens.

**Key projects (from training data — may be outdated):**
- **Autonolas (OLAS):** Framework for autonomous agent services on-chain
- **Fetch.ai:** AI agent marketplace
- **Virtuals Protocol:** AI agent tokenization on Base
- **AI16Z/ELIZA:** Framework for AI agents with crypto capabilities
- **Morpheus:** Decentralized AI agent network

### 29. What infrastructure exists for AI agents on Ethereum?

**Wallet/Transaction Infrastructure:**
- **Safe (with modules):** Smart account wallets with spending limits, session keys — ideal for agent wallets with human oversight
- **ERC-4337 Account Abstraction:** Smart accounts with programmable validation — session keys, spending limits, whitelisted functions
- **Privy / Dynamic / Turnkey:** Embedded wallet providers — create wallets programmatically for agents
- **Fireblocks / Fordefi:** Institutional key management for high-value agent wallets

**Development Frameworks:**
- **ethers.js / viem:** JavaScript Ethereum libraries agents can use to read/write to chains
- **Foundry (cast):** CLI tool for on-chain interactions — an agent can shell out to `cast` for quick queries
- **ELIZA framework:** Open-source framework for building AI agents with crypto integrations
- **LangChain/LangGraph with Web3 tools:** Integration layers connecting LLMs to blockchain operations

**Data/Indexing:**
- **The Graph:** Query on-chain data via GraphQL — agents can read protocol state without parsing raw blockchain data
- **Dune Analytics API:** Query pre-built dashboards and datasets
- **Etherscan/Blockscout APIs:** Contract ABIs, transaction history, token balances
- **Alchemy / QuickNode Enhanced APIs:** NFT endpoints, token APIs, webhook notifications

**Oracle Infrastructure:**
- **Chainlink Functions:** Run custom off-chain computation triggered by on-chain events — could connect AI inference to smart contracts
- **UMA Optimistic Oracle:** Agents can propose and dispute oracle values

**Payment Infrastructure:**
- **Superfluid:** Stream payments per-second — pay agents continuously as they work
- **Sablier:** Token streaming
- **Request Network:** Invoicing infrastructure

**Agent-Specific Protocols:**
- **Autonolas:** Agent service framework — register agents on-chain, coordinate multi-agent systems
- **Lit Protocol:** Decentralized key management and compute — agents can have keys managed by a decentralized network
- **Phala Network:** TEE-based compute for confidential agent execution

**UNSURE** about many of the newer AI-agent-specific protocols — this space evolved extremely rapidly through 2024-2025 and my knowledge may be incomplete or outdated.

---

## Summary of Confidence Levels

| Area | Confidence |
|---|---|
| Gas & Costs (mechanics) | ✅ High — formulas and mechanics are solid |
| Gas & Costs (current prices) | ⚠️ Medium — exact current prices UNSURE |
| Wallets & Account Abstraction | ✅ High |
| L2s | ✅ High — good knowledge of the landscape |
| Token Standards (major ones) | ✅ High |
| ERC-8004 | ❌ UNSURE |
| x402 | ❌ UNSURE |
| Tools & Frameworks | ✅ High — Scaffold-ETH 2, Foundry, etc. well covered |
| abi.ninja | ⚠️ Medium — know what it does, less detail on internals |
| DeFi Protocols | ✅ High |
| Uniswap V4 | ✅ High on concepts, ⚠️ Medium on current deployment status |
| Security & Hacks | ✅ High |
| AI + Ethereum | ⚠️ Medium — rapidly evolving, knowledge likely stale |
| Why Ethereum | ✅ High |

---

*This document represents Claude Opus 4's baseline Ethereum knowledge as of its training data cutoff. No tools, web searches, or external data were used.*