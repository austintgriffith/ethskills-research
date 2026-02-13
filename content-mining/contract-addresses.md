# Contract Address Registries & Verified Protocol Addresses

## Why Contract Addresses Matter

**Critical Security Concern:** Always verify contract addresses before interacting!
- Scammers create fake tokens with similar names
- Phishing sites display wrong addresses
- One wrong character = funds sent to attacker

**Best Practice:** Always cross-reference addresses from multiple official sources

---

## Primary Address Discovery Resources

### 1. Etherscan Verified Contracts
**URL:** https://etherscan.io/contractsVerified
**Features:**
- 831,883+ verified contracts (as of 2026)
- Source code verification
- Contract creation info
- Interaction history
- Token tracker integration

**How to Use:**
```
1. Search by contract name or address
2. Check "Contract" tab for verified source
3. Look for green checkmark ✓
4. Review contract code if needed
```

**Red Flags:**
- No verified source code
- Recently deployed (<1 month)
- Low transaction count
- Similar name to popular token

### 2. CoinGecko
**URL:** https://www.coingecko.com
**Purpose:** Token contract addresses with price data

**Info Provided:**
- Contract address per chain
- Token decimals
- Total supply
- Market cap
- Official links

**Multi-Chain Support:**
- Ethereum, Polygon, BSC, Arbitrum, Optimism, etc.
- Shows same token across different chains

### 3. CoinMarketCap
**URL:** https://coinmarketcap.com
**Similar to CoinGecko:**
- Contract addresses
- Network selection dropdown
- Official links
- Explorers

**How to Find:**
```
1. Search token name
2. Click token page
3. Scroll to "Contracts" section
4. Select network
5. Copy address with 📋 icon
```

### 4. DefiLlama
**URL:** https://defillama.com
**Focus:** DeFi protocol addresses and TVL

**Provides:**
- Protocol contracts per chain
- Treasury addresses
- Governance contracts
- Token addresses
- Historical TVL

---

## Protocol-Specific Registries

### Uniswap
**Docs:** https://docs.uniswap.org/contracts/v3/reference/deployments/

**Core Contracts:**

**Ethereum Mainnet:**
```
UniswapV3Factory: 0x1F98431c8aD98523631AE4a59f267346ea31F984
SwapRouter: 0xE592427A0AEce92De3Edee1F18E0157C05861564
NonfungiblePositionManager: 0xC36442b4a4522E871399CD717aBDD847Ab11FE88
```

**Pool Addresses:**
- Each pool = unique UniswapV3Pool instance
- Deterministic address from factory
- Auto-verified on Etherscan
- Example: ETH/USDC 0.3% pool

**Multi-Chain:**
- Same addresses across Optimism, Arbitrum, Polygon, etc.
- Create2 deterministic deployment

### Aave
**Docs:** https://docs.aave.com/developers/deployed-contracts/deployed-contracts
**Dashboard:** https://aave.com/docs/resources/addresses

**Architecture:**
```
PoolAddressesProvider → Main registry
    ├── Pool (lending/borrowing)
    ├── PoolConfigurator
    ├── Oracle
    └── ACLManager
```

**Key Pattern:** Use PoolAddressesProvider as entry point
- Acts as registry for all protocol contracts
- Addresses can be upgraded
- Single source of truth per market

**Markets:**
- Ethereum Mainnet
- Optimism
- Arbitrum
- Polygon
- Avalanche
- Fantom
- Harmony
- Base
- Metis
- Gnosis Chain
- BNB Chain
- Scroll

**Example Query:**
```solidity
IPoolAddressesProvider provider = IPoolAddressesProvider(PROVIDER_ADDRESS);
address pool = provider.getPool();
address oracle = provider.getPriceOracle();
```

### Compound
**Docs:** https://docs.compound.finance/v2/

**Core Contracts (Ethereum):**
```
Comptroller: 0x3d9819210A31b4961b30EF54bE2aeD79B9c9Cd3B
cETH: 0x4Ddc2D193948926D02f9B1fE9e1daa0718270ED5
cUSDC: 0x39AA39c021dfbaE8faC545936693aC917d5E7563
cDAI: 0x5d3a536E4D6DbD6114cc1Ead35777bAB948E3643
```

