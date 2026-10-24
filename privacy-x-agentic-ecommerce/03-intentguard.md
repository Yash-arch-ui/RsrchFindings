# IntentGuard — A Cross-Layer Firewall for Agent Payments

## Metadata

- **Target hackathons:** Monad (primary), Zcash (secondary), ETHGlobal
- **Tracks:** agentic commerce, security, privacy, payments, infrastructure
- **Papers used:** AP2 security analysis (2608.23858), SoK Security of Autonomous LLM Agents (2604.15367), AESP (2603.00318), Secure Autonomous Agent Payments (2511.15712), MinMandate (2026/1674), Cloaked MCP (2026)
- **One-liner:** A signed mandate is not intent. IntentGuard binds the context that produced the mandate and blocks the attack before the payment is signed.

## The problem

Agent payments have a structural blind spot: the signature covers the **mandate**, not the **context** that produced it. AP2's 2026 security analysis found 48 threats across five attack families and eight High-risk threats. A correctly signed $80 mandate can be produced for a $50 user request by poisoning the catalog, drifting a sub-agent, or swapping an MCP tool result. The signature is valid. The payment is wrong.

Existing defenses are partial:

- Prompt-level defenses ("do not leak secrets") fail under context pressure.
- Spending caps stop runaway loss but not a wrong-but-in-cap payment.
- On-chain policy checks see the transaction, not the reasoning or the tool output that shaped it.
- A human-in-the-loop review is defeated by approval fatigue and a rendered UI that can hide the real fields.

The result is a cross-layer failure: an LLM-layer compromise becomes a signed payment, a custody loss, or a regulatory event.

## The solution

IntentGuard is a **pre-signing firewall** that sits between the agent, the merchant, and the wallet.

1. **Context capture.** Every step that shapes a payment — discovery result, product page, cart state, A2A message, MCP tool output, quote, and rendered mandate — is hashed into a **context commitment**. The commitment is bound into the signed mandate.
2. **Deterministic policy outside the model.** A signed policy engine evaluates amount, merchant class, recipient, item, quantity, cumulative spend, time window, and risk flags. The LLM cannot override it. The policy is the AESP eight-check gate, extended with AP2's mandate-chain checks.
3. **Tool and endpoint pinning.** MCP servers, tool names, and A2A endpoints are pinned to signed identities. A lookalike tool or a typosquatted merchant fails the pin check.
4. **Cross-layer anomaly detector.** A model watches the trajectory for prompt injection, memory poisoning, sudden recipient changes, amount drift, and collusion patterns. It does not sign; it raises the review tier.
5. **Review escalation.** Low-risk payments auto-approve. Medium-risk require explicit confirmation with a faithful rendered summary. High-risk require biometric/passkey approval. The review UI shows the **context diff**, not just the final amount.
6. **Cumulative spend and herding monitor.** A sequence of individually valid payments can still be an attack. IntentGuard tracks per-agent, per-task, and per-merchant cumulative spend and flags fragmentation across agents.
7. **Private settlement path.** For Zcash, the approved payment is a shielded note; the context commitment and policy proof are retained locally or as a minimal receipt. For Monad, the policy check and receipt registry live on-chain.
8. **Signed risk data.** Merchant risk, tool risk, and issuer risk are signed attestations, not unsigned hints. A malicious merchant cannot downgrade its own risk score.

## Why this is out of this world

Most agent-security demos are a chatbot that says "I won't do that." IntentGuard is a **runtime enforcement layer** with a signed evidence chain: it can show exactly which context produced the payment, which policy passed, which tool was pinned, and which review tier was used. It turns "the model behaved" into "the system can prove what the model saw."

The killer demo: a legitimate mandate that pays the wrong merchant, blocked by a context hash mismatch. The audience sees that a valid signature is not enough.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| AP2 security analysis | 48 threats, 8 High-risk, pre-signing context gap, context commitment, deployment-aware scanner |
| SoK autonomous LLM agents | Five dimensions, 12 cross-layer vectors, five-layer defense, memory injection, systemic herding |
| AESP | Eight-check deterministic policy, review tiers, dual-signed commitments, human sovereignty invariant |
| Secure Agent Payments (TIVA) | DID/VC identity, on-chain intent proof, ZK selective disclosure, optional TEE attestation |
| MinMandate | Task-scoped authority and fresh per-call views; stable-handle privacy lesson |
| Cloaked MCP | On-chain spending caps and the limits of caps-only security |

