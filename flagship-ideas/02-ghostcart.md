# GhostCart — Private Agent Procurement with Sealed Bids and Shielded Settlement

## Metadata

- **Target hackathons:** Zcash (primary), Monad (secondary)
- **Tracks:** privacy, agentic commerce, procurement, shielded payments, ZK
- **Papers used:** MinMandate (2026/1674), BBS+ anonymous credentials (2025/824), issuer hiding (2026/555), Zcash linkability (1712.01210), AP2 security analysis (2608.23858), Blockchain A2A payments SoK (2604.03733)
- **One-liner:** An agent runs a private reverse auction for a purchase, and no one learns the losing bids or the buyer's budget.

## The problem

Agentic procurement is an information leakage engine. A buyer agent asks several merchants for quotes; each merchant learns the buyer's identity, budget range, timing, and the fact that a purchase is coming. Losing merchants learn the winner's price. A competitor watching the chain can infer the buyer's strategy, and a malicious merchant can tailor a quote after seeing the competition. If the buyer pays on a transparent chain, the payment graph links the whole workflow.

Traditional RFQ (request for quote) solves the coordination problem but not privacy: the buyer reveals the request to every seller, and the winning price becomes a reference point. Sealed-bid auctions solve the bid-privacy problem but usually assume a trusted auctioneer or a one-shot auction. Agentic commerce needs **repeated, private, multi-merchant procurement** with delivery accountability.

## The solution

GhostCart is a private procurement protocol for agents:

1. **Private RFQ.** The buyer agent publishes a commitment to the request: item, quantity, delivery deadline, quality constraints, and a maximum budget. The request itself is encrypted to an enclave/MPC committee or hidden behind a commit-reveal scheme; merchants see only a category and a deadline until they commit to bid.
2. **Merchant eligibility.** Merchants prove eligibility with BBS+ credentials: business license, insurance, service category, reputation tier. Issuer hiding hides which authority certified them. Revocation is checked with a status credential.
3. **Sealed bids.** Each merchant submits an encrypted bid plus a ZK proof that the bid satisfies the request constraints and the merchant's capacity. Bids are committed on-chain; the content is not public.
4. **Private clearing.** A threshold committee or MPC computes the winning bid and a uniform clearing rule. The losing bids remain sealed. The winner is revealed only to the buyer and the winner; the public sees that a procurement round closed.
5. **Delivery escrow.** The buyer funds a shielded escrow note. The merchant delivers; the buyer (or an evaluator) signs a delivery attestation. The escrow releases the shielded payment. If delivery fails, a dispute window and a bond handle the refund.
6. **Receipt binding.** The payment receipt commits to the RFQ, the winning bid, and the delivery attestation, but not to the buyer's identity. This closes the A2A SoK's payment-service decoupling gap: payment finality is not delivery.
7. **Boundary hygiene.** The buyer splits funding into notes of non-round amounts, batches shield/unshield operations, and avoids exact round-trip amounts, directly addressing the 2017 Zcash linkability heuristic.

## Why this is out of this world

Most private payment demos hide the payment. GhostCart hides the **market**: who was asked, what they bid, who lost, and what the buyer was willing to pay. The buyer gets competitive quotes without revealing leverage; merchants get a fair shot without colluding; the chain gets a settlement without the strategy.

It also fixes a subtle agentic-commerce failure: a cheap payment is not a delivered service. GhostCart binds the payment, the bid, and the delivery proof into one receipt chain.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| MinMandate | Task-scoped authority and fresh per-call views; adaptive merchant selection without preauthorizing every seller |
| BBS+ AC spec | Merchant eligibility with selective disclosure, unlinkability, non-duplication, revocation |
| Issuer hiding | Merchant credentials do not reveal which authority certified them |
| Zcash linkability | Concrete boundary-amount and timing heuristics; motivates note splitting and batching |
| AP2 security analysis | Context commitment; signed mandate must bind discovery/cart/tool outputs |
| A2A payments SoK | Four-stage lifecycle; payment-service decoupling; receipt binding |

