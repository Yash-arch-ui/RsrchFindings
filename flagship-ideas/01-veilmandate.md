# VeilMandate — Private, Adaptive Agent Payments on Zcash

## Metadata

- **Target hackathons:** Zcash (primary), Monad (secondary), ETHGlobal (fallback)
- **Tracks:** privacy, agentic commerce, payments, ZK, shielded transactions
- **Papers used:** MinMandate (2026/1674), AP2 security analysis (2608.23858), BBS+ anonymous credentials (2025/824), improved issuer hiding (2026/555), Zcash linkability (1712.01210), Orchard audit (2026)
- **One-liner:** One human approval, many merchant choices, zero cross-merchant linkage.

## The problem

An AI agent can now plan and pay for a multi-step workflow: buy a dataset, hire an enrichment API, order shipping, pay a translator, renew a subscription. The user cannot name every merchant in advance because the agent discovers the right merchant from intermediate results. AP2-style mandates solve authorization by making the user pre-approve merchants or service classes, but that either limits the agent's adaptability or forces the user back into the loop.

The privacy failure is worse than "the merchant sees the payment." Agent-payment protocols often expose a **stable holder identifier** across calls. One reusable handle lets an observer join separate purchases, infer the user's broader intent, and reconstruct the workflow even when each individual payment looks private. Zcash shielding hides the sender, receiver, and amount on-chain, but shield/unshield boundaries and timing can still leak. In 2017, exact-amount round trips matched 31.5% of coins entering the Sprout pool; the boundary problem is not solved by "use a shielded address."

The result: an agent can be forced to choose between utility (adaptive merchant selection) and privacy (no linkable handle). VeilMandate refuses that tradeoff.

## The solution

VeilMandate is a **task-scoped payment mandate** with **fresh per-call views** and **shielded settlement**.

1. The user signs one mandate for a task: service classes, merchant predicates, total budget, per-call cap, expiry, and a single-use spend-slot structure. The mandate does not name every merchant.
2. The agent chooses a merchant at runtime and derives a fresh **service view** and **spend view** for that call. The merchant sees only the authority and amount it needs. The redemption wallet sees the spend proof. Neither sees a stable cross-merchant identifier.
3. A **BBS+ anonymous credential** proves the agent is authorized for this task and merchant class without revealing the user, the issuer, or the other calls. Issuer hiding prevents the verifier from learning which bank, employer, or platform issued the credential.
4. A **one-time serial** prevents double spending. A **funding tag** binds the serial to a funded spend slot. The tag is derived per call, not reused across merchants.
5. A **context commitment** binds the discovery result, cart, tool outputs, and rendered mandate fields into the signed object. This closes the AP2 pre-signing gap where a valid mandate can authorize an unintended merchant after catalog or tool manipulation.
6. Settlement uses **Zcash shielded notes** (Orchard). The merchant receives a shielded payment; the agent's funding and change notes are split/batched to reduce boundary correlation. The chain sees a shielded transfer, not the workflow graph.
7. A **status credential** provides revocation without revealing the credential holder on every call. Revocation is a renewal decision, not a public denylist lookup that links all of a user's presentations.
8. For Monad, the mandate registry, merchant admission, and policy checks live on a high-throughput EVM contract; Zcash is the settlement rail. For a Zcash-only demo, the registry can be a local attestation service and the payment is native ZEC.

## Why this is out of this world

Most "private agent payment" demos hide one transaction. VeilMandate hides the **shape of the workflow**: which merchants were considered, which were rejected, which calls belong to the same task, and who the human is. It still lets the agent adapt, and it still lets a merchant verify authorization and payment. It turns privacy from a per-transaction property into a **task-level property** that survives merchant substitution.

The closest existing products each solve a slice:

- **MinMandate** solves adaptive merchant selection and per-call unlinkability, but not shielded settlement or Zcash boundary leakage.
- **AP2** solves signed mandates, but not pre-signing context binding.
- **Cloaked** enforces spending caps on Solana, but not task scope, issuer hiding, or cross-call unlinkability.
- **BBS+ credentials** solve selective disclosure and revocation, but not payment settlement or agent workflow.
- **Zcash** solves on-chain shielding, but not mandate semantics or merchant discovery.

VeilMandate is the composition: task-scoped authority + context binding + issuer-hiding credentials + shielded settlement + revocation.

## Sources and what each contributes

| Source | What it gives VeilMandate |
|---|---|
| MinMandate | Task-scoped service/spend split; fresh per-call views; stable-handle ablation (+27.4 pp attacker recovery); one-time serials and funding tags |
| AP2 security analysis | 48 threats, 8 High-risk; pre-signing context is outside the signed mandate; context commitment fix |
| BBS+ AC spec | Selective disclosure, unlinkability, non-duplication, hardware binding, status credential, privacy-preserving revocation |
| Issuer hiding | Malicious-issuer model; signed policy; accountable issuer hiding; avoids revealing which issuer |
| Zcash linkability | Boundary-amount and timing leakage; round-trip heuristic; why shielded alone is not enough |
| Orchard audit | Current Zcash circuit security; PCZT signer-flow and proof-size footguns to avoid in wallet integration |

