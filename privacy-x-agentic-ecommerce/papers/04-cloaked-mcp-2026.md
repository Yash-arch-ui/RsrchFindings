# Cloaked MCP Server

## Metadata

- Author/vendor: CloakedAgent (cloakedagent.com), listed on Glama.
- Date: no publication date; related-server timestamps are 2026; accessed 2026-10-23.
- Venue/ID: Glama MCP server directory, "Cloaked MCP Server", Blockchain/Cryptocurrency/Payments & Billing. Not a paper or peer-reviewed.
- Length: 273 lines of page text in the local capture.
- Type: vendor product page and MCP listing with a short architecture/privacy section.
- Read status: full local page text read. Tags: [V] vendor claim, [A] independently verified fact, [I] inference. No [A] evidence (audit, source review, on-chain verification, or benchmark) appears on the page.

## One-paragraph summary

Cloaked is a vendor-built MCP server and TypeScript SDK for giving an AI agent a Solana spending account whose limits are enforced by an on-chain program rather than by the agent's own code. The owner keeps a human wallet with full control; the agent gets a delegate key that can spend SOL or USDC only within per-transaction, daily, lifetime, and expiry limits, with an instant freeze. The vendor argues that agent-side limits fail because a jailbroken, buggy, or prompt-injected agent holds the keys and can bypass its own rules, whereas on-chain constraints cannot be exceeded even if the agent is compromised. Standard mode links owner and agent on-chain; private mode uses zero-knowledge proofs to hide that link, with funding via Privacy Cash. The page also advertises x402 support, a multi-agent dashboard, and devnet program IDs. It is a useful design reference for agent spending controls, but the page contains no audit, benchmark, threat-model proof, or deployment evidence, so its security and privacy properties are vendor claims.

## Problem and threat model

The vendor's stated problem is autonomous spending with dangerous wallet access. Examples: a jailbroken agent drains the wallet; a bug causes an infinite loop; prompt injection lets an attacker control funds. The core claim is that "agent-side limits don't work" because the agent has the keys and can bypass its own rules. The proposed trust model is owner human wallet with full control, delegate AI agent key that can spend within limits, and constraints enforced by a Solana program, not by the agent.

The implicit threat is a compromised or misaligned agent plus runaway behavior; the control is a hard cap on what the delegate key can move. The page does not analyze a compromised owner, malicious merchant, colluding relayer, compromised validator, key extraction from the MCP host, or upgrade authority over the program. It does not say whether the program is upgradeable, who controls upgrades, or whether the relayer can censor or correlate payments. These gaps matter because "trustless" is a strong claim not supported by the page.

Privacy has a separate model. Standard mode links the owner wallet to the agent on-chain. Private mode claims ZK proofs hide the wallet-agent link, the owner proves ownership without revealing identity, and funding is anonymous via Privacy Cash. The page does not claim private mode hides the payment amount, recipient, timing, or graph. It gives no anonymity set, Privacy Cash trust assumptions, or relayer metadata. The privacy claim is narrow and vendor-asserted.

## Architecture / protocol / taxonomy

The product has an on-chain Solana program and an off-chain application stack.

**On-chain spending account.** The owner creates an agent with a delegate key and constraints. The example configuration is:

- `max_per_tx: 0.1 SOL`
- `daily_limit: 1 SOL`
- `total_limit: 10 SOL`
- `expires_at: 2026-02-15`
- `token_limits: USDC: 5/tx, 50/day, 500 total`

The intended invariant is: a single transfer must not exceed the per-transaction cap; the sum in the current day must not exceed the daily cap; the lifetime sum must not exceed the total cap; no transfer after expiry; each token has its own caps. The page says the Solana program enforces these, so a jailbroken agent "CANNOT exceed these limits." Whether a "day" is rolling 24 hours or UTC calendar day is not stated. The example expiry is already past relative to this reading, suggesting illustrative or stale documentation.

**Standard mode.** Owner wallet linked to agent on-chain; simpler and lower-fee. Linkability is explicit.

**Private mode (ZK).** The page describes hiding the wallet-agent link. The privacy stack is Noir (Aztec) circuits, Barretenberg UltraHonk via WASM for client proving, Sunspot Groth16 on Solana for on-chain verification, Poseidon as the ZK-friendly hash, and Privacy Cash for anonymous funding. Private-agent creation is: wallet signs a message, a master secret is derived, a commitment is generated; on-chain storage holds `owner_commitment` (a hash), not the wallet address; management requires proving knowledge of the preimage via a ZK proof. The page gives no exact commitment formula, circuit statement, proving-key setup, or trusted-setup assumptions. It does not explain the UltraHonk-to-Groth16 relationship; the combination is the vendor's description.