## Intuitive workflow

1. **The user asks for a $50 gift.** The agent searches, compares, and builds a cart.
2. **IntentGuard records the journey.** Discovery page, product, price, shipping, tax, merchant identity, and tool outputs are hashed into one context fingerprint.
3. **The agent proposes checkout.** The wallet shows a human-readable summary and the context fingerprint.
4. **A poisoned tool swaps the merchant.** The context fingerprint changes. IntentGuard blocks the signature before the user sees a misleading "same" cart.
5. **A valid but over-broad open mandate is replayed.** The mandate-chain and cumulative-spend checks reject it.
6. **A medium-risk payment is escalated.** The review screen shows a diff: "merchant changed from A to B; shipping increased 40%."
7. **The payment settles.** On Monad, the policy receipt is on-chain. On Zcash, the payment is shielded and the receipt stays private.
8. **An auditor later asks why.** IntentGuard produces the signed context and policy trace, without exposing unrelated purchases.

## Demo (5 minutes)

**Act 1 — The valid-but-wrong payment.** Run an AP2-style checkout. A malicious catalog changes the payee after the user sees the quote. The mandate signature is valid; the payment goes to the attacker. The judge sees the failure.

**Act 2 — The context commitment.** Turn on IntentGuard. The same attack changes the context hash and is blocked before signing.

**Act 3 — The tool collision.** A lookalike MCP tool returns a fake shipping quote. The pin check rejects it; the review tier escalates.

**Act 4 — The cumulative attack.** Ten small payments each pass the per-transaction cap but exceed the task budget. The cumulative monitor freezes the agent.

**Act 5 — Private settlement.** Approve a payment and settle in shielded ZEC. The merchant verifies; the public chain does not see the workflow.

**Close:** "The signature was valid. The intent was not. IntentGuard checks both."

## Hackathon fit

- **Monad:** cheap per-transaction policy checks, on-chain receipt registry, high-throughput agent traffic.
- **Zcash:** shielded settlement and private receipts.
- **ETHGlobal:** security + agentic commerce; strong "attack demo" format.
- **Judging:** concrete exploit, concrete fix, measurable false-positive/latency tradeoff.

## Cons and risks

- **Context capture is hard.** Tool outputs, rendered UI, and model memory are messy; a hash only proves what was captured, not what the model "understood."
- **Policy expressiveness.** A deterministic policy cannot encode every semantic intent. It can still allow a wrong-but-in-policy payment.
- **Latency.** Hashing, policy checks, and review escalation add overhead to every payment.
- **False positives.** Over-blocking frustrates users and encourages them to disable the firewall.
- **Review fatigue.** A human who approves everything defeats the review tier.
- **Tool pinning is brittle.** MCP server updates and key rotation need a transparent registry.
- **Privacy vs audit.** The context trace is sensitive; storing it privately weakens auditability, storing it publicly leaks intent.
- **No formal guarantee.** The anomaly detector is probabilistic; only the deterministic policy is enforceable.

## Judge Q&A

**"Isn't this just AP2 plus a policy engine?"** AP2 signs the mandate; IntentGuard binds the context that produced it and enforces policy outside the model. The AP2 paper itself identifies this gap.

**"Can't the agent just lie about the context?"** The context commitment is constructed by the wallet/tool layer, not the LLM. The agent can choose tools, but it cannot rewrite the signed capture without failing the hash.

**"What about a compromised wallet?"** A compromised wallet can sign anything. IntentGuard protects against a compromised **agent**, not a compromised signer. The trust boundary must be stated.

**"Why Monad?"** Per-payment policy checks and receipts are cheap enough to run on every transaction; Monad's throughput makes that practical.

**"Why Zcash?"** The payment and receipt can be shielded, so the security layer does not become a surveillance layer.

## Claims to avoid

- "Prevents all prompt injection."
- "Guarantees intent alignment."
- "Trustless."
- "No false positives."
- "Production-ready."

## One-sentence pitch

**IntentGuard binds the context, tools, and policy behind an agent payment into a signed evidence chain, so a valid mandate cannot be used to authorize an unintended purchase.**
