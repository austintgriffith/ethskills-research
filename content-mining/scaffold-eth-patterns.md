# Scaffold-ETH 2 Patterns & Best Practices

## Core Technology Stack

### Frontend
- **NextJS**: Building modern React applications with server-side rendering
- **RainbowKit**: Wallet connection UI components
- **Wagmi**: React Hooks for Ethereum interactions
- **Viem**: Low-level Ethereum primitives (modern alternative to ethers.js/web3.js)
- **DaisyUI + Tailwind CSS**: Pre-built UI components

### Backend (Smart Contracts)
- **Hardhat OR Foundry**: Developer's choice for contract development
- **TypeScript**: Type-safe development across the stack

## Key Features

### 1. Contract Hot Reload
- Frontend automatically adapts when smart contracts are edited
- No manual ABI syncing needed
- Instant feedback loop for development

### 2. Burner Wallet & Local Faucet
- Quick testing without mainnet funds
- Ephemeral wallets for development
- Built-in faucet for local networks

### 3. Multi-Wallet Provider Integration
- Seamless connection to different wallets
- Network switching handled automatically

## Development Workflow Patterns

### Contract Deployment Pattern
```bash
# Deploy all contracts
yarn deploy

# Deploy to specific network
yarn deploy --network sepolia

# Deploy with tags (specific contracts)
yarn deploy --tags tagExample
```

### Contract Verification
```bash
# Verify all deployed contracts
yarn verify --network sepolia

# Alternative: Hardhat verify with args
yarn hardhat-verify --network sepolia <address> "Constructor arg"
```

### Network Configuration Pattern
```typescript
// hardhat.config.ts
networks: {
  base: {
    url: "https://mainnet.base.org",
    accounts: [deployerPrivateKey]
  }
}
```

### Account Management
```bash
# Generate new deployer account
yarn generate

# Import existing private key
yarn account:import

# Check account balance
yarn account

# Reveal private key (security risk!)
yarn account:reveal-pk
```

## Essential Hooks

### useScaffoldContract
Get contract instance for direct interaction:
```typescript
const { data: yourContract } = useScaffoldContract({
  contractName: "YourContract",
  chainId: 31337,
  walletClient,
});

// Can call methods directly
await yourContract?.read.greeting();
await yourContract?.write.setGreeting(["new greeting"]);
```

### useScaffoldReadContract
Read public variables and view functions:
```typescript
const { data: totalCounter } = useScaffoldReadContract({
  contractName: "YourContract",
  functionName: "userGreetingCounter",
  args: ["0xd8da6bf26964af9d7eed9e03e53415d37aa96045"],
  watch: true, // Auto-refresh on new blocks
});
```

### useScaffoldWriteContract
Send transactions to modify state:
```typescript
const { writeContractAsync } = useScaffoldWriteContract({ 
  contractName: "YourContract" 
});

// Usage in component
await writeContractAsync({
  functionName: "setGreeting",
  args: ["The value to set"],
  value: parseEther("0.1"), // For payable functions
  onBlockConfirmation: (txReceipt) => {
    console.log("Transaction confirmed!", txReceipt);
  },
  blockConfirmations: 1,
});
```

## Scaffold UI Components

### Pre-built Components
1. **Address**: Display Ethereum addresses with ENS support, blockie avatar, and copy functionality
2. **AddressInput**: Input field with ENS resolution
3. **Balance**: Display ETH or ERC20 token balances with USD conversion toggle
4. **EtherInput**: Input field for ETH amounts with real-time USD conversion

### Debug Contracts Component
- Automatically generated UI for deployed contracts
- Interactive contract debugging
- Call any function directly from UI
- View all contract state

## Deployment Best Practices

### 1. Environment Setup
- Use `.env` files for sensitive data
- Store encrypted private keys (DEPLOYER_PRIVATE_KEY_ENCRYPTED)
- Keep passwords secure and memorable

### 2. Pre-Deployment Checklist
- Code finalized and frozen
- 100% test coverage (aim for 90%+)
- All tests passing
- Documentation complete
- Security audit if handling value

### 3. API Keys for Production
Replace default keys with your own:
- **ALCHEMY_API_KEY**: For RPC access (both hardhat and nextjs .env files)
- **ETHERSCAN_API_KEY**: For contract verification

### 4. Network Configuration
- Use Alchemy docs for network-specific URLs
- Configure proper gas settings per network
- Set appropriate block confirmations

## Testing Patterns

### Integration with Local Forks
```typescript
// Use Hardhat forking for mainnet testing
// Test against real protocol state
// Validate edge cases with actual data
```

### Test Coverage Goals
- Minimum 90% code coverage
- Test all edge cases
- Include integration tests against forks
- Test authorization and access control
- Validate state changes

## Smart Contract Development Best Practices

### 1. Use Battle-Tested Libraries
- Import OpenZeppelin contracts via NPM
- Don't copy-paste code
- Keep dependencies up to date

### 2. Follow Conventions
- Checks-Effects-Interactions pattern
- Fail early and loudly
- Consistent naming conventions
- Follow Solidity style guide

### 3. Modular Architecture
- Short, focused functions
- Well-encapsulated modules
- Clear separation of concerns
- Easy navigation of codebase

## Key Files Structure
```
packages/
├── hardhat/
│   ├── contracts/
│   ├── deploy/
│   ├── test/
│   └── hardhat.config.ts
└── nextjs/
    ├── app/
    ├── components/
    └── .env.local
```

## What Bots Need to Know

### Contract Interaction Pattern
1. Contracts auto-generate TypeScript types
2. Frontend components auto-discover deployed contracts
3. ABIs synced automatically between hardhat and nextjs
4. Contract addresses tracked in deployments/ folder

### Development Lifecycle
1. Write Solidity contract
2. Write tests
3. Deploy locally (`yarn chain` + `yarn deploy`)
4. Frontend immediately usable
5. Iterate on contract
6. Hot reload updates frontend automatically

### Network Targeting
- Default network set in hardhat.config.ts
- Override with --network flag
- Frontend reads from deployments/<chainId>/
- Multiple networks supported simultaneously

### Common Pitfalls to Avoid
- Not running `yarn chain` before deploying locally
- Missing environment variables
- Not generating account before deploying
- Forgetting to verify contracts on production
- Using default API keys in production
