# ShadeScore — Private Credit and Reputation for Agents

## Metadata

- **Target hackathons:** Zcash (primary), Monad (secondary)
- **Tracks:** privacy, agentic commerce, credit, identity, ZK
- **Papers used:** BBS+ anonymous credentials (2025/824), improved issuer hiding (2026/555), delegation of verifiable presentations (2025/2306), Zcash linkability (1712.01210), A2A payments SoK (2604.03733), Secure Agent Payments / TIVA (2511.15712)
- **One-liner:** An agent proves it is creditworthy without showing its transaction history, its human, or its counterparties.

## The problem

Agent commerce needs credit and reputation. A merchant wants to know whether an agent can pay, whether it has defaulted, and whether it is a serious counterparty. A lender wants to underwrite an agent's working capital. A marketplace wants to rank reliable agents.

Today, the only way to prove this is to reveal the transaction history. That is a privacy disaster:

- The history reveals the agent's strategy, suppliers, margins, and volume.
- The human behind the agent is often linkable through the history.
- The counterparties are exposed, even if they used shielded payments.
- A single reputation score becomes a stable identifier that follows the agent across merchants, recreating the cross-call linkage that private payments were meant to avoid.

On Zcash, even a shielded history leaks at the boundaries: the 2017 linkability study matched 31.5% of coins entering the Sprout pool through exact-amount round trips and timing. A credit score built from raw history would turn those boundary leaks into a public dossier.

## The solution

ShadeScore is a **private credit and reputation layer** built from predicates, not histories.

1. **Attested history, not raw history.** A trusted auditor, escrow contract, or payment processor issues a BBS+ credential over aggregate facts: "agent has completed 120 transactions," "no default in 12 months," "median settlement time < 1 hour," "disputed 2 of 100." The credential does not contain the transaction list.
2. **Predicate proofs.** The agent proves only the predicate the verifier needs: "default count = 0," "volume >= 50,000," "account age >= 180 days." The verifier learns the truth of the statement, not the underlying values.
3. **Issuer hiding.** The verifier accepts a set of approved auditors/issuers, but does not learn which one attested the agent. This prevents the issuer from becoming a de facto identity.
4. **Delegation with scope.** A human delegates credit authority to an agent with a verifiable presentation delegation: the agent can borrow up to X, for Y purpose, until Z date. The delegatee identity is bound, but the human is not revealed to every lender.
5. **Revocation without a public denylist.** A status credential proves the credential is not revoked, without publishing the agent's identifier on every check. Revocation updates the status, not the agent's public reputation.
6. **Shielded settlement.** Loan disbursement and repayment use Zcash shielded notes. The lender sees repayment proofs, not the agent's entire cash flow. Note splitting and batching reduce boundary linkability.
7. **Score as a proof, not a token.** There is no transferable "credit NFT" that becomes a stable cross-platform identifier. The agent presents a fresh proof to each verifier; the proof is unlinkable across verifiers.
8. **Accountable issuer hiding.** If an issuer attests false facts, an opening authority can attribute the fraud under a defined governance process. This is the AIH model: privacy by default, accountability by exception.

## Why this is out of this world

Most privacy-preserving credit systems prove a score. ShadeScore proves the **predicate the market actually needs**, with issuer hiding and scoped delegation, and settles in shielded ZEC. It separates three things that are usually conflated:

- the **fact** (no defaults),
- the **issuer** (which auditor verified it),
- the **agent/human identity** (who is behind it).

The demo: an agent with no public history gets a loan because it proves "0 defaults, 18 months, volume >= 100k" without revealing a single counterparty. A competitor scraping the chain learns nothing.

## Once-a-decade primitive

ShadeScore becomes a **query-private, non-reconstructible underwriting protocol**. The lender commits to the predicate it wants tested; the agent does not receive the exact threshold, and a single-use query capsule prevents a lender or colluding lenders from combining hundreds of yes/no answers into an approximate private history. The result is a proof of eligibility, not a portable score that becomes a new surveillance identifier.

**Impossible demo:** two colluding lenders ask complementary thresholds about volume, repayment time, and reserves. Neither can reconstruct the agent's exact values; a fabricated auditor credential is later opened and attributed without exposing honest borrowers. Credit becomes selectively verifiable without becoming a public dossier.

