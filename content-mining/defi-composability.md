# DeFi Composability & Money Legos

## What is Composability?

**Definition:** The ability of decentralized protocols to interact and integrate seamlessly, allowing developers to combine existing "building blocks" to create new financial applications.

**Key Characteristics:**
- **Permissionless:** No approval needed to integrate
- **Open-source:** All code is public and auditable
- **Interoperable:** Protocols can call each other
- **Atomic:** Multi-step operations in single transactions
- **Programmable:** Compose via smart contracts

**Why "Money Legos"?**
Like Lego bricks, DeFi protocols:
- Have standardized interfaces (ERC20, etc.)
- Can be stacked together
- Build complex structures from simple pieces
- Are reusable across projects

---

## Three Levels of Composability

### 1. Atomic Composability
**Definition:** Multiple operations across different protocols execute in a single transaction

**Pattern:**
```
Transaction {
  1. Borrow from Aave
  2. Swap on Uniswap
  3. Deposit into Curve
  4. All or nothing!
}
```

**Critical Property:** Either ALL steps succeed or ALL steps revert
**Why It Matters:** No intermediate states, no partial failures

### 2. Modular Integration
**Definition:** Smart contracts integrate other protocols as modules

**Example:**
```solidity
contract MyStrategy {
    IAave public aave;
    IUniswap public uniswap;
    
    function executeStrategy() external {
        // Use Aave for lending
        aave.deposit(token, amount);
        
        // Use Uniswap for swapping
        uniswap.swap(tokenA, tokenB, amount);
    }
}
```

### 3. Syntactic Composability
**Definition:** Common standards (ERC20, ERC721) allow universal integration

**Why Standards Matter:**
- Any ERC20 can work with any DEX
- Any ERC721 can work with any marketplace
- No custom integration per token

---

## Core DeFi Building Blocks

### 1. Token Standards
**ERC20:** Fungible tokens
- Base currency for DeFi
- Transferable, approvable, queryable
- Used: USDC, DAI, UNI, AAVE, etc.

**ERC721:** Non-fungible tokens
- Unique assets
- Ownership representation
- Used: NFTs, position NFTs (Uniswap V3)

**ERC1155:** Semi-fungible tokens
- Batch operations
- Mix of fungible/non-fungible
- Used: Gaming, multi-asset protocols

### 2. Decentralized Exchanges (DEXs)
**Function:** Token swapping without intermediaries

**Types:**
- **AMM (Automated Market Maker):** Uniswap, SushiSwap, Curve
- **Order Book:** dYdX, Serum
- **Aggregators:** 1inch, Matcha, CoW Protocol

**Composability Value:**
- Instant liquidity for any protocol
- Price discovery
- No custody required

### 3. Lending Protocols
**Function:** Lend/borrow assets with collateral

**Major Players:**
- **Aave:** Multi-chain, flash loans
- **Compound:** Interest-bearing tokens (cTokens)
- **Euler:** Permission-less listing

**Composability Value:**
- Leverage without selling
- Yield on idle assets
- Flash loans for instant capital

### 4. Stablecoins
**Function:** Price-stable cryptocurrencies

**Types:**
- **Fiat-backed:** USDC, USDT
- **Crypto-collateralized:** DAI (MakerDAO)
- **Algorithmic:** FRAX, LUSD

**Composability Value:**
- Stable unit of account
- Reduce volatility exposure
- Base pair for trading

### 5. Yield Aggregators
**Function:** Optimize yield farming across protocols

**Examples:**
- **Yearn Finance:** Auto-compounding strategies
- **Beefy Finance:** Multi-chain yield
- **Convex/Votium:** Boosted Curve yields

**Composability Value:**
- Abstract complexity
- Efficient capital allocation
- Automated optimization

### 6. Derivatives
**Function:** Synthetic assets, options, futures

**Examples:**
- **Synthetix:** Synthetic assets (sUSD, sBTC)
- **GMX:** Perpetual futures
- **Dopex:** Options trading

**Composability Value:**
- Exposure without holding asset
- Hedging strategies
- Leverage trading

---

## Classic Composability Patterns

### Pattern 1: Collateral Lego
**Flow:**
```
1. Deposit ETH into Aave
2. Receive aETH (interest-bearing token)
3. Use aETH as collateral in other protocol
4. aETH continues earning interest
5. Capital is used twice!
```

