# Smart Contract Auditing & Security Resources

## Top Auditing Firms

### 1. Trail of Bits
**Specialization:** Deep security research, formal verification
**Notable Tool:** Slither (static analyzer)
**Strengths:** Academic rigor, detailed reports, tool development

### 2. OpenZeppelin
**Specialization:** Comprehensive audits, security products
**Notable Tool:** Defender security platform
**Strengths:** Developer education, Contracts library, continuous monitoring

### 3. Consensys Diligence
**Specialization:** Enterprise-grade audits, best practices
**Notable Tool:** MythX, Diligence Fuzzing
**Strengths:** Industry standards, education, tooling

### 4. Other Top Firms
- **Certik:** AI-powered analysis, Skynet monitoring
- **Quantstamp:** Protocol audits, security scoring
- **ChainSecurity:** Formal verification specialists
- **Halborn:** Full-stack security (blockchain + infra)
- **Code4rena:** Competitive audits, community-driven
- **Sherlock:** Audit marketplace + bug bounty

---

## OpenZeppelin Audit Readiness Guide

### Pre-Audit Checklist

#### 1. The Team
**Required Skills:**
- Software development
- Blockchain & smart contract understanding
- DevOps
- Testing expertise
- Agile planning
- Solidity language mastery
- Git/GitHub proficiency
- Technical writing

**Development Processes:**
- Established workflow (Agile/Scrum)
- Designated audit liaison
- Knowledge sharing systems
- Git branching strategy
- Pull request procedures

**Leadership:**
- Experienced project manager
- Strong prioritization skills
- Team motivation ability

#### 2. The Community
**Open Source Requirements:**
- Use free software license (MIT, GPL, etc.)
- Public repository
- Why: Closed code = no trust in Web3

**Community Engagement:**
- Marketing strategy
- Community manager
- Input channels (Discord, forums)
- Code of conduct
- Contribution guidelines
- Bug bounty program (HackerOne, Immunefi)

