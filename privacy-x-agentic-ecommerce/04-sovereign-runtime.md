# Sovereign Runtime — An Agent Wallet That Cannot Betray Its Human

## Metadata

- **Target hackathons:** Monad (primary), ETHGlobal, Zcash (settlement add-on)
- **Tracks:** agentic commerce, wallets, security, privacy, account abstraction
- **Papers used:** AESP (2603.00318), Secure Autonomous Agent Payments / TIVA (2511.15712), Cloaked MCP (2026), BBS+ anonymous credentials (2025/824), delegation of verifiable presentations (2025/2306), MinMandate (2026/1674)
- **One-liner:** A policy-enforcing agent wallet where the model can reason, but only a deterministic, verifiable runtime can move money.

## The problem

An AI agent that can pay is an AI agent that can be drained. Three failure modes dominate:

1. **The agent holds the keys.** A jailbreak, prompt injection, or bug can spend everything. Agent-side spend limits are useless because the agent can bypass its own code.
2. **The policy is too coarse.** A per-transaction cap stops a single drain but not a sequence of small payments, a wrong merchant, or a malicious subscription.
3. **The authority is too broad.** A user delegates "buy supplies" and the agent pays for something unrelated. A signed mandate covers the amount, not the context that made the amount look reasonable.

Existing wallets either trust the model, rely on a human to approve every transaction, or enforce a small set of on-chain caps. None of them gives the user a **sovereign runtime**: a boundary that the agent cannot reason its way out of.

## The solution

Sovereign Runtime is a three-layer agent wallet.

### Layer 1: The human sovereignty kernel

A deterministic policy engine outside the LLM evaluates every proposed action:

- identity and delegation validity,
- task scope and service class,
- recipient and merchant admission,
- amount, per-transaction cap, daily cap, lifetime cap,
- cumulative spend across agents and sessions,
- time window and expiry,
- risk tier and required review,
- replay/nullifier and mandate-chain binding.

The engine returns **allow**, **review**, or **block**. The LLM cannot call the signer directly; it can only submit an action request to the kernel. If the kernel says block, the transaction never reaches the key.

### Layer 2: The delegation graph

A human delegates authority to an agent with a BBS+ credential and a verifiable presentation delegation:

- The credential proves the agent's role, scope, and expiry.
- The delegation binds the delegatee identity, the operation, the verifier, and a nonce.
- The presentation is unlinkable across verifiers; the agent does not reuse a stable identity.
- A status credential handles revocation without publishing the agent's whole history.
- The agent can delegate a narrow sub-task to another agent, but only within the parent's scope; the delegation chain is verified, not trusted.

### Layer 3: The settlement boundary

On Monad, the kernel and the policy receipt live on-chain:

- The wallet is an account-abstraction smart account.
- The policy engine is a pre-signing module; the signer is a passkey/HSM/TEE-backed key.
- Every approved payment emits a compact receipt binding the action, the policy version, the context hash, and the mandate.
- The agent never sees the root key.

On Zcash, the payout can be shielded: the merchant receives a private note, and the receipt commits to the payment without publishing the amount or the agent's identity.

## Why this is out of this world

Most agent wallets are either "trust the model" or "approve every click." Sovereign Runtime makes a stronger claim: **the model can be fully compromised and still cannot exceed the human's policy**, because the model never touches the signer. It also makes the delegation itself private and scoped, so the agent does not need to reveal the human's identity to every merchant.

The demo moment: the agent is "jailbroken" on stage. It tries to drain the wallet. The runtime blocks it, shows the exact policy that fired, and the agent's own logs cannot explain its way out.

## Once-a-decade primitive

Sovereign Runtime becomes a **non-bypassable behavioral automaton for money**. Authorization is a state transition, not a transaction check: the state includes cumulative sequence history, delegation ancestry, revocation epochs, merchant concentration, and task progress. A payment can be individually valid and still be rejected because the sequence is now unsafe.

**Impossible demo:** jailbreak the agent, bypass its SDK, issue raw wallet calls, and send twenty individually valid micropayments across fresh addresses. The external reference monitor blocks the sequence when the behavioral budget is exhausted, while a replayed old state cannot advance the automaton.

