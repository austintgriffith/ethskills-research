# SpeedRunEthereum Learning Path

## Overview
SpeedRunEthereum is a hands-on learning platform that teaches Ethereum development through progressively challenging interactive projects.

## Challenge Progression

### Challenge #0: Tokenization 🎫
**Core Learning:**
- Create ERC721 NFT tokens
- Master Scaffold-ETH 2 basics
- Learn HardHat compilation and deployment
- Build React frontends with Ethereum components
- Deploy to public testnets
- Share NFTs with friends

**Key Concepts:**
- Smart contract basics
- Token standards (ERC721)
- Frontend-contract integration
- Public network deployment

---

### Challenge #1: Crowdfunding 🦸
**Core Learning:**
- Decentralized coordination without trust
- Group funding mechanisms
- Smart contract as arbiter
- Users only trust code, not each other

**Key Concepts:**
- Trustless systems
- Adversarial game theory
- Collective action through code
- State machine patterns

---

### Challenge #2: Token Vendor 🤖
**Core Learning:**
- Create custom ERC20 tokens
- Build automated token exchange (vending machine)
- Implement buy/sell functionality
- Master the "approve" pattern for ERC20
- Contract-to-contract interactions

**Key Concepts:**
- ERC20 token standard
- Token economics
- Approve/transferFrom pattern
- Always-on contract logic
- Automated market making basics

---

### Challenge #3: Dice Game 🎰
**Core Learning:**
- Blockchain randomness challenges
- Block hash as weak randomness source
- Exploit predictable randomness
- Attack vulnerable random number generation
- Only roll winning dice!

**Key Concepts:**
- Deterministic blockchain
- Proof-of-work relation to randomness
- Security vulnerabilities in RNG
- Front-running attacks
- Predictability exploits

**WARNING:** This teaches how NOT to do randomness!

---

### Challenge #4: Build a DEX 💵
**Core Learning:**
- Automated Market Maker (AMM) mechanics
- Constant product formula (x * y = k)
- ETH ↔ Token swapping
- Liquidity provision
- Liquidity pool tokens (LP tokens)
- Fee distribution to LPs
- Reserve ratio pricing

**Key Concepts:**
- Decentralized exchange design
- Bonding curves
- Impermanent loss
- Liquidity mining
- Slippage
- Price impact

**This is a CORE DeFi pattern!**

---

### Challenge #5: Oracles 🔮
**Core Learning:**
- Bring off-chain data on-chain
- Three oracle types:
  1. **Simple Whitelist Oracle**: Trusted addresses
  2. **Staking-Based Oracle**: Economic security
  3. **Optimistic Oracle**: Challenge periods

**Key Concepts:**
- Oracle problem
- Trust models (centralized vs decentralized)
- Economic security through staking
- Dispute resolution mechanisms
- Challenge periods
- Fraud proofs

**Critical for:** Any system needing external data (prices, events, etc.)

---

### Challenge #6: Over-Collateralized Lending 🌽
**Core Learning:**
- Collateralized debt positions (CDPs)
- Borrow against locked assets
- Liquidation mechanics when collateral drops
- Health factor calculations
- Liquidator incentives

**Key Concepts:**
- Lending/borrowing protocols
- Collateral ratios
- Liquidation cascades
- Price oracles integration
- Risk management

**Real-world examples:** MakerDAO, Compound, Aave

---

### Challenge #7: Stablecoins 💰
**Core Learning:**
- Create MyUSD stablecoin
- Collateral-backed stability
- Dynamic borrowing based on collateral value
- Liquidation system for undercollateralized positions
- Peg maintenance

**Key Concepts:**
- Stablecoin design
- Soft peg vs hard peg
- Collateralization ratios
- Stability mechanisms
- Redemption mechanics

**This builds on Challenge #6!**

---

### Challenge #8: Prediction Markets 📈
**Core Learning:**
- Create markets for future events
- Users bet on outcomes
- Trade outcome shares
- Dynamic pricing based on market belief
- Automated Market Maker for odds
- Resolution and payouts

**Key Concepts:**
- Prediction market design
- Information aggregation
- Market-based forecasting
- AMM for binary/multi-outcome events
- Oracle integration for resolution

---

### Challenge #9: ZK Voting 🔒
**Core Learning:**
- Zero-knowledge proofs in practice
- Private voting on-chain
- Results computed off-chain with ZK proofs
- Verifiable computation
- Privacy-preserving governance

**Key Concepts:**
- Zero-knowledge cryptography
- Privacy in blockchain
- Off-chain computation + on-chain verification
- ZK-SNARKs/ZK-STARKs basics
- Verifiable randomness

---

## Additional Build Ideas

### Multisig Wallet 👩‍👩‍👧‍👧
**Core Learning:**
- Contract as wallet
- Multi-signature authorization
- Transaction proposal system
- Confirmation threshold
- Execute when quorum reached

**Key Concepts:**
- Multi-sig patterns
- Gnosis Safe architecture
- M-of-N signatures
- Transaction queuing
- Role-based access

---

### SVG NFT 🎨
**Core Learning:**
- On-chain SVG generation
- Dynamic NFTs
- Contract-generated art
- Metadata on-chain
- No IPFS needed

**Key Concepts:**
- On-chain vs off-chain data
- SVG in Solidity
- Base64 encoding
- Data URIs
- Fully decentralized NFTs

---

## Advanced Resources

### ETH Tech Tree
- Advanced Solidity challenges
- Test Ethereum dev skills
- Progressive difficulty
- https://www.ethtechtree.com

### Capture the Flag
- 12 smart contract security challenges
- Hack vulnerable contracts
- Learn by breaking things
- https://ctf.buidlguidl.com

---

## Learning Path Strategy

### For Beginners:
1. Start with Challenge #0 (Tokenization)
2. Do #1 (Crowdfunding) to learn trustless coordination
3. Master #2 (Token Vendor) for ERC20 + contract interactions

### For Intermediate:
4. Attempt #3 (Dice Game) to learn security
5. Build #4 (DEX) - ESSENTIAL DeFi knowledge
6. Study #5 (Oracles) - critical infrastructure

### For Advanced:
7. Tackle #6 & #7 (Lending + Stablecoins) together
8. Explore #8 (Prediction Markets)
9. Master #9 (ZK Voting)

### Extra Credit:
- Multisig Wallet (production pattern)
- SVG NFT (on-chain creativity)
- CTF Challenges (security focus)

---

## What Bots Should Know

### Challenge Structure
Each challenge teaches ONE core concept deeply through building, not just reading.

### Progressive Difficulty
Challenges build on each other. Don't skip ahead without understanding previous concepts.

### Common Patterns Learned
- **Token standards**: ERC20, ERC721
- **DeFi primitives**: AMMs, lending, stablecoins
- **Security**: Randomness, reentrancy, access control
- **Oracles**: External data integration
- **Privacy**: Zero-knowledge proofs
- **Governance**: Multi-sig, voting

### Real-World Relevance
These aren't toy examples—they're simplified versions of:
- Uniswap (DEX)
- MakerDAO (Stablecoins)
- Aave (Lending)
- Augur (Prediction Markets)
- Gnosis Safe (Multisig)
- Chainlink (Oracles)

### Time Investment
- Each challenge: 4-8 hours for beginners
- Total curriculum: 60-100 hours
- Worth it: These are the building blocks of ALL Ethereum apps

### Critical Success Factors
1. Actually BUILD each challenge, don't just read
2. Understand WHY each pattern exists
3. Break your own contracts (security mindset)
4. Read other people's solutions
5. Deploy to testnet, share with community