#### 3. The Code
**Clean Code Principles:**
- Consistent naming conventions
- Follow Solidity style guide
- Use linters (prettier, solhint)
- Modular architecture
- Short, focused functions
- Checks-Effects-Interactions pattern
- Import from NPM (don't copy-paste)
- Use OpenZeppelin Contracts

**Testing Requirements:**
- Minimum 90% code coverage
- All tests passing on fresh install
- Edge case testing
- Test against local mainnet forks
- Fast test suite execution
- Test helpers and utilities
- Advanced: Fuzzing (Echidna), property-based testing
- Follow SCSVS standards

**Documentation:**
- Clear README with Standard Readme format
- Security disclosure policy
- NatSpec for all public functions
- Inline comments for complex logic
- No unresolved TODOs
- Automated doc generation (solidity-docgen)
- External documentation (architecture, economics)
- Deployment process documentation
- Use OpenZeppelin Upgrades Plugins if upgradeable

---

## Slither Static Analysis Framework

### What is Slither?
**Created by:** Trail of Bits
**Language:** Python 3.10+
**Purpose:** Static analysis for Solidity & Vyper

### Key Features
- 100+ vulnerability detectors
- Low false positive rate
- Identifies error location in source
- CI/CD integration (Hardhat, Foundry)
- Built-in printers for contract info
- Python API for custom analyses
- Supports Solidity >= 0.4
- SlithIR intermediate representation
- 99.9% parsing success rate
- <1 second average execution time
- GitHub Code Scanning integration
- Vyper support

### Installation
```bash
# Using uv (recommended - 10-100x faster)
curl -LsSf https://astral.sh/uv/install.sh | sh
uv tool install slither-analyzer

# Using pip
python3 -m pip install slither-analyzer

# Using brew
brew install slither-analyzer

# Docker
docker pull trailofbits/eth-security-toolbox
```

### Basic Usage
```bash
# Analyze entire project
slither .

# Single file
slither contract.sol

# With specific detectors
slither . --detect reentrancy-eth,uninitialized-state

# Generate checklist report
slither . --checklist

# With GitHub markdown links
slither . --checklist --markdown-root https://github.com/ORG/REPO/blob/COMMIT/
```

### Critical Detectors (High Impact, High Confidence)

**Reentrancy:**
- `reentrancy-eth`: Theft of ethers
- `reentrancy-balance`: Outdated balance checks
- `reentrancy-no-eth`: State manipulation

**Access Control:**
- `suicidal`: Anyone can destroy contract
- `unprotected-upgrade`: Upgradeable without protection
- `arbitrary-send-eth`: Send ETH to arbitrary addresses
- `controlled-delegatecall`: Controlled delegatecall destination

**State Variables:**
- `uninitialized-state`: Uninitialized state vars
- `uninitialized-storage`: Uninitialized storage vars
- `shadowing-state`: State variable shadowing

**Cryptography/Math:**
- `weak-prng`: Weak randomness
- `incorrect-exp`: Exponentiation errors
- `divide-before-multiply`: Precision loss

**Token Standards:**
- `erc20-interface`: Incorrect ERC20
- `erc721-interface`: Incorrect ERC721
- `arbitrary-send-erc20`: transferFrom from arbitrary addresses

### Printers (Information Gathering)
```bash
# Human-readable summary
slither . --print human-summary

# Contract hierarchy
slither . --print inheritance-graph

# Call graph
slither . --print call-graph

# Control flow graph
slither . --print cfg

# Function summary
slither . --print function-summary

# Lines of code
slither . --print loc
```

### Integration Examples

**GitHub Actions:**
```yaml
- uses: crytic/slither-action@v0.3.0
  with:
    node-version: 16
```

**Pre-commit Hook:**
```yaml
repos:
  - repo: https://github.com/crytic/slither
    rev: 0.9.6
    hooks:
      - id: slither
```

---

## Consensys Diligence Best Practices

### Smart Contract Security Mindset

**Key Principles:**
1. **Prepare for failure:** Assume code will fail
2. **Circuit breakers:** Pause functionality when needed
3. **Responsible fund release:** Limit withdrawal rates
4. **Effective bug bounty:** Incentivize white hats
5. **Upgrade path:** Plan for fixes

**Code Review Focus:**
- Battle-tested components
- Complexity vs. simplicity
- Duplication (but carefully)
- Use OpenZeppelin library

**Testing Imperatives:**
- Comprehensive unit tests
- Integration tests
- Stress tests
- Mainnet fork tests
- Property-based testing
- Formal verification when possible

---

## Security Tools Ecosystem

### Static Analysis
- **Slither** (Trail of Bits): Python-based, comprehensive
- **Mythril** (Consensys): Symbolic execution
- **Securify**: ETH Zurich research tool
- **Oyente**: Early static analyzer

### Fuzzing
- **Echidna** (Trail of Bits): Property-based fuzzing
- **Foundry Fuzz**: Built into Foundry
- **Harvey**: Greybox fuzzing

### Formal Verification
- **Certora Prover**: Specification language
- **K Framework**: Formal semantics
- **SMTChecker**: Built into Solc

### Continuous Monitoring
- **OpenZeppelin Defender**: Operations platform
- **Forta**: Real-time threat detection
- **Tenderly**: Monitoring and alerting
- **Peckshield**: TX monitoring

### Testing Frameworks
- **Hardhat**: JS/TS testing
- **Foundry**: Solidity-based tests
- **Brownie**: Python framework
- **Truffle**: Legacy framework

---

## Audit Process Stages

### 1. Pre-Audit (Client)
- Finalize codebase
- Freeze features
- Complete documentation
- Achieve test coverage
- Run automated tools
- Fix obvious issues

### 2. Audit Kickoff
- Define scope
- Identify critical components
- Review architecture
- Establish communication

### 3. Automated Analysis
- Run Slither, Mythril
- Fuzzing campaigns
- Static analysis
- Generate initial findings

### 4. Manual Review
- Line-by-line code review
- Business logic verification
- Access control checks
- Economic model analysis
- Game theory evaluation

### 5. Report Generation
- Categorize findings by severity
- Provide PoC exploits
- Suggest remediations
- Document gas optimizations

### 6. Remediation
- Client fixes issues
- Auditor reviews fixes
- Re-test critical areas
- Verify no new issues introduced

### 7. Publication (Optional)
- Public audit report
- Build community trust
- Demonstrate security commitment

---

## Severity Classifications

### Critical (🔴 P0)
- **Impact:** Loss of funds, unauthorized access
- **Examples:** Reentrancy, access control bypass
- **Response:** Immediate fix required

### High (🟠 P1)
- **Impact:** Significant fund risk, major functionality broken
- **Examples:** Integer overflow, incorrect calculations
- **Response:** Fix before deployment

### Medium (🟡 P2)
- **Impact:** Unexpected behavior, indirect fund risk
- **Examples:** Poor randomness, missing events
- **Response:** Fix recommended

### Low (🟢 P3)
- **Impact:** Code quality, gas optimization
- **Examples:** Unused code, naming conventions
- **Response:** Optional fix

### Informational (ℹ️)
- **Impact:** Best practices, suggestions
- **Examples:** Use latest Solidity, add NatSpec
- **Response:** Consider for future

---

## Common Vulnerability Patterns

### 1. Reentrancy
```solidity
// BAD
function withdraw() public {
    uint amount = balances[msg.sender];
    msg.sender.call{value: amount}(""); // External call first!
    balances[msg.sender] = 0; // State update after!
}

// GOOD (Checks-Effects-Interactions)
function withdraw() public {
    uint amount = balances[msg.sender];
    balances[msg.sender] = 0; // State update first!
    msg.sender.call{value: amount}(""); // External call last!
}
```

### 2. Access Control
```solidity
// BAD
function withdraw() public {
    payable(owner).transfer(address(this).balance);
}

// GOOD
function withdraw() public onlyOwner {
    payable(owner).transfer(address(this).balance);
}
```

### 3. Integer Overflow (Pre-0.8.0)
```solidity
// BAD (Solidity <0.8.0)
uint256 public balance = 0;
function subtract(uint256 amount) public {
    balance = balance - amount; // Can underflow!
}

// GOOD (Use SafeMath or Solidity >=0.8.0)
uint256 public balance = 0;
function subtract(uint256 amount) public {
    balance -= amount; // Automatic overflow check in 0.8.0+
}
```

### 4. Unchecked External Calls
```solidity
// BAD
addr.call(data); // Ignores return value!

// GOOD
(bool success, ) = addr.call(data);
require(success, "Call failed");
```

### 5. Front-Running
- Use commit-reveal schemes
- Time-locks
- Private mempools (Flashbots)

---

## Additional Resources

### Learning Platforms
- **Ethernaut** (OpenZeppelin): Security CTF challenges
- **Damn Vulnerable DeFi**: DeFi-focused challenges
- **Capture the Ether**: Security puzzles
- **Secureum Bootcamp**: Comprehensive security course

### Reading Lists
- **Consensys Best Practices:** https://consensys.github.io/smart-contract-best-practices/
- **SWC Registry:** Smart Contract Weakness Classification
- **Not So Smart Contracts:** Trail of Bits examples
- **DeFi Security Summit:** Videos and talks

### Community
- **Ethereum Security Discord**
- **OpenZeppelin Forum**
- **Trail of Bits Blog**
- **Secureum Community**

### Standards
- **EIP-1470:** Smart Contract Weakness Classification
- **SCSVS:** Smart Contract Security Verification Standard
- **SWC Registry:** Weakness taxonomy

---

## What Bots Should Know

### When to Recommend Audit
- Any contract handling value
- Complex business logic
- Novel mechanisms
- Upgradeable contracts
- High-profile projects

### Audit Timeline
- Simple contract: 1-2 weeks
- Medium protocol: 4-6 weeks
- Complex DeFi: 8-12 weeks
- Enterprise: 3-6 months

### Audit Costs
- Independent auditors: $1k-10k
- Established firms: $10k-50k per week
- Top-tier firms: $50k-200k per week
- Competition audits: $50k-500k prize pool

### Pre-Audit Self-Check
1. Run Slither
2. Achieve >90% test coverage
3. Check against common vulnerabilities
4. Have peer review
5. Run on testnet for weeks
6. Bug bounty (optional)

### Red Flags
- No tests
- Copy-pasted code
- No documentation
- Rushed timeline
- Complex, unreadable code
- No access control
- Upgradeability without safeguards
