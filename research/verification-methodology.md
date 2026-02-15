# Address Verification Methodology

**Purpose:** Every contract address in ethskills.com must be verified onchain before publishing.

## Tools

### cast (Foundry)
Primary tool for onchain reads. No cost, direct RPC calls.

```bash
# Confirm bytecode exists at address
cast code <address> --rpc-url <rpc_url>

# Confirm contract identity (token symbol, name, etc.)
cast call <address> "symbol()(string)" --rpc-url <rpc_url>
cast call <address> "name()(string)" --rpc-url <rpc_url>

# Check specific function exists / works
cast call <address> "getPool(address,address,uint24)(address)" <token0> <token1> <fee> --rpc-url <rpc_url>
```

### Alchemy RPCs
Available for all major L2s:

| Chain | RPC Pattern |
|-------|-------------|
| Ethereum | `https://eth-mainnet.g.alchemy.com/v2/<key>` |
| Base | `https://base-mainnet.g.alchemy.com/v2/<key>` |
| Arbitrum | `https://arb-mainnet.g.alchemy.com/v2/<key>` |
| Optimism | `https://opt-mainnet.g.alchemy.com/v2/<key>` |
| zkSync | `https://zksync-mainnet.g.alchemy.com/v2/<key>` |

### Blockscout MCP
Available at https://mcp.blockscout.com/mcp (SSE transport, MCP protocol).
Also accessible via REST: https://base.blockscout.com/api, https://arbitrum.blockscout.com/api, etc.

## Verification Checklist

For each address:
1. [ ] `cast code` returns non-empty bytecode
2. [ ] `cast call` confirms identity (symbol, name, or key function)
3. [ ] Cross-referenced with official source (GitHub deployments, protocol docs)
4. [ ] Verification date recorded
5. [ ] Chain ID confirmed

## Sources (Priority Order)
1. Official GitHub deployment files (e.g., `deployments/42161-core.json`)
2. Protocol documentation
3. Block explorer verified contracts
4. CoinGecko (for token addresses)
5. DeFi Llama (for protocol identification)

## Anti-Patterns
- NEVER use an address from LLM memory without verification
- NEVER use an address from a random blog post without onchain confirmation
- NEVER assume CREATE2 addresses are the same on all chains (verify each)
