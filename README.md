# ethskills-research

Research lab for [ethskills.com](https://ethskills.com) — figuring out what LLMs don't know about Ethereum.

## What's Here

### `/results/` — Baseline Audits
Stock LLM knowledge tests. We ask models Ethereum questions with no tools and see what they get wrong.

- `baseline-sonnet-4.5.md` — Claude Sonnet 4.5 baseline
- `baseline-gpt-5.2.md` — GPT-5.2 baseline  
- `baseline-opus-4.6.md` — Opus 4.6 baseline
- `gap-analysis-summary.md` — **Start here.** Compiled gaps across all models.

### `/research/` — Deep Dives
Research on specific topics LLMs don't know:

- `erc-8004.md` — Trustless agent identity standard (completely unknown to all models)
- `x402.md` — HTTP 402 payment protocol (completely unknown)
- `protocol-upgrades.md` — Pectra & Fusaka details
- `ai-ethereum-projects.md` — AI + Ethereum landscape
- `new-eips-ercs.md` — Recent standards

### `/content-mining/` — Source Material
Raw research used to build skill content:

- `contract-addresses.md` — Verified addresses
- `defi-composability.md` — Current DeFi state
- `ethereum-apps-ecosystem.md` — App landscape
- `scaffold-eth-patterns.md` — SE2 patterns
- `speedrun-ethereum.md` — SRE content
- And more

## Methodology

1. **Test** — Ask a stock LLM 20 Ethereum questions (no tools)
2. **Find gaps** — What did it get wrong or not know?
3. **Fill** — Write corrections with verified on-chain data
4. **Prune** — If the LLM already knows it, cut it
5. **Repeat** — Models improve, gaps change, content evolves

## Key Findings (Feb 2026)

All tested models (Sonnet 4.5, GPT-5.2, Opus 4.6):
- Think gas is **10-30 gwei** (reality: 0.05-0.3 gwei)
- Have **never heard of ERC-8004** (trustless agent identity)
- Have **never heard of x402** (HTTP payments)
- Think L2 transactions cost **$0.01-2.00** (reality: <$0.001)
- **Unsure if Pectra/Fusaka shipped** (both live)
- **Hallucinate contract addresses** when asked

## Related

- [ethskills site repo](https://github.com/austintgriffith/ethskills) — the published skills
- [ethskills.com](https://ethskills.com) — live site

## License

MIT