## Intuitive workflow

1. **The user hires an agent.** "Plan a three-day offsite for 12 people, budget 8 ZEC, buy food, transport, and a venue; use any reputable merchant."
2. **The user signs one mandate.** The wallet shows the task, total budget, per-call cap, allowed service classes, expiry, and a privacy notice. The user does not pick merchants.
3. **The agent shops.** It discovers three caterers, two bus companies, and four venues. It compares quotes and picks a combination.
4. **Each call gets a fresh face.** For the caterer, the agent presents a service view that says "authorized for food service, up to 2 ZEC, task not expired" and a spend view that proves the budget. The caterer cannot link this call to the transport call or to the user's previous purchases.
5. **A malicious catalog tries to swap the payee.** The context commitment no longer matches the signed cart and tool outputs. The wallet refuses to sign. The agent cannot be tricked into paying a different merchant.
6. **The merchant is paid privately.** The payment is a shielded ZEC note. The merchant sees the amount and the service proof; the chain does not see the workflow.
7. **The task ends.** Unspent notes return to the user. The agent cannot spend after expiry. Revocation is instant if the agent is compromised.
8. **The user sees a receipt.** A local, private receipt shows the calls and total spend. It is not published on-chain.

## Demo (5 minutes)

**Act 1 — The leak.** Run an AP2-style checkout. The same holder handle appears in the caterer and transport calls. A graph view joins them into one workflow and infers the offsite budget.

**Act 2 — The mandate.** Sign one task-scoped mandate. The agent picks a substitute caterer after the first is unavailable. The task still completes without a new user signature.

**Act 3 — The attack.** A malicious catalog changes the payee after the user sees the quote. The context commitment check fails; the payment is blocked. Then disable the check and show the valid-but-wrong mandate.

**Act 4 — The shielded settlement.** Pay the merchant in ZEC. The explorer shows a shielded transfer. The merchant verifies authorization. The cross-call graph is gone.

**Act 5 — The boundary test.** Run the Zcash linkability lens on a naive shield/unshield flow. Show a round-trip match. Turn on batching and note splitting; the match rate drops.

**Close:** "The agent can choose any merchant. The world cannot choose the agent's story."

## Hackathon fit

- **Zcash:** native shielded settlement, Orchard, viewing keys, wallet integration, linkability analysis, PCZT safety.
- **Monad:** high-throughput mandate registry, merchant admission, policy verification, agent identity; cheap per-call checks.
- **ETHGlobal:** strong privacy + agentic commerce narrative; can demo on Monad testnet with a Zcash testnet settlement adapter.
- **Judging:** novel composition, clear attack demo, measurable privacy result (linkability match rate), working end-to-end flow.

## Cons and risks

- **Cross-chain complexity.** Zcash + Monad requires a bridge or a testnet adapter; a production bridge is a large trust surface.
- **Boundary leakage remains.** Shielding does not hide timing, amounts at the boundary, or network metadata. The demo must show the residual leak, not claim perfect privacy.
- **Issuer trust.** BBS+ issuer hiding assumes an issuer and a policy authority. A malicious issuer can still attest false attributes; accountable issuer hiding only catches some cases.
- **Revocation privacy.** Status credentials introduce linkability between the credential and its status check. The design must state when this link is visible.
- **Merchant adoption.** Merchants must verify BBS+ presentations and shielded payments; that is a new integration.
- **Agent compromise.** A compromised agent can spend within the mandate. The mandate limits loss, not intent.
- **No audit.** The BBS+ and Zcash integration would be a hackathon prototype, not production cryptography.

## Judge Q&A

**"Why not just use Zcash for everything?"** Zcash hides the payment, not the mandate semantics or merchant discovery. VeilMandate adds the task-scoped authorization and context binding that an agent needs.

**"Why not just use AP2?"** AP2 signs the mandate but not the context that produced it; a valid mandate can be over-broad after catalog/tool manipulation. And its holder handle links calls.

**"Doesn't the merchant need to know the user?"** No. The merchant needs proof that the agent is authorized for this service and that the payment is valid. It does not need the human's identity or the rest of the workflow.

**"How do you prevent double spending?"** A one-time serial per spend slot, bound to a funding tag. The redemption path rejects a reused serial.

**"What leaks?"** Timing, merchant identity, the fact that a task exists, network metadata, and some boundary amounts. The privacy passport states this.

**"Is this production-ready?"** No. It is a research prototype and a hackathon demo. The cryptography exists; the integration and audit do not.

## Claims to avoid

- "Fully anonymous agent payments."
- "Untraceable Zcash."
- "Trustless bridge."
- "No metadata leakage."
- "Production-ready."
- "The agent cannot misbehave." It can spend within the mandate; it just cannot exceed it.

## One-sentence pitch

**VeilMandate gives an AI agent one private, task-scoped mandate to shop across merchants, with unlinkable per-call credentials and Zcash-shielded settlement, so the agent can adapt without exposing the human's intent.**
