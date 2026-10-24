# Privacy x Agentic E-Commerce Research

Research on private, secure agent payments and agentic commerce. This is the product layer for the Zcash and Monad hackathons: mandates, agent wallets, MCP payments, AP2 security, multi-agent privacy, and LLM leakage.

## Papers and sources

| # | File | Source | Type | Key result |
|---|---|---|---|---|
| 1 | [01-minmandate-2026.md](papers/01-minmandate-2026.md) | *MinMandate: Private Task-Scoped Payment Authorization for Adaptive Agent Workflows* (Gao et al., 2026) | Protocol + evaluation | One task-scoped mandate enables adaptive merchant choice; fresh per-call views remove a stable cross-merchant handle; +32.7 pp task success at 50% merchant unavailability; stable handle adds +27.4 pp attacker recovery |
| 2 | [02-aesp-2026.md](papers/02-aesp-2026.md) | *AESP: A Human-Sovereign Economic Protocol for AI Agents with Privacy-Preserving Settlement* (Wang, 2026) | Protocol + SDK | Eight-check policy engine, review tiers, dual-signed EIP-712 commitments, context-isolated ephemeral addresses; evaluation deferred; privacy is address-level |
| 3 | [03-secure-autonomous-agent-payments-2025.md](papers/03-secure-autonomous-agent-payments-2025.md) | *Secure Autonomous Agent Payments: Verifying Authenticity and Intent in a Trustless Environment* (Acharya, 2025) | Conceptual framework | TIVA combines DID/VC, on-chain intent proofs, ZK selective disclosure, and optional TEE; no implementation or benchmark |
| 4 | [04-cloaked-mcp-2026.md](papers/04-cloaked-mcp-2026.md) | Cloaked MCP Server (CloakedAgent, 2026) | Vendor product page | On-chain per-transaction/daily/lifetime caps for Solana agent spending; private mode hides wallet-agent link; devnet-only, no audit |
| 5 | [05-sok-security-autonomous-llm-agents-2026.md](papers/05-sok-security-autonomous-llm-agents-2026.md) | *SoK: Security of Autonomous LLM Agents in Agentic Commerce* (Mao et al., 2026) | SoK | Five threat dimensions, 12 cross-layer vectors, seven protocols; no protocol covers all dimensions; cross-layer compromise is the core risk |
| 6 | [06-sok-blockchain-agent-to-agent-payments-2026.md](papers/06-sok-blockchain-agent-to-agent-payments-2026.md) | *SoK: Blockchain Agent-to-Agent Payments* (Zhang et al., 2026) | SoK | Four-stage lifecycle; weak intent binding, misuse under valid authorization, payment-service decoupling, limited accountability |
| 7 | [07-ap2-security-analysis-2026.md](papers/07-ap2-security-analysis-2026.md) | *Beyond the Mandate: A Systematic Security Analysis of the Agent Payments Protocol (AP2)* (Aviv et al., 2026) | Threat model + PoC + scanner | 48 threats, 8 High-risk; signed mandates do not cover pre-signing context; context poisoning yields valid-but-wrong payments |
| 8 | [08-can-llms-keep-a-secret-2023.md](papers/08-can-llms-keep-a-secret-2023.md) | *Can LLMs Keep a Secret?* / ConfAIde (Mireshghallah et al., 2023/2024) | Benchmark | Contextual-integrity tiers; GPT-4/ChatGPT leak in 39%/57% of Tier 4 summaries; CoT does not fix it |
| 9 | [09-privacy-in-action-2025.md](papers/09-privacy-in-action-2025.md) | *Privacy in Action* / PrivacyChecker (Wang et al., 2025) | Mitigation + benchmark | Per-flow reasoning reduces leakage 36.08% -> 7.30% (DeepSeek-R1) and 33.06% -> 8.32% (GPT-4o); judge-action gap |
| 10 | [10-magpie-2025.md](papers/10-magpie-2025.md) | *MAGPIE: A dataset for Multi-AGent contextual PrIvacy Evaluation* (Juneja et al., 2025) | Benchmark | 50-60% active-collaboration leakage; 29.7% task success; privacy-utility tradeoff is steep |

## What this folder establishes

1. **Authorization is not intent.** A signed mandate can be valid and still authorize a transaction the user did not intend, because the pre-signing context is outside the signature (AP2).
2. **Privacy is not a stable handle.** Reusing one holder/agent identifier across merchants destroys the privacy benefit even when each payment is encrypted (MinMandate).
3. **Caps are necessary but insufficient.** On-chain spending limits stop runaway loss, not a wrong-but-in-cap payment or a slow drain (Cloaked, AESP, AP2).
4. **Agent privacy is a cross-layer problem.** LLM context leakage, tool compromise, memory poisoning, and payment settlement are one attack surface (SoK).
5. **Flow-level gating beats privacy prompts.** ConfAIde shows models know the data is private and still leak; PrivacyChecker shows per-flow reasoning reduces leakage substantially.
6. **Multi-agent collaboration is harder than single-agent redaction.** MAGPIE shows 50-60% leakage when private information is necessary for the task.
7. **Payment-service decoupling is unsolved.** Finality of payment is not proof of delivery; receipts and accounting are the missing layer (A2A SoK).

## Flagship ideas from this folder

- [VeilMandate](01-veilmandate.md) — task-scoped private agent payments on Zcash.
- [GhostCart](02-ghostcart.md) — sealed-bid procurement with shielded settlement.
- [IntentGuard](03-intentguard.md) — AP2/MCP context-binding firewall.
- [Sovereign Runtime](04-sovereign-runtime.md) — deterministic agent wallet.
- [ContextCurtain](05-contextcurtain.md) — multi-agent contextual-integrity firewall.
- [ShadeScore](../privacy/06-shadescore.md) — private credit/reputation.
- [Veil402](10-veil402.md) — private pay-per-use APIs.

## Access note

The MinMandate full PDF was retrieved from the public GitHub repository and read; the ePrint PDF itself was Cloudflare-blocked. The Cloaked page is vendor marketing, not an audit. The other eight sources were read in full from local PDFs.