**Off-chain stack.** Solana settlement, Helius RPC, Anchor contract, Noir/Barretenberg/Sunspot proofs, Next.js 16/React 19/TypeScript frontend, Express.js relayer, MCP, USDC, and native x402. The relayer is a fee payer and ZK helper; authentication, logging, and availability are not described.

**Program IDs (devnet).** Cloaked Program: `3yMjzAeXXc5FZRUrJ1YqP4YMPhPd5bBxHQ6npNSPCUwB`. ZK Verifier: `G1fDdFA16d199sf6b8zFhRK1NPZiuhuQCwWWVmGBUG3F`. No mainnet program ID or deployment date is given.

## Section-by-section findings

- **Problem.** The failure is key custody: if the agent holds keys, agent-side rules are advisory. This is reasonable for runaway-agent risk but ignores policy errors, malicious merchants, and key compromise within the caps.
- **Solution.** On-chain constraints are the product. The diagram labels the owner as "Human wallet (full control)" and delegate as "AI agent key (can spend within limits)." The claim is that even a jailbroken agent cannot exceed the limits.
- **Privacy architecture.** Standard links owner and agent; private uses ZK to hide the link and Privacy Cash to fund. The page says "owner proves ownership without revealing identity," but the hidden identity is the wallet-agent link, not necessarily the owner's real-world identity.
- **Technology stack.** Concrete component names make the design inspectable at a high level, but this is not a specification. No circuit code, program source, or audit is linked.
- **Program IDs.** Devnet only, a signal that the page does not present a mainnet-ready financial system.
- **Key features.** On-chain per-transaction/daily/lifetime/expiry limits and instant freeze; standard/private ZK modes; x402 pay-per-use support; multi-agent dashboard with live spending and one-click freeze; USDC per-token constraints.
- **Quick start.** `npx cloaked-mcp` with `CLOAKED_AGENT_KEY`; pay in SOL or USDC via `cloak_pay` with `token: "USDC"`.
- **SDK.** `npm install @cloakedagent/sdk`; `new CloakedAgent(agentKey, rpcUrl)`; `agent.spend({destination, amount})`. Examples: `100_000_000` lamports = 0.1 SOL; `5_000_000` = 5 USDC at six decimals.
- **Maintenance.** Glama shows "Activity: Inactive" and "Responsiveness: No issues"; "This server cannot be deployed" appears in the Glama deployment panel. This is a directory limitation, not necessarily a product limitation, but it matters for a demo.
- **Market context.** Related servers include BotWallet, ows-mcp-wallet, capline-mcp, AgenticBTC, Veyra, Regent, and AgentPays. The category is crowded; differentiation needs stronger evidence than this page.

## Key mechanisms and equations

The page has no formal equations or proofs. The enforceable policy can be restated as these inequalities [I], not as a quoted specification:

- `amount_tx <= max_per_tx`
- `sum(amount_tx in day) <= daily_limit`
- `sum(amount_tx) <= total_limit`
- `block_time <= expires_at`
- the same checks per `token_limits[token]`

Concrete identifiers and parameters:

- `max_per_tx = 0.1 SOL`; `daily_limit = 1 SOL`; `total_limit = 10 SOL`; `expires_at = 2026-02-15`.
- USDC: `5` per transaction, `50` per day, `500` total.
- Cloaked Program (devnet): `3yMjzAeXXc5FZRUrJ1YqP4YMPhPd5bBxHQ6npNSPCUwB`.
- ZK Verifier (devnet): `G1fDdFA16d199sf6b8zFhRK1NPZiuhuQCwWWVmGBUG3F`.
- MCP binary `cloaked-mcp`; env var `CLOAKED_AGENT_KEY`; SDK `@cloakedagent/sdk`.
- Token decimals in examples: SOL 9 (`100_000_000` = 0.1); USDC 6 (`5_000_000` = 5).
- ZK stack names: Noir, Barretenberg UltraHonk (WASM), Sunspot Groth16 (Solana), Poseidon, Privacy Cash.

The strongest technical claim is negative: "Even if jailbroken, agent CANNOT exceed these limits." There is no adversarial evaluation, program audit, or formal invariant proof. The private-mode claim is qualitative: ZK proofs "hide wallet-agent link"; the owner proves ownership without revealing identity. No proof size, proving time, verification cost, or anonymity-set size is given.

## Experiments / evaluation

There are no experiments, datasets, baselines, metrics, benchmarks, audits, or user studies. The only numbers are configuration limits and code examples. No evidence of a red-team test against prompt injection, adversarial over-limit sequence testing, proving/verification latency or cost, privacy analysis of standard vs private mode (including funding, timing, or relayer linkage), mainnet deployment/TVL/transaction count, or an independent audit/bug bounty. The Glama "Activity: Inactive" and "This server cannot be deployed" are operational metadata, not security evaluation, but they argue against treating Cloaked as a maintained production dependency without due diligence.

