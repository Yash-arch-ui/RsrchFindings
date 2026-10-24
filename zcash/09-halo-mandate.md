# Halo Mandate — Recursive Proof-Carrying Agent Authority

## Metadata

- **Target hackathons:** Zcash (primary), Monad (verification layer), ETHGlobal
- **Tracks:** Zcash, ZK, recursive proofs, agentic commerce, privacy
- **Papers used:** Halo (2019/1021), Orchard audit (2026), MinMandate (2026/1674), BBS+ credentials (2025/824), issuer hiding (2026/555), Zcash linkability (1712.01210)
- **One-liner:** An agent carries one recursive proof of its entire authority, policy, and payment history, and reveals only the predicate a merchant needs.

## The problem

An agent that acts for a human accumulates evidence: a delegation credential, a task mandate, a spending policy, a set of prior payments, and a revocation status. To prove it is allowed to make a payment, it currently has to present a bundle of credentials, signatures, and receipts. That bundle is:

- **Linkable.** Reusing the same credential or receipt across merchants creates a stable identifier.
- **Large.** More calls mean more evidence; verification cost grows.
- **Leaky.** Each credential reveals attributes the merchant does not need.
- **Hard to compose.** A sub-agent needs to prove it inherits authority from a parent, without revealing the parent's identity or the whole chain.
- **Brittle.** Revocation and expiry must be checked against multiple sources.

The Halo line of work solved a related problem for proofs: recursive composition lets a large computation be verified incrementally, without a trusted setup. No one has applied that idea to **agent authority**.

## The solution

Halo Mandate is a **recursive proof of agent authority**:

1. **Base proof.** The human's wallet proves: "I issued a task-scoped mandate to agent A, with scope S, budget B, expiry E, and policy P." The proof is bound to a BBS+ credential and an issuer-hiding policy.
2. **Delegation proof.** A sub-agent proves: "I received a scoped delegation from an agent whose authority is valid under the parent proof, and my scope is a subset." The delegation paper's scope/delegatee binding is the statement.
3. **Payment proof.** Each payment proves: "This shielded note is authorized by the mandate; the amount is within the remaining budget; the merchant class matches; the nullifier is fresh." The payment does not reveal the buyer or the rest of the history.
4. **Recursive aggregation.** The agent composes the base, delegation, and payment proofs into a single recursive proof. The verifier checks one proof, not a bundle. The proof size is independent of the number of payments; verification time is linear in the circuit size but constant in the history length.
5. **Selective disclosure.** The merchant receives only the predicate it needs: "authorized for food service, amount <= 2 ZEC, task not expired." It does not receive the human's identity, the budget, the other merchants, or the payment history.
6. **Nullifier and revocation.** The proof carries a one-time nullifier and a status-credential check. Double spending and revoked authority are rejected without a public denylist.
7. **Zcash settlement.** The payment is a shielded Orchard note. The recursive proof is verified by the wallet/merchant; the chain sees a shielded transfer and a nullifier, not the authority graph.

## Why this is out of this world

Agent authority is usually a document or a token. Halo Mandate makes it a **recursive proof object** that grows in evidence but not in size. The agent can prove a year of delegated authority and a clean payment history in one proof, without revealing the history. It is the missing link between Zcash's recursive-proof lineage and agentic commerce.

The demo: an agent presents one proof to a merchant. The merchant verifies it in milliseconds. Behind that proof are 40 payments, 3 sub-delegations, a revocation check, and a budget proof. The merchant sees only "authorized, 2 ZEC, food service."

## Sources and what each contributes

| Source | Contribution |
|---|---|
| Halo | Recursive proof composition without a trusted setup; nested amortization; Pasta/Halo2 lineage |
| Orchard audit | Current Zcash circuit safety; PCZT/proof-size hygiene; what a production verifier must check |
| MinMandate | Task-scoped authority, per-call views, one-time serials, funding tags |
| BBS+ AC spec | Selective disclosure, unlinkability, non-duplication, revocation |
| Issuer hiding | Policy over approved issuers; accountable issuer hiding |
| Zcash linkability | Boundary leakage; why the payment proof must be paired with note hygiene |