**Pattern:** Each market = separate cToken contract
- cETH, cUSDC, cDAI, etc.
- Query Comptroller for all markets
- Interest-bearing tokens

### MakerDAO (DAI)
**Core Contracts:**
```
DAI Token: 0x6B175474E89094C44Da98b954EedeAC495271d0F
DAI Savings (DSR): 0x197E90f9FAD81970bA7976f33CbD77088E5D7cf7
MCD_VAT (Core): 0x35D1b3F3D7966A1DFe207aa4514C12a259A0492B
```

### Curve Finance
**Registry Pattern:**
- Main Registry contract
- MetaRegistry (aggregates multiple registries)
- Pool Registry
- Factory contracts

**Why Complex:**
- Multiple pool types (StableSwap, CryptoSwap, etc.)
- Factory-deployed pools
- Metapools

**Best Approach:** Use official UI or registry contracts

---

## Address Verification Process

### Step-by-Step Verification

#### 1. Official Website
```
✓ Visit official domain (check SSL, spelling)
✓ Find "Contracts" or "Developers" page
✓ Copy address from official docs
```

#### 2. Cross-Reference
```
✓ Check same address on CoinGecko
✓ Verify on CoinMarketCap
✓ Confirm on official GitHub
✓ Review on block explorer
```

#### 3. Etherscan Validation
```
✓ Search address on Etherscan
✓ Verify green checkmark (source verified)
✓ Check contract name matches
✓ Review creation date (not too recent)
✓ Check transaction history (active?)
✓ Verify token tracker info (if ERC20)
```

#### 4. Community Confirmation
```
✓ Check official Discord/Telegram
✓ Ask in community channels
✓ Verify on protocol governance forum
✓ Check official Twitter
```

---

## Common Address Lookup Patterns

### For ERC20 Tokens

**MetaMask Method:**
```
1. Visit token page (CoinGecko/CMC)
2. Find "Contracts" section
3. Select network (Ethereum, Polygon, etc.)
4. Copy address
5. Add to MetaMask custom token
```

**Wallet Integration:**
- MetaMask has built-in token list
- Trust Wallet has token registry
- Rainbow auto-detects popular tokens

### For Protocol Interactions

**Web3 Developer Pattern:**
```javascript
// Use official SDK
import { UNISWAP_V3_FACTORY } from '@uniswap/sdk-core';

// Or import from verified source
const FACTORY_ADDRESS = '0x1F98431c8aD98523631AE4a59f267346ea31F984';
```

**Hardhat/Foundry Pattern:**
```solidity
// Import from npm package
import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Factory.sol";

// Or use constants file
contract Config {
    address constant UNISWAP_V3_FACTORY = 0x1F98431c8aD98523631AE4a59f267346ea31F984;
}
```

---

## Address Lists & Registries

### Token Lists Standard
**Uniswap Token Lists:** https://tokenlists.org/
- JSON format
- Versioned
- Community-maintained
- Used by many DEXs

**Example:**
```json
{
  "name": "Uniswap Default List",
  "tokens": [
    {
      "chainId": 1,
      "address": "0x6B175474E89094C44Da98b954EedeAC495271d0F",
      "name": "Dai Stablecoin",
      "symbol": "DAI",
      "decimals": 18
    }
  ]
}
```

### DeFi Addresses Aggregators
- **DeFi Llama:** TVL + contract addresses
- **DeBank:** Multi-chain address tracker
- **Zapper:** Protocol addresses with integrations

---

## Major Protocol Addresses Quick Reference

### Stablecoins (Ethereum Mainnet)
```
USDC:  0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
USDT:  0xdAC17F958D2ee523a2206206994597C13D831ec7
DAI:   0x6B175474E89094C44Da98b954EedeAC495271d0F
FRAX:  0x853d955aCEf822Db058eb8505911ED77F175b99e
LUSD:  0x5f98805A4E8be255a32880FDeC7F6728C6568bA0
```

### DEX Routers (Ethereum Mainnet)
```
Uniswap V2:  0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D
Uniswap V3:  0xE592427A0AEce92De3Edee1F18E0157C05861564
SushiSwap:   0xd9e1cE17f2641f24aE83637ab66a2cca9C378B9F
Curve:       [Multiple registries - see docs]
Balancer:    0xBA12222222228d8Ba445958a75a0704d566BF2C8
```