**Real Example:**
- Deposit USDC in Aave → Get aUSDC
- Stake aUSDC in Convex → Get rewards
- Use Convex receipt token in Curve → More rewards
- Original USDC still earning Aave interest

### Pattern 2: Flash Loan Lego
**Flow:**
```
1. Borrow 10,000 ETH (no collateral)
2. Execute arbitrage across DEXs
3. Repay loan + fee
4. Keep profit
5. All in ONE transaction
```

**Use Cases:**
- Arbitrage
- Liquidations
- Collateral swaps
- Self-liquidation

### Pattern 3: Liquidity Provider (LP) Lego
**Flow:**
```
1. Provide liquidity to Uniswap (ETH-USDC)
2. Receive LP tokens
3. Stake LP tokens in yield farm
4. Earn UNI + farm rewards
5. Compound rewards back into LP
```

### Pattern 4: Leveraged Yield Farming
**Flow:**
```
1. Deposit USDC as collateral
2. Borrow more USDC against it
3. Deposit borrowed USDC into yield farm
4. Use farm rewards to pay interest
5. Keep net profit (if rewards > interest)
```

**Risk:** Liquidation if collateral value drops

### Pattern 5: Hedged Position Lego
**Flow:**
```
1. Buy ETH on spot market
2. Short ETH on perpetual futures (GMX)
3. Net: Market neutral position
4. Earn funding rate while hedged
```

---

## Advanced Composability Examples

### Yield Maximizer Strategy
```
Step 1: Deposit USDC in Aave
  ↓ Receive aUSDC (earning interest)

Step 2: Deposit aUSDC in Curve pool
  ↓ Receive Curve LP token (earning trading fees)

Step 3: Stake Curve LP in Convex
  ↓ Receive CRV + CVX rewards

Step 4: Auto-compound rewards
  ↓ Convert CRV/CVX to more USDC
  ↓ Restart cycle

Result: 4 layers of yield on same capital!
```

### Self-Repaying Loan
```
Step 1: Deposit ETH in Alchemix
  ↓ Borrow alUSD (synthetic stablecoin)

Step 2: Deposited ETH earns yield
  ↓ Yield automatically pays off loan

Step 3: Loan self-repays over time
  ↓ Zero liquidation risk

Result: Free loan that pays itself!
```

### Cross-Protocol Arbitrage
```
Step 1: Flash loan 1000 ETH from Aave
  ↓ Pay 0.09% fee

Step 2: Identify price discrepancy
  ↓ ETH on Uniswap: $2000
  ↓ ETH on SushiSwap: $2005

Step 3: Buy on Uniswap, sell on SushiSwap
  ↓ Profit: $5 per ETH × 1000 = $5000

Step 4: Repay flash loan
  ↓ $1000 × 0.0009 = $0.90 fee

Result: $5000 - $0.90 = $4,999.10 profit
         (assuming no slippage/gas)
```

---

## Critical Interface Standards

### ERC20 Interface
```solidity
interface IERC20 {
    function transfer(address to, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
    function allowance(address owner, address spender) external view returns (uint256);
}
```

### DEX Interface (Uniswap V2)
```solidity
interface IUniswapV2Router {
    function swapExactTokensForTokens(
        uint amountIn,
        uint amountOutMin,
        address[] calldata path,
        address to,
        uint deadline
    ) external returns (uint[] memory amounts);
}
```

### Lending Interface (Aave)
```solidity
interface IPool {
    function supply(
        address asset,
        uint256 amount,
        address onBehalfOf,
        uint16 referralCode
    ) external;
    
    function borrow(
        address asset,
        uint256 amount,
        uint256 interestRateMode,
        uint16 referralCode,
        address onBehalfOf
    ) external;
}
```

---

## Composability Risks (The Double-Edged Sword)

### 1. Cascading Failures
**Problem:** If one protocol fails, all dependent protocols affected

**Example:** 
- Protocol A uses Protocol B for price oracle
- Protocol B gets hacked
- Protocol A becomes vulnerable too

**Mitigation:**
- Multiple oracle sources
- Circuit breakers
- Independent fallbacks

### 2. Complexity Risk
**Problem:** More integrations = larger attack surface

**As Gemini notes:** "All structures are only as strong as their weakest points"

**Mitigation:**
- Thorough audits of all integrations
- Understand dependencies fully
- Test edge cases

### 3. Composability Exploits
**Problem:** Combining protocols in unintended ways

**Examples:**
- Flash loan attacks manipulating oracle prices
- Reentrancy across multiple protocols
- Circular dependency exploits