The moat is a formally specified policy language compiled into account modules and passkey/HSM boundaries. The product is the state machine and its proof obligations, not another session-key wallet.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| AESP | Eight-check deterministic policy, review tiers, dual-signed commitments, human sovereignty invariant |
| TIVA | DID/VC identity, on-chain intent proof, ZK compliance, optional TEE attestation |
| Cloaked MCP | On-chain per-tx/daily/lifetime caps; the lesson that caps alone are insufficient |
| BBS+ AC spec | Selective disclosure, unlinkability, non-duplication, status credential, revocation |
| Delegation paper | Scoped delegation of presentations; delegatee identity binding; unforgeability and unlinkability |
| MinMandate | Task-scoped authority and per-call fresh views; avoid stable cross-merchant handles |

## Intuitive workflow

1. **The human creates an agent.** The wallet issues a BBS+ credential: "shopping agent, food and transport, 5 MON/day, expires in 30 days."
2. **The agent plans.** It can browse, compare, and propose. It cannot sign.
3. **The agent proposes a payment.** The runtime checks the action against the policy and the delegation graph.
4. **A prompt injection tries to drain the wallet.** The runtime blocks it before signing; the human gets an alert with the blocked action.
5. **A legitimate purchase needs review.** The runtime escalates because the merchant is new. The human approves with a passkey; the receipt is stored.
6. **The agent delegates a sub-task.** It issues a narrow delegation to a logistics agent. The sub-agent cannot exceed the parent scope.
7. **The merchant is paid.** On Monad, the payment settles quickly; on Zcash, the payout is shielded.
8. **The human audits.** A private dashboard shows spend by task and policy, not the agent's entire reasoning trace.

## Demo (5 minutes)

**Act 1 — The drain.** A jailbroken agent tries to send the entire balance to an attacker. The runtime blocks it. Show the policy decision.

**Act 2 — The slow drain.** The agent sends 20 small payments. The cumulative monitor freezes the wallet after the daily cap.

**Act 3 — The over-broad delegation.** The agent tries to pay a merchant outside its service class. The delegation graph rejects it.

**Act 4 — The sub-agent.** A logistics agent gets a narrow delegation. It tries to exceed the parent scope; the chain check fails.

**Act 5 — Private settlement.** Pay a merchant in shielded ZEC; the receipt proves policy compliance without revealing the amount.

**Close:** "The model can be compromised. The money cannot."

## Hackathon fit

- **Monad:** account abstraction, low fees, on-chain policy receipts, high-frequency agent payments.
- **Zcash:** shielded payout and private receipts.
- **ETHGlobal:** strong security/wallet narrative with a dramatic live attack.
- **Judging:** concrete threat model, deterministic enforcement, private delegation, working demo.

## Cons and risks

- **Policy expressiveness.** Deterministic rules cannot capture every human intent. A wrong-but-in-policy payment is still possible.
- **Signer compromise.** If the root key or passkey is compromised, the runtime is bypassed. The security claim is about a compromised **agent**, not a compromised signer.
- **TEE trust.** If TEE attestation is used, the hardware vendor becomes a trust anchor.
- **Delegation complexity.** Multi-hop delegation and revocation are hard to reason about; a bug can create an escape hatch.
- **Automaton governance.** A behavioral automaton is only as safe as its state semantics; changing a transition rule is a protocol upgrade, not a configuration tweak.
- **Privacy leakage.** On-chain policy receipts can reveal spending patterns if not minimized.
- **Adoption.** Merchants and agent frameworks must integrate the runtime.
- **No audit.** A hackathon prototype is not a wallet users should trust with real funds.

## Judge Q&A

**"How is this different from a session key?"** A session key caps spending; it does not enforce task scope, delegation, context binding, or cumulative behavior. The runtime is a policy boundary, not just a key permission.

**"What if the agent gets the user to approve?"** The review tier is a human decision. The runtime can still enforce hard caps and scope; it cannot stop a user from approving a bad payment.

**"Why BBS+?"** The agent can prove role and scope without revealing the human or reusing an identity across merchants. Revocation is privacy-preserving.

**"Why Monad?"** Cheap policy checks and receipts on every payment; high throughput for many agents.

**"Is it trustless?"** No. It reduces trust in the model and moves it to the runtime, the signer, and the policy. The demo states the trust boundary.

## Claims to avoid

- "Unhackable agent wallet."
- "Trustless."
- "The agent can never misbehave."
- "Production-ready."
- "No metadata leakage."

## One-sentence pitch

**Sovereign Runtime puts a deterministic policy kernel and a private delegation graph between the agent's reasoning and the wallet's key, so a compromised model can propose but cannot betray the human's limits.**