## Intuitive workflow

1. **The human creates a mandate.** "Agent A may buy food and transport for the offsite, up to 8 ZEC, until Friday."
2. **The agent makes payments.** Each payment produces a proof: authorized, in budget, fresh nullifier, merchant class.
3. **The agent delegates a sub-task.** A logistics agent gets a narrow sub-mandate; its proof includes the parent proof.
4. **The agent aggregates.** The base mandate, the delegation, and all payments become one recursive proof.
5. **The merchant verifies.** It checks one proof and sees only the predicate it needs.
6. **The agent tries to overspend.** The recursive proof fails the budget predicate.
7. **The agent is revoked.** The status credential check fails; the next proof is rejected.
8. **The payment settles in ZEC.** The merchant is paid; the chain sees a shielded note.

## Demo (5 minutes)

**Act 1 — The bundle.** Show the current approach: a pile of credentials, receipts, and signatures. The merchant must verify all of them and learns too much.

**Act 2 — The recursive proof.** Replace the bundle with one proof. Verify it in one step. Show the proof size does not grow with 40 payments.

**Act 3 — The predicate.** The merchant sees "authorized, 2 ZEC, food service," not the budget or the history.

**Act 4 — The sub-agent.** A sub-delegation is composed into the proof; the parent's identity stays hidden.

**Act 5 — The attack.** Overspend, replay, and revoked authority all fail the proof.

**Close:** "One proof. A year of authority. No history."

## Hackathon fit

- **Zcash:** Halo/Halo2, Orchard, shielded settlement, recursive proof lineage.
- **Monad:** fast proof verification and merchant acceptance; on-chain nullifier/status registry.
- **ETHGlobal:** deep ZK + agentic commerce; strong technical demo.
- **Judging:** genuine recursive composition, not a wrapper; clear privacy and scalability win.

## Cons and risks

- **Circuit complexity.** Recursive proofs of heterogeneous statements (BBS+, delegation, payment, revocation) are hard to compose.
- **Trusted setup.** Halo is transparent, but Halo2/Orchard has its own setup and circuit assumptions. The demo must be precise.
- **Proving cost.** Recursive proving is expensive on a laptop; a hackathon demo may use a small circuit or a precomputed proof.
- **Verifier work.** Halo is recursive but not fully succinct; verification is linear in circuit size. The claim must be "constant in history," not "constant time for arbitrary computation."
- **Credential integration.** BBS+ and Halo2 use different curves/assumptions; a composed proof may need a bridge or a custom circuit.
- **Revocation freshness.** The status check must be recent; a stale proof can authorize a revoked agent.
- **No audit.** The composition is a prototype.

## Judge Q&A

**"Why recursive proofs?"** The authority evidence grows with every payment. Recursion keeps the proof size independent of history, so the merchant verifies one object instead of a bundle.

**"Is Halo fully succinct?"** No. It is recursive; verification is linear in circuit size but independent of recursion depth. We state that precisely.

**"How do BBS+ and Halo compose?"** The BBS+ credential is verified inside the recursive circuit or bridged via a commitment; the demo uses a simplified credential circuit. This is the main engineering risk.

**"Why Zcash?"** The payment is an Orchard note; the proof carries the authority. Zcash provides the shielded settlement and the Halo2 lineage.

**"What leaks?"** The merchant sees the predicate and the payment amount; the chain sees a shielded note. Timing and merchant identity remain.

## Claims to avoid

- "Constant-time verification for arbitrary computation."
- "Trustless."
- "Unlimited recursion at no cost."
- "Production-ready."
- "No leakage."

## One-sentence pitch

**Halo Mandate compresses an agent's mandate, delegation chain, payment history, and revocation status into one recursive proof, so a merchant verifies authority without seeing the agent's history or human.**