The moat is a governed federation of issuers with long-lived repayment attestations, query-rate governance, and accountable opening. The difficult asset is trusted historical coverage, not proof plumbing.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| BBS+ AC spec | Selective disclosure, unlinkability, non-duplication, hardware binding, status credential, privacy-preserving revocation |
| Issuer hiding | Malicious-issuer model, signed policy, accountable issuer hiding, no secret policy keys |
| Delegation paper | Scoped delegation of presentations; delegatee identity binding; unforgeability and unlinkability |
| Zcash linkability | Boundary-amount/timing leakage; why raw history is dangerous |
| A2A payments SoK | Payment-service decoupling; accounting and accountability gaps |
| TIVA | DID/VC identity, ZK compliance, on-chain policy, optional TEE |

## Intuitive workflow

1. **An agent wants credit.** It has 18 months of transactions, but no public profile.
2. **An auditor attests aggregates.** The auditor issues a BBS+ credential: "no default, 18 months, 140 completed transactions, median settlement 30 min."
3. **The lender asks for a predicate.** "Prove default count = 0 and volume >= 100k."
4. **The agent proves it.** The lender learns the predicates are true, not the exact volume, the counterparties, or the human.
5. **The issuer is hidden.** The lender sees that an approved auditor attested it, but not which one.
6. **The human delegates borrowing.** The agent can borrow up to 10,000 ZEC for inventory until March 31. The delegation is scoped and revocable.
7. **The loan settles privately.** Shielded ZEC is disbursed. Repayment proofs update the credential without exposing the cash flow.
8. **A fraudulent issuer is caught.** The opening authority attributes the false attestation; the agent's privacy is preserved for honest users.

## Demo (5 minutes)

**Act 1 — The dossier.** Show a transparent credit history: every counterparty, volume, and timing is visible. A competitor copies the strategy.

**Act 2 — The predicate.** The agent proves "0 defaults, volume >= 100k, age >= 18 months" without revealing the history. The lender approves.

**Act 3 — The issuer.** Two approved auditors attest the agent. The lender cannot tell which one; issuer-based profiling fails.

**Act 4 — The delegation.** The human delegates a 10k ZEC credit line to the agent for inventory only. The agent tries to borrow for a different purpose; the delegation rejects it.

**Act 5 — The default.** The agent defaults. The status credential is revoked; the next lender sees the revocation without learning the agent's entire history.

**Close:** "Credit without a dossier."

## Hackathon fit

- **Zcash:** shielded loan disbursement/repayment, privacy-preserving revocation, linkability-aware note hygiene.
- **Monad:** credential verification, lender marketplace, delegation registry, cheap per-query proofs.
- **ETHGlobal:** privacy + DeFi credit; strong institutional use case.
- **Judging:** clear predicate proofs, issuer hiding, revocation, and a working loan flow.

## Cons and risks

- **Issuer trust.** The auditor can lie. Accountable issuer hiding catches some fraud, but the governance/opening authority is a trust anchor.
- **Predicate leakage.** A predicate can still leak information (e.g., "volume >= 100k" narrows the range). The query set must be designed carefully.
- **Correlation across queries.** A lender can ask many predicates and reconstruct a profile. Rate limits and query privacy are needed.
- **Hidden predicates reduce usability.** A lender may need to prove that its private query was legitimate without revealing underwriting strategy; query capsules require governance and careful recovery paths.
- **Revocation linkability.** Status checks can link the agent to the issuer or the status epoch.
- **Credit risk.** The system proves facts, not future ability to repay. A private score can still be wrong.
- **Regulatory.** Lending/KYC/AML requirements may force disclosure; the design needs a compliance path.
- **No audit.** The cryptography is a prototype.

## Judge Q&A

**"How is this different from a ZK credit score?"** It proves predicates with issuer hiding and scoped delegation, and settles in shielded ZEC. It does not publish a reusable score token.

**"What stops an issuer from lying?"** Accountable issuer hiding and a governance/opening process. It is not trustless; it is accountable.

**"Can the lender reconstruct the history from many queries?"** Not perfectly, but query correlation is a real risk. The demo shows a query-privacy budget and rate limits.

**"Why Zcash?"** Loan flows and repayments are shielded; the agent's counterparties are not exposed.

**"Is the score transferable?"** No. A transferable score becomes a stable identifier; ShadeScore uses fresh proofs per verifier.

## Claims to avoid

- "Trustless credit."
- "No leakage."
- "The issuer cannot lie."
- "Production-ready."
- "One score for everything."

## One-sentence pitch

**ShadeScore lets an agent prove creditworthiness with issuer-hidden predicate credentials and shielded ZEC settlement, so it can borrow without exposing its history, counterparties, or human.**
