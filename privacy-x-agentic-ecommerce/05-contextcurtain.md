# ContextCurtain — A Privacy Firewall for Multi-Agent Commerce

## Metadata

- **Target hackathons:** Monad (primary), Zcash (private audit), ETHGlobal
- **Tracks:** agentic commerce, privacy, LLM security, multi-agent systems
- **Papers used:** MAGPIE (2506.20737), Can LLMs Keep a Secret? / ConfAIde (2310.17884), Privacy in Action / PrivacyChecker (2509.17488), SoK Security of Autonomous LLM Agents (2604.15367), SoK Blockchain A2A Payments (2604.03733)
- **One-liner:** A contextual-integrity firewall that lets buyer, merchant, and logistics agents negotiate without leaking the secrets that make the deal work.

## The problem

Multi-agent commerce is a privacy paradox. Agents must share enough information to close a deal, but the information that makes the deal possible is often the information that weakens their position. A buyer agent has a maximum budget; a seller agent has a walk-away price; a logistics agent knows a deadline is flexible. Revealing any of these can destroy value.

The empirical evidence is brutal:

- MAGPIE finds 50-60% leakage in active multi-agent collaboration and only 29.7% task success.
- ConfAIde finds GPT-4/ChatGPT leak in 39%/57% of Tier 4 summaries even when told to preserve privacy.
- PrivacyChecker finds a judge-action gap: GPT-4o classifies sensitive data correctly 97.97% of the time but leaks in 33.06% of actions.
- The SoK shows prompt injection, memory poisoning, and tool compromise turn an LLM-layer failure into a payment or custody event.

Existing defenses are model-level prompts or static redaction. They do not understand **who is allowed to know what, from whom, for what purpose** in a live negotiation. They also do not bind privacy to the payment or service outcome.

## The solution

ContextCurtain is a **contextual-integrity runtime** for multi-agent commerce.

1. **Flow model.** Every message, tool call, and payment request is decomposed into an information flow: sender, recipient, subject, information type, purpose, and transmission principle. This is the ConfAIde/MAGPIE/PrivacyChecker model, made operational.
2. **Policy per agent and per task.** The buyer's agent has a policy: "do not reveal max budget to seller; may reveal delivery deadline to logistics; may reveal quality constraints to seller." The seller has its own policy. Policies are signed and versioned.
3. **Flow gate.** Before a message or tool call leaves an agent, the gate classifies the flow, checks the policy, and chooses **allow**, **abstract**, **redact**, or **block**. It can replace an exact number with a range, a name with a role, or a secret with a proof.
4. **Proof instead of disclosure.** When a counterparty needs a fact but not the value, the agent sends a ZK/BBS+ proof: "budget is at least 5 MON," "delivery is within 30 days," "merchant is licensed." The verifier learns the predicate, not the secret.
5. **Live trajectory evaluation.** The runtime logs the full multi-turn trajectory, not a single prompt. It measures leakage against the task's private facts and the deal outcome. This closes the static-benchmark gap.
6. **Payment binding.** The final payment commits to the privacy policy and the negotiated terms. A merchant cannot claim a different price after the agent disclosed a budget; the receipt binds the quote, the flow, and the payment.
7. **Private audit.** On Zcash, the audit trail is encrypted/shielded; an auditor can verify compliance with a viewing key or a selective-disclosure proof. On Monad, a hash of the policy and flow decisions is recorded for accountability without publishing the transcript.
8. **Attack surface.** The runtime treats tool outputs and peer messages as untrusted. It pins tool identities, detects prompt injection, and flags memory poisoning. The SoK's five-layer defense is the design checklist.

## Why this is out of this world

Most privacy tools are a filter on the model's output. ContextCurtain is a **negotiation-aware information-flow controller**: it knows that the same fact can be public in one direction and secret in another, and it can replace disclosure with proof. It also measures the **privacy-utility frontier** live: the demo shows that clamping leakage collapses the deal, and that proof-based disclosure recovers it.

The killer demo: the buyer agent never reveals its max budget, yet the seller agent accepts a price within it because the buyer proves "budget >= 6 MON" without revealing "budget = 8 MON." The deal closes, and the seller cannot extract the extra 2 MON.

## Once-a-decade primitive

ContextCurtain introduces **purpose-bound disclosure leases**. A permitted fact flow is issued as a lease bound to a recipient, purpose, expiry, and settlement context. The commerce protocol accepts the fact only while that lease is valid; reusing a fact for another purpose, recipient, or payment invalidates the proof. This does not pretend that copied plaintext can be erased; it makes authorized use machine-checkable.

**Impossible demo:** the seller probes for the buyer's maximum through five paraphrases while a compromised logistics tool tries to exfiltrate the same fact. The runtime blocks semantic leakage, but accepts a lower-bound proof and closes the deal below the hidden maximum. The privacy control improves the negotiation outcome instead of merely redacting text.