## Paper-specific findings

1. The core control is an on-chain delegate account with per-transaction, daily, lifetime, expiry, and per-token caps, not an agent-side policy engine. [V]
2. The threat model is a compromised or runaway agent; the control caps spendable value, not the reason for payment. [V]
3. Standard mode deliberately links owner and agent on-chain; private mode is the only mode claiming to hide that link. [V]
4. Private mode uses a commitment to a master secret (`owner_commitment`), not the wallet address, with ZK proof of preimage for management. [V]
5. The ZK stack is Noir/Barretenberg/Sunspot/Poseidon with Privacy Cash; no circuit spec or trusted setup is provided. [V]
6. Per-token USDC limits are advertised alongside SOL with the same on-chain enforcement claim. [V]
7. x402 support is advertised for pay-per-use APIs, but the flow, settlement, and dispute handling are undocumented. [V]
8. Program and verifier IDs are explicitly devnet, not mainnet. [V]
9. MCP integration is thin: `npx cloaked-mcp` plus an agent key, `cloak_pay` for SOL/USDC, and an SDK for programmatic spending. [V]
10. Glama marks the server inactive and not deployable through Glama; the vendor page points to its own deployment path. [V]
11. The privacy claim is narrow: it hides the wallet-agent link, not the payment graph, amount, recipient, or timing. [V][I]
12. "Trustless" is unsupported: upgrade authority, relayer trust, Privacy Cash assumptions, and key custody are not analyzed. [I]

## Limitations and caveats

The page is marketing and integration documentation, not a security argument. "Trustless" and "cannot bypass" are absolute claims with no audit, formal model, or adversarial test. On-chain caps do not prevent a compromised agent from paying an attacker within the cap, overpaying a merchant, or leaking business logic through payment timing and recipients. A stolen delegate key can spend the remaining limits. A stolen owner key can change or freeze the account. The relayer is an additional trust and availability dependency; the page does not say whether it can see or block transactions.

Private mode is high-level only. It is unclear whether the proof hides the owner wallet from the program, relayer, merchant, or all on-chain observers; the page says "hide wallet-agent link" and "proves ownership without revealing identity." Privacy Cash is an external funding dependency with its own anonymity set and trust assumptions; correlation between funding and spending is not analyzed. The proof-system description mixes UltraHonk client proving with Groth16 on-chain verification without explaining the conversion. No proof sizes, proving times, or verification costs are given.

The devnet-only IDs, inactive directory status, past example expiry, and absence of a linked audit or source license are red flags for a hackathon dependency. The page lacks a version, changelog, incident history, or mainnet date. Related servers show a crowded market, so "first" or "only" claims would be unsafe.

## Hackathon hooks

**What this enables.** A concrete guardrail demo: give an agent a capped Solana account, let it pay for x402 APIs or services, show that a prompt-injected agent cannot drain more than the cap, and freeze the account from a dashboard. The standard/private split is a useful teaching device for linkability, and the MCP interface wires easily into agent frameworks.

**What it does NOT enable.** It is not a Zcash or Monad product, not audited custody, and not a general privacy layer. It does not hide payment amounts or recipients in private mode (the page claims only the wallet-agent link), does not prevent authorized-but-bad payments within caps, does not provide refunds or chargebacks, and does not establish operator trust. It is devnet-only here.

**Demo ideas.** (1) "Drain the agent": prompt injection tries to empty the wallet; show the cap rejection and freeze button. (2) "Two-mode linkability": visualize the on-chain owner-agent edge in standard mode, then the commitment-only view in private mode, while noting amounts and recipients stay visible. (3) "Per-token budget": an agent buys a USDC API call and a SOL service under independent caps. (4) "Outer mandate, inner cap": pair a MinMandate-style task-scoped authorization with Cloaked's per-transaction cap; this is a concept, not an integration in either source. (5) "x402 checkout": pay a pay-per-use endpoint and show the dashboard.

## Claims to avoid

Do not call Cloaked audited, trustless, production-ready, mainnet-ready, or unhackable. Do not claim it prevents all prompt injection or all drainage; it caps losses, within the configured asset and limits. Do not claim private mode hides amounts, recipients, timing, or the full graph; the page claims only the wallet-agent link. Do not claim real-world owner anonymity, regulatory compliance, refunds, or chargebacks. Do not say it works on Zcash or Monad. Do not present devnet IDs as mainnet. Do not treat "cannot be deployed" or "Activity: Inactive" as proof of abandonment. Do not claim the ZK construction is sound or Privacy Cash integration anonymous without independent evidence.