## Intuitive workflow

1. **The buyer agent needs 500 custom widgets.** It writes a private RFQ with specs, deadline, and a hidden maximum budget.
2. **Five merchants see a category request.** They prove they are licensed and insured without revealing their identity or certification authority.
3. **Each merchant submits a sealed bid.** No merchant sees another bid. A malicious merchant cannot undercut after seeing the competition.
4. **The private clearing committee picks the best conforming bid.** The loser bids stay sealed forever.
5. **The buyer funds a shielded escrow.** The merchant sees that funds exist, not the buyer's balance or other purchases.
6. **The merchant delivers.** The buyer signs a delivery attestation. The escrow releases the shielded payment.
7. **The buyer gets a private receipt.** It can prove to an auditor that the procurement was competitive and delivered, without revealing the bids.
8. **A competitor scans the chain.** It sees a shielded transfer and a procurement round, not the buyer, the bids, or the losing merchants.

## Demo (5 minutes)

**Act 1 — The leak.** Run a public RFQ. Show every merchant learning the budget and the winning price; show a competitor reconstructing the buyer's strategy.

**Act 2 — The sealed auction.** Submit three encrypted bids. The public sees commitments only. The committee clears privately; the losing bids remain sealed.

**Act 3 — The eligibility proof.** A merchant proves license and insurance with a BBS+ credential. The verifier cannot tell which authority issued it.

**Act 4 — Delivery binding.** The merchant delivers; the buyer signs the attestation; the shielded escrow releases. A fake delivery attempt fails the receipt check.

**Act 5 — Boundary test.** Run the linkability lens on a naive shield/unshield. Show a round-trip match. Turn on note splitting and batching; the match disappears.

**Close:** "The buyer got five bids. The world got none."

## Hackathon fit

- **Zcash:** shielded escrow, note splitting, viewing keys, linkability lens.
- **Monad:** high-throughput bid commitments, credential verification, committee coordination, cheap micro-payments.
- **ETHGlobal:** strong procurement/agent-commerce story with a real privacy attack and a measurable fix.
- **Judging:** sealed-bid mechanism + shielded settlement + delivery binding is a complete market primitive, not a dashboard.

## Cons and risks

- **Committee trust.** Private clearing needs a threshold committee, MPC, or TEE. If all committee members collude, bids leak.
- **Merchant adoption.** Sealed-bid procurement is unfamiliar; merchants may prefer transparent RFQs.
- **Regulatory constraints.** Public procurement often requires transparency and auditability. GhostCart needs a selective-disclosure audit path.
- **Delivery disputes.** The attestation is only as good as the evaluator. A bonded evaluator or multi-sig delivery proof is needed.
- **Zcash boundary leakage.** Note splitting and batching reduce but do not eliminate timing/linkability.
- **Complex UX.** The buyer agent must manage escrow, deadlines, and dispute windows.
- **No production audit.** The protocol is a hackathon prototype.

## Judge Q&A

**"Why not use a normal sealed-bid auction?"** Normal auctions assume a trusted auctioneer and one-shot bidding. GhostCart adds agent authority, merchant credentials, shielded settlement, and delivery binding across repeated procurements.

**"How do you prove the winning bid is valid?"** A ZK proof that the bid satisfies the RFQ constraints, plus a committee/MPC clearing transcript. The losing bids stay sealed.

**"What if the committee cheats?"** Threshold trust; a bond and a dispute window. A fully trustless private auction is still hard.

**"Why Zcash?"** The payment and escrow are shielded; the public chain does not learn the buyer, the amount, or the link to the RFQ.

**"What leaks?"** The existence of a procurement round, merchant participation if they choose to reveal it, timing, and network metadata. The demo states this.

## Claims to avoid

- "Fully private procurement."
- "Trustless auction."
- "No metadata leakage."
- "Production-ready."
- "The committee cannot collude."

## One-sentence pitch

**GhostCart lets an AI agent run a sealed-bid procurement and pay the winner in shielded ZEC, so the buyer gets competition without revealing the budget, the losing bids, or the workflow.**