### Lending Protocols (Ethereum Mainnet)
```
Aave V3 Pool: 0x87870Bca3F3fD6335C3F4ce8392D69350B4fA4E2
Compound:     0x3d9819210A31b4961b30EF54bE2aeD79B9c9Cd3B
Euler:        0x27182842E098f60e3D576794A5bFFb0777E025d3
```

### Oracles
```
Chainlink ETH/USD:  0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419
Uniswap V3 Oracle:  [Use pool TWAP]
```

---

## Security Best Practices

### DO ✅
- Verify addresses from multiple official sources
- Check Etherscan verification status
- Use address from official documentation
- Bookmark official sites
- Use hardware wallet for large amounts
- Test with small amount first

### DON'T ❌
- Copy address from random website
- Trust addresses from social media only
- Skip verification steps
- Use addresses from Google ads
- Ignore browser security warnings
- Trust "similar" names (USDC vs USDC2)

---

## Address Format & Checksums

### Ethereum Address Format
```
- 40 hexadecimal characters (0-9, a-f)
- Prefixed with 0x
- Total length: 42 characters
- Example: 0x6B175474E89094C44Da98b954EedeAC495271d0F
```

### EIP-55 Checksum
**Mixed case = checksum:**
```
Checksum:    0x6B175474E89094C44Da98b954EedeAC495271d0F
No Checksum: 0x6b175474e89094c44da98b954eedeac495271d0f
```

**Tools validate checksum automatically:**
- MetaMask warns on invalid checksum
- Web3 libraries validate
- Etherscan shows checksum format

---

## Finding New Protocol Addresses

### Launch Announcement Pattern
```
1. Check official Twitter
2. Read announcement blog post
3. Visit official docs
4. Check GitHub repository
5. Verify on Etherscan
6. Confirm on Discord/Telegram
```

### Alpha Research Pattern
```
1. DeFi Llama new listings
2. Dune Analytics new contracts
3. Etherscan recent verifications
4. Twitter crypto security accounts
5. Protocol governance forums
```

---

## Multi-Chain Considerations

### Same Protocol, Different Chains
**Pattern:** Many protocols use SAME addresses across chains (CREATE2)

**Example - Uniswap V3:**
```
Ethereum:  0x1F98431c8aD98523631AE4a59f267346ea31F984
Optimism:  0x1F98431c8aD98523631AE4a59f267346ea31F984
Arbitrum:  0x1F98431c8aD98523631AE4a59f267346ea31F984
Polygon:   0x1F98431c8aD98523631AE4a59f267346ea31F984
```

**But tokens differ:**
```
USDC Ethereum:  0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
USDC Polygon:   0x2791Bca1f2de4661ED88A30C99A7a9449Aa84174
USDC Arbitrum:  0xFF970A61A04b1cA14834A43f5dE4533eBDDB5CC8
```

### Bridge Wrapped Assets
**Wrapped tokens have different addresses:**
```
WETH (Wrapped ETH): 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
WBTC (Wrapped BTC): 0x2260FAC5E5542a773Aa44fBCfeDf7C193bc2C599
stETH (Lido):       0xae7ab96520DE3A18E5e111B5EaAb095312D7fE84
```

---

## What Bots Should Know

### When User Asks for Address
1. Confirm which network (Ethereum? Polygon?)
2. Verify protocol name (spelling matters!)
3. Provide address with source link
4. Remind to verify independently
5. Warn about scams

### Example Response Template
```
The official Uniswap V3 SwapRouter address on Ethereum Mainnet is:
0xE592427A0AEce92De3Edee1F18E0157C05861564

Sources:
- Official Docs: https://docs.uniswap.org/contracts/v3/reference/deployments/
- Etherscan: https://etherscan.io/address/0xE592427A0AEce92De3Edee1F18E0157C05861564

⚠️ Always verify addresses from multiple official sources before interacting!
```

### Red Flags to Mention
- Recently created contracts
- Unverified source code
- Low transaction count
- Suspicious token names
- Mismatched checksums

### Tools to Recommend
- Etherscan for verification
- CoinGecko/CoinMarketCap for tokens
- Official protocol docs
- Token Lists (tokenlists.org)
- Hardware wallet (always!)
