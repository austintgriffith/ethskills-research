# Content Triage Methodology

**Purpose:** How to critically evaluate proposed additions or changes to ethskills.com. Every line on ethskills must earn its place by filling a verified LLM blind spot. This document describes the process for deciding what stays, what gets compressed, and what gets cut.

## The Core Question

For every piece of content:

> "Would a stock LLM get this wrong?"

If no → cut it. You're wasting context tokens on something the agent already knows.

## The Process

### Step 1: Classify Every Item

Go through each item in the proposed content and sort it into three buckets:

| Bucket | Meaning | Action |
|--------|---------|--------|
| 🔴 **LLM blind spot** | Stock LLMs consistently get this wrong, even when asked to do it well | **KEEP** — this is the whole point of ethskills |
| 🟡 **LLM knows but skips** | The LLM knows the concept but won't do it unless explicitly told | **COMPRESS** to one line or a checklist item |
| 🟢 **LLM does this naturally** | Any competent model does this unprompted | **CUT** — wasting context tokens |

### Step 2: Run a Baseline Test

Don't guess. Test.

**Setup:** Spawn a fresh LLM session with NO tools, NO skill docs, NO web access. Pure training data. Use a current-gen model (Sonnet 4.5, GPT-5.2, etc.) — the same class of model that will be building with ethskills.

**Prompt pattern:** Give the LLM a realistic task that exercises the content you're evaluating. Don't ask "do you know about X?" — ask it to BUILD something, then examine what it produces.

Example for the QA skill:
```
You are a coding agent. A user asks you to build a token staking dApp 
using Scaffold-ETH 2. The app should:
- Let users approve and stake an ERC20 token
- Show their staked balance
- Let them withdraw

Write the COMPLETE main page component. Include all imports, hooks, 
state management, and JSX. Make it production-ready.

Also list what changes you'd make to:
1. scaffold.config.ts
2. app/layout.tsx (metadata)
3. README.md
4. The footer component
```

**Key principle:** The prompt should be natural — the kind of thing a real user would ask. Don't hint at the answers you're looking for. You want to see what the LLM does *unprompted*.

### Step 3: Analyze the Output

Go through the LLM's output and check every item from your proposed content:

- **Did the LLM get it right without being told?** → 🟢 Cut it
- **Did the LLM mention it but get the details wrong?** → 🟡 Compress to the correct detail
- **Did the LLM completely miss it or actively do the wrong thing?** → 🔴 Keep it

Document the results. Be specific — "the LLM wrote `<p>Please connect your wallet</p>` instead of rendering a connect button" is more useful than "failed on wallet connection."

### Step 4: Run Multiple Models (Optional but Recommended)

A blind spot on one model might not be a blind spot on another. If something is borderline (one model gets it right, another doesn't), keep it but compress it. The skill needs to work across models.

### Step 5: Write the Pruned Version

With your classification done, rewrite the content:
- 🔴 items get full treatment — explanation, code examples, the "why"
- 🟡 items get one line in a checklist
- 🟢 items get deleted entirely

The goal is a document where **every line earns its place**. A reviewer agent reading a 95-line skill doc is more effective than one reading a 205-line doc where half is stuff it already knows.

## Example: QA Skill Triage (February 2026)

Original PR: 205-line QA pre-ship audit checklist.

**Baseline test:** Stock Sonnet 4.5 asked to build a production-ready SE2 staking dApp.

### What the LLM got RIGHT (cut from skill):
- Used `formatEther()` / `parseEther()` correctly
- Used `useScaffoldWriteContract` (not raw wagmi)
- Added loading spinners on buttons
- Used `<Address/>` component
- Made the layout responsive
- Had proper error handling

### What the LLM got WRONG (kept in skill):
- **Wallet connection:** Wrote `"Please connect your wallet to continue"` as text, not a button
- **Network switching:** Zero wrong-network handling anywhere
- **Approve flow:** Both approve and action buttons visible simultaneously (no four-state flow)
- **SE2 branding:** Left the entire stock SE2 footer, tab title, README intact
- **OG image:** Relative URL (`/thumbnail.jpg`), not absolute
- **USD values:** Zero USD conversion anywhere
- **Contract address display:** Showed wallet address but not the contract address
- **pollingInterval:** Left at 30000 (default) instead of 3000
- **RPC overrides:** Used the default Alchemy key that ships with SE2

**Result:** 205 lines → 95 lines. More effective because every line targets a verified blind spot.

## When to Re-Test

- When a new model generation ships (GPT-6, Claude 5, etc.) — training data changes
- When SE2 has a major release — the scaffold itself changes
- When a skill hasn't been updated in 6+ months — drift happens silently

## Anti-Patterns

- **Don't trust your intuition alone.** "I think agents get this wrong" is not evidence. Run the test.
- **Don't ask leading questions.** "Do you know to use absolute URLs for OG images?" will get a "yes." Ask the LLM to build something and see if it actually does it.
- **Don't keep content because it's correct.** Correct isn't the bar. The bar is: does the LLM need to be told this? A lot of correct information is already in training data.
- **Don't skip the test because it takes time.** A 90-second baseline test saved us from shipping 110 lines of content that would have diluted the 95 lines that actually matter.