### 4. Systemic Risk
**Problem:** DeFi becomes interconnected financial system

**Concern:** 
- "Too interconnected to fail"
- Contagion effects
- Black swan events cascade

---

## Best Practices for Building Composable Protocols

### 1. Use Standard Interfaces
```solidity
// GOOD: Standard ERC20
contract MyToken is ERC20 {
    // Composable with all DeFi
}

// BAD: Custom interface
contract MyToken {
    function send(address to, uint amt) external {
        // Not composable!
    }
}
```

### 2. Make Functions View When Possible
```solidity
// GOOD: Pure computation
function calculatePrice(uint a, uint b) external view returns (uint) {
    return a * b / 1e18;
}

// Allows off-chain calculation
// Composable in multi-calls
```

### 3. Emit Events for State Changes
```solidity
event Deposit(address indexed user, uint amount);

function deposit(uint amount) external {
    balances[msg.sender] += amount;
    emit Deposit(msg.sender, amount); // Essential for composability!
}
```

### 4. Avoid Protocol-Specific Logic
```solidity
// BAD: Hardcoded protocol
function mustUseUniswap() external {
    uniswap.swap(...);
}

// GOOD: Interface-based
function swap(ISwapRouter router) external {
    router.swap(...); // Works with any DEX
}
```

### 5. Document Integration Points
```solidity
/// @notice This function is designed for external protocol integration
/// @dev Safe to call from other contracts, follows CEI pattern
/// @param token The ERC20 token address
/// @return shares The amount of shares minted
function deposit(address token, uint256 amount) external returns (uint256 shares)
```

---

## Tools for Composability Development

### Integration Testing
```javascript
// Foundry example: Testing with real protocols
function testAaveUniswapComposition() public {
    // 1. Fork mainnet
    vm.createSelectFork(MAINNET_RPC);
    
    // 2. Deposit in Aave
    aave.supply(USDC, 1000e6, address(this), 0);
    
    // 3. Borrow ETH
    aave.borrow(WETH, 1e18, 2, 0, address(this));
    
    // 4. Swap on Uniswap
    uniswap.swap(WETH, USDC, 1e18);
    
    // Assert composition worked
    assertGt(IERC20(USDC).balanceOf(address(this)), 1000e6);
}
```

### Multicall Pattern
```solidity
// Execute multiple actions atomically
function multicall(bytes[] calldata data) external returns (bytes[] memory results) {
    results = new bytes[](data.length);
    for (uint256 i = 0; i < data.length; i++) {
        (bool success, bytes memory result) = address(this).delegatecall(data[i]);
        require(success);
        results[i] = result;
    }
}
```

---

## Learning Resources

### Visualize Composability
- **DeFi Llama:** Track protocol TVL and flows
- **Dune Analytics:** Custom composability queries
- **Nansen:** Money flow tracking

### Hands-On Learning
- Build a yield aggregator
- Create a flash loan arbitrage bot
- Implement leveraged farming
- Design a multi-protocol strategy

### Study Real Strategies
- **Yearn vaults:** Strategy implementations
- **Beefy contracts:** Cross-chain composability
- **Convex:** Protocol-over-protocol model

---

## What Bots Should Know

### When Explaining Composability
Use the Lego metaphor:
- "Like Lego bricks, DeFi protocols snap together"
- "Each protocol = one type of brick"
- "Build complex financial products from simple pieces"

### Key Composability Concepts to Convey
1. **Permissionless:** Anyone can integrate
2. **Atomic:** All-or-nothing execution
3. **Transparent:** All code is open
4. **Standardized:** Common interfaces (ERC20, etc.)

### Common User Questions

**"How can I use multiple protocols together?"**
→ Explain flash loans, yield aggregators, or LP staking

**"Is composability safe?"**
→ Acknowledge risks, explain audits, mention circuit breakers

**"What's the most complex composable strategy?"**
→ Multi-layer yield farming (Aave → Curve → Convex → auto-compound)

**"How do I get started with composability?"**
→ Start simple: Uniswap LP → Stake LP tokens
→ Progress: Add leverage, multiple protocols, automation

### Red Flags in Composability
- Circular dependencies
- Unbounded loops
- No error handling
- Price oracle manipulation risk
- Reentrancy across protocols

### Green Flags
- Battle-tested integrations
- Multiple audits
- Circuit breakers
- Time-locks on changes
- Clear documentation
- Established protocols (Aave, Uniswap, etc.)