The moat is a trajectory-and-purpose benchmark that measures inferred leakage, not only literal secret strings, and becomes the evaluation standard for private agent negotiation.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| MAGPIE | 158 multi-agent scenarios; 50-60% active-collaboration leakage; privacy-utility tradeoff |
| ConfAIde | Contextual integrity tiers; 39%/57% Tier 4 summary leakage; CoT does not fix it |
| PrivacyChecker | Per-flow reasoning; judge-action gap; 33.06% -> 7-8% leakage with flow gating |
| SoK LLM agents | Cross-layer threats, memory injection, five-layer defense, systemic herding |
| A2A payments SoK | Four-stage lifecycle; payment-service decoupling; accountability |

## Intuitive workflow

1. **Three agents enter a negotiation.** Buyer, seller, logistics. Each has a private fact: budget, walk-away price, deadline flexibility.
2. **The buyer asks for a quote.** The seller's agent proposes a price. The buyer's gate checks the outbound message: no budget leak.
3. **The seller probes.** "What is your maximum?" The gate blocks the exact value and offers a proof: "budget is at least 6 MON."
4. **The logistics agent needs the deadline.** The buyer's policy allows the deadline to logistics but not to the seller. The gate routes the fact with a purpose tag.
5. **A malicious tool returns a poisoned message.** The gate sees an unexpected recipient and blocks the flow; the trajectory is flagged.
6. **The deal closes.** Payment binds the negotiated terms and the privacy policy. The seller cannot claim a different price.
7. **An auditor checks.** The buyer reveals only the flow decisions and the predicate proofs, not the transcript. The audit passes.

## Demo (5 minutes)

**Act 1 — The leak.** Run three agents with no firewall. The buyer reveals its max budget; the seller captures the surplus. Show the leakage score and the deal price.

**Act 2 — The gate.** Turn on ContextCurtain. The exact budget is blocked; the proof "budget >= 6 MON" is accepted. The deal closes at 6.5 instead of 8.

**Act 3 — The poisoned tool.** A tool output asks the buyer to send its private deadline to the seller. The gate blocks the cross-purpose flow.

**Act 4 — The live benchmark.** Show the privacy-utility curve: no gate (high leakage, high success), naive redaction (low leakage, low success), ContextCurtain (low leakage, high success).

**Act 5 — Private audit.** An auditor verifies the policy compliance with a selective-disclosure proof, without seeing the transcript.

**Close:** "The deal needs the truth. The counterparty does not."

## Hackathon fit

- **Monad:** high-throughput flow decisions, on-chain policy/receipt registry, cheap agent messages.
- **Zcash:** private audit trail and shielded payment for the final deal.
- **ETHGlobal:** multi-agent demo is visually compelling; privacy-utility curve is a strong result.
- **Judging:** empirical grounding from three benchmarks, a live negotiation, and a measurable frontier.

## Cons and risks

- **Flow extraction is hard.** PrivacyChecker's residual failures are mostly judgment errors and judge-action gaps, not missed flows. A production system needs a reliable classifier.
- **Policy authoring.** Users and agents must write policies; bad policies leak or over-block.
- **Proof expressiveness.** Not every fact can be turned into a predicate proof; some disclosures are inherently value-revealing.
- **Latency and cost.** Per-flow classification and proof generation add overhead.
- **Adaptive adversaries.** The benchmarks are non-adversarial; a strategic seller can probe across turns.
- **Leases cannot erase plaintext.** A purpose-bound lease can make protocol use rejectable and auditable, but it cannot recall a fact once a malicious recipient has copied it outside the protocol.
- **Audit privacy.** A private audit trail can hide misconduct; a viewing-key design needs governance.
- **No cryptographic guarantee.** The gate is a policy engine, not a formal non-interference proof.

## Judge Q&A

**"Isn't this just a privacy prompt?"** No. It decomposes flows, enforces a signed policy outside the model, and can replace disclosure with a proof. The benchmarks show prompts alone fail.

**"How do you measure leakage?"** Exact-value leakage plus inferred/paraphrased leakage; the demo uses a curated set of private facts and a live trajectory.

**"What if the model ignores the gate?"** The gate sits on the tool/message path, not inside the model. The model cannot send a message that the gate blocks.

**"Why Monad?"** Many small flow decisions and receipts; cheap execution makes per-message enforcement practical.

**"Why Zcash?"** The audit trail and final payment can be private, so the privacy layer does not become a surveillance layer.

## Claims to avoid

- "Prevents all leakage."
- "Formally private."
- "No utility loss."
- "Works for any agent."
- "Production-ready."

## One-sentence pitch

**ContextCurtain enforces contextual integrity across multi-agent commerce, replacing secret disclosures with predicate proofs so agents can close deals without revealing the facts that give the other side leverage.**
