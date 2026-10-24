# Privacy x Perpetuals: Synthesis and Flagship Ideas

## Thesis

Privacy is not a feature you bolt onto a perpetual. It is a change to the **information schedule** of the entire market:

- who sees an order before it executes,
- who sees a position while it is open,
- who sees the funding signal and when it is measured,
- who can detect a liquidation,
- what settlement reveals after the fact,
- and which trust assumptions replace the missing data.

The five papers collectively show that every privacy layer leaks somewhere else. Encrypted order flow can shield funding manipulation (Paper 1). Private positions push risk into aggregate OI and an orchestrator (Paper 2). Private matching leaks rank, top-K prices, and participation (Papers 3 and 4). Private settlement needs bonds, threshold custody, and refunds (Paper 5). The winning product is therefore not "a private perp DEX." It is a **privacy-native perp stack with explicit leakage, solvency, and funding guarantees**.

## Once-a-decade thesis

The category-defining move is **sealed books, public invariants**.

Hide the order book and the positions, but prove the properties that make the market safe: worst-case solvency under committed stress, a bound on funding manipulation, and observational equivalence against a declared adversary. Most private-perp pitches stop at "positions are private." That is a feature. A perp where LPs can verify hidden-book solvency, where funding carries an immunity certificate, and where a passport can prove or disprove observer equivalence is a new market category: **proof-carrying private market infrastructure**.

The four flagship ideas are the four invariants:

| Idea | Public invariant over private state |
|---|---|
| VeilPerp | No hidden position can create bad debt beyond the insurance fund under the committed stress set |
| CipherClock | No concealed batch can extract more than a published funding transfer under declared correction capacity |
| VeilPassport | The venue is observationally equivalent to its privacy claim, or the passport returns a reconstruction counterexample |
| Bonded Shade | A disputed match reveals only the disputed edge, and privacy loss is bonded and compensable |

## What the papers actually prove

| Question | Answer | Source |
|---|---|---|
| Does encrypted ordering always reduce manipulation? | No. It can increase self-authored funding manipulation by shielding correction. | Paper 1 |
| Is fast decryption enough? | No. A committed batch cannot admit an adaptive correction; the reaction gap is a scheduling property. | Paper 1 |
| Is a private position enough for a private perp? | No. Aggregate OI, oracle prices, timing, funding, and liquidation leak. | Papers 2, 3 |
| Can a perp hide individual positions and still publish OI? | Not atomically without leaking sizes in a ZK execution model; a trusted aggregator or a different proof design is needed. | Paper 2 |
| Is private matching end-to-end privacy? | No. Matching output, rank, counterparties, settlement activity, and state changes leak. | Papers 3, 4 |
| Can private matching be economically safe? | Yes, with insured MPC, threshold custody, public verifiability, and refunds. | Paper 5 |
| Are any of these production-ready perps? | No. ZKPerp is testnet/unaudited; Rialto and P2DEX are spot/one-shot; the SoK is a taxonomy. | All |

## The privacy x perp threat model

### Adversaries

- **Public observer / MEV searcher** — sees transactions, state, funding, liquidations.
- **Operator / orchestrator** — sees aggregate state or individual orders; may lie or censor.
- **Keeper / liquidator** — needs enough information to act; may front-run or collude.
- **Oracle relayer** — can delay or, with a quorum, corrupt prices.
- **Compliance server** — maps identity to wallet; may leak or over-block.
- **Counterparty** — learns trade terms or position through matching/settlement.
- **Insider / server coalition** — breaks MPC threshold or threshold signature assumptions.

### Assets to protect

1. **Order existence and timing.**
2. **Order side, size, limit price, leverage.**
3. **Position size, entry, collateral, liquidation level, PnL.**
4. **Funding payments and accrued yield.**
5. **Liquidation intent and timing.**
6. **Trader identity / wallet linkage.**
7. **Aggregate risk** — OI, insurance fund, solvency.

### The leakage chain

```text
private order
  -> batch membership / timing leak
  -> matched counterparty / clearing price leak
  -> position commitment + oracle price
  -> funding payment / OI change
  -> liquidation price inference
  -> settlement activity / account linkage
```

A perp privacy claim must address **each arrow**, not just the first box.

## Design principles

1. **Reveal, correct, measure, then pay.** Never let the first post-reveal state set funding. Insert a correction window and align position eligibility with the public measurement window (Paper 1).
2. **Separate the signal ledger from the tax-base ledger.** Hiding the order but allowing timestamp entry is not enough. Eligibility must be snapshotted before the batch (Paper 1).
3. **Commit to risk, not just to positions.** Publish aggregate OI, insurance coverage, and solvency proofs; keep individual positions private (Paper 2).
4. **Make leakage explicit.** Use phase × observer × information claims; no single "private" badge (Paper 3).
5. **Batch, don't continuously expose.** Private batch auctions hide order flow and enable fair matching, at the cost of latency and rank leakage (Paper 4).
6. **Bond the intermediaries.** Threshold custody, server bonds, public verifiability, and refunds turn trust into an economic guarantee (Paper 5).
7. **Assume liquidation detection is adversarial.** A private perp must decide who can detect underwater positions and what they learn. There is no free lunch.
8. **Price the privacy budget.** Every reveal (top-K, OI, funding, liquidation) is a deliberate disclosure. Show the tradeoff.

## Flagship slate

### 1. VeilPerp — the privacy-native perpetual exchange

**One-liner:** A perpetual DEX where orders and positions are private, aggregate risk is publicly provable, funding is manipulation-resistant, and liquidation is threshold-executed.

**Why it wins:** It is the only idea that composes all five papers into one coherent product: private matching (4/5), private positions (2), leakage accounting (3), and funding security (1). It is a full perp, not a dashboard or a matching demo.

**Users:**

- Institutional traders who cannot show size or strategy.
- LPs who need aggregate risk and solvency visibility.
- Traders who want MEV-resistant execution.
- Risk desks that need a privacy/risk passport.

**Core modules:**

| Module | Function | Paper |
|---|---|---|
| **VeilNotes** | Private position notes: size, entry, collateral, leverage, PnL, liquidation level | 2 |
| **ShadeMatch** | Private batch order flow and matching (commit-reveal / threshold encryption / MPC) | 3, 4, 5 |
| **CipherClock** | Reveal-correct-measure-pay funding; eligibility snapshot; correction buffer | 1 |
| **ProofOfRisk** | ZK/attested aggregate OI, insurance coverage, solvency | 2, 3 |
| **ThresholdLiq** | 1-of-N or k-of-N keeper proofs of underwaterness; encrypted hints | 2 |
| **VeilPassport** | Phase × observer leakage map + risk attestation | 3 |
| **BondedSettle** | Server/keeper bonds, public verifiability, refund path | 5 |

**End-to-end workflow:**

1. **Onboard.** Trader deposits collateral into a private note. A compliance proof (if required) proves allowlist membership without revealing identity.
2. **Order intent.** Trader submits an encrypted/secret-shared perp intent: side, size, limit, leverage, collateral, expiry. A ZK proof shows collateral sufficiency and leverage bound.
3. **Batch discovery.** Orders collect for a fixed window. The public sees only that a batch exists, not its contents. No continuous order book.
4. **Private matching.** MPC or FHE/commit-reveal matching computes a uniform clearing price and matched allocations. Unmatched orders never reveal their limits.
5. **Risk proof.** The venue publishes aggregate long/short OI, insurance-fund coverage, and a solvency proof. Individual positions remain private.
6. **CipherClock funding.** The batch's price impact is revealed, then a correction window `L` runs. Funding is measured only after correction. Positions opened in the concealed batch are ineligible for the imminent event, or accrue over the public measurement window.
7. **Position notes.** Matched positions become private notes. The chain stores commitments and aggregate state, not size/entry/direction.
8. **Liquidation.** Threshold keepers receive encrypted hints or proofs. A keeper proves the position is below maintenance margin using a fresh oracle price, without revealing the position to the public. A bond covers wrongful liquidation.
9. **Close / settle.** Trader closes with a commitment proof. PnL is settled from the pool; the public sees a settlement occurred, not the position details.
10. **Passport.** VeilPassport publishes what leaked in each phase (batch membership, aggregate OI, settlement activity) and the trust assumptions.

**Once-a-decade primitive: Sealed-Book Solvency Envelope.** The venue proves, for a committed set of stress paths (price gaps, funding spikes, correlated liquidation waves), that the hidden book cannot produce bad debt beyond the insurance fund. The proof quantifies over all private positions without revealing any of them. It is not aggregate OI with a risk dashboard; it is a worst-case joint-risk certificate over a book the verifier cannot see.

**Supporting primitives:**

- **Eligibility snapshot + correction buffer** as a first-class perp funding rule.
- **Public risk proofs over private positions** — aggregate solvency without individual disclosure.
- **Privacy-aware liquidation** — prove underwaterness without publishing the position.
- **Leakage passport** — a standardized, per-observer privacy claim for a perp venue.
- **Bonded private batch settlement** — optimistic on-chain footprint with refunds.

**Impossible demo:** smuggle a hidden 50x position into a batch that would blow through the insurance fund under a 20% gap. The public envelope rejects the batch even though no observer can see the position, the side, or the collateral. Resize the hidden position and the same proof passes. The judge watches the venue catch an insolvency it cannot see.

**Demo moment (narrative):** a whale opens a $50M short. The public sees a batch and a solvency envelope, not the side or size. The batch clears. CipherClock delays funding measurement, so the whale cannot harvest the batch's own price impact. A keeper liquidates a different underwater position using a threshold proof; the public sees a liquidation occurred, not whose or how large. VeilPassport shows the exact leakage surface. The judge tries to infer the whale's position from public data and fails.

**Moat:**

- The stress-scenario library and the risk circuits that quantify over hidden positions.
- A solvency-envelope standard that LPs and venues can price and require.
- The funding/liquidation composition and the leakage dataset.
- The batch-clearing and bonding mechanism.

**Cons:**

- **Complexity.** Private matching + private positions + funding + liquidation is a lot of cryptography. A hackathon build must scope down.
- **Liquidation detection is unsolved.** Threshold keepers or encrypted hints reintroduce trust; a fully private permissionless liquidation design is open research.
- **Envelope scope.** The solvency proof is only as good as the committed stress set, correlation model, and maintenance assumptions; a valid envelope can still miss a novel shock.
- **Batch admission.** A venue can game the envelope by censoring risky positions from the batch; batch admission needs a separate accountability path.
- **Public aggregate OI still leaks.** Time-series changes in OI, funding, and settlement activity can reveal a large position.
- **Batch auctions change UX.** Latency and fill quality differ from a continuous order book.
- **MPC/FHE cost.** Private matching at scale is expensive; quantifying over hidden positions is even heavier; a demo can use small batches.
- **Trust surface.** Oracle quorum, keeper set, server bonds, compliance server, and any aggregator are trust assumptions. The passport must state them.
- **Regulatory uncertainty.** Private derivatives may face KYC/AML obligations; the compliance layer must be credible.
- **Not audited.** Do not present as production-ready.

**Scoring (1-5):**

| Dimension | Score | Why |
|---|---|---|
| Novelty | 5 | Composes five research threads into a new perp stack |
| Technical depth | 5 | ZK + MPC + funding + liquidation + risk proofs |
| Demoability | 5 | Whale inference failure + liquidation proof is visual |
| Feasibility (hackathon) | 3 | Full stack is hard; scope to a testnet prototype |
| Judge appeal | 5 | Privacy + DeFi + market integrity |
| Moat | 5 | Leakage dataset, risk circuits, funding rule |
| **Total** | **28/30** | Highest ceiling, highest execution risk |

### 2. CipherClock — funding security for private perps

**One-liner:** A decryption-aware funding clock and attack simulator that prevents encrypted order flow from becoming a funding-manipulation shield.

**Why it wins:** It is the most novel finding in the batch. It turns a subtle economic attack into a product: a funding rule, a simulator, and a verifiable eligibility snapshot. It is also a natural module for any private perp, including VeilPerp.

**Once-a-decade primitive: Manipulation-Immunity Certificate.** CipherClock compiles a decryption-aware funding schedule together with a machine-checkable certificate: for a declared correction capacity and attacker retention, no concealed batch can extract more than a published funding transfer. The certificate is a proof over the reaction-gap model, not a dashboard estimate. LPs can require it, and a venue can publish it per market and per stress regime.

**Workflow:**

1. Input venue batch/decryption schedule, funding window, cap, and eligibility rule.
2. Parameterize correction intensity and attacker retention.
3. Compute reaction gap `g`, amplification `R_g`, endowed/created attack value, and two-ledger index `Gamma`.
4. Recommend correction buffer `L`, eligibility snapshot, and notional concentration limit.
5. Generate a funding module (Solidity/Noir/Aleo) that enforces the rule.
6. Emit a manipulation-immunity certificate bound to the rule, the correction assumption, and the stress set.
7. Publish a machine-readable spec with assumptions and sensitivity.

**Impossible demo:** run the same self-authored attack against two venues. The immediate-funding venue pays the attacker. The CipherClock venue rejects the batch because the immunity certificate for that batch would exceed the published bound, then clears it after the correction window with the attack value collapsed. Privacy is identical in both runs; only the certificate differs.

**Moat:** the calibrated reaction-gap dataset and the certificate format. A simulator is copyable; a certificate that LPs, venues, and regulators can verify is not.

**Cons:** calibration of `m` and correction intensity is hard; the certificate is conditional on the correction-capacity assumption, so if correctors vanish the bound is void; the model is one-shot, so repeated-game attackers may adapt; the product is a simulator + small contract, not a full exchange; venues may resist the UX change; the attack is subtle.

**Scoring:** Novelty 5, technical 4, demo 4, feasibility 5, judge 4, moat 4 = 26/30.

### 3. VeilPassport — leakage and solvency attestation

**One-liner:** A privacy passport and risk attestation layer that tells traders, LPs, and auditors exactly what a perp venue hides, what it leaks, and whether it is solvent.

**Why it wins:** Every private perp will need this. It is a B2B infrastructure play with a strong visual demo: a "privacy nutrition label" for perp DEXs, plus a ZK solvency proof.

**Once-a-decade primitive: Adversary-Equivalence Passport.** VeilPassport proves that two venue configurations are observationally equivalent to a declared observer model, or returns a concrete reconstruction counterexample. It is not a privacy score. It is a proof-or-counterexample about what a phase-by-observer transcript lets an adversary infer. A venue can claim "private against the public, not against the keeper"; the passport either proves the claim under the declared model or shows the exact field combination that breaks it.

**Workflow:**

1. Ingest contracts, docs, public state, and sample transactions.
2. Map each observable to phase × observer × information.
3. Build a leakage graph: side + OI + timing + funding + liquidation.
4. Run inference scenarios to show what can be reconstructed.
5. Emit either an equivalence certificate or a minimal reconstruction counterexample.
6. Generate a ZK/attested solvency proof over aggregate risk.
7. Publish a passport hash on-chain; venues can claim specific protections.
8. Recommend fixes: batching, eligibility snapshots, decoys, threshold keepers, aggregate-only disclosure.

**Impossible demo:** a venue claims "private perps." VeilPassport finds a two-field reconstruction (OI delta + funding payment timing) that identifies a whale's direction, then emits the counterexample. After the fix, it proves the reconstruction impossible under the declared observer model, while the solvency proof still passes. The judge sees a privacy claim become a falsifiable engineering artifact.

**Moat:** a cross-venue leakage corpus and a standard adversary model. The certificate becomes the way LPs and regulators compare private venues; a dashboard cannot substitute for a proof-or-counterexample.

**Cons:** the certificate is relative to the declared observer model, and a stronger adversary can invalidate it; the scanner depends on public data/docs or venue cooperation; inference counterexamples are probabilistic; passports can be gamed by venues that hide fields off-chain; it is an audit tool, not a trading venue; it overlaps with VeilRisk/VeilMap and must be merged.

**Scoring:** Novelty 4, technical 4, demo 5, feasibility 5, judge 4, moat 5 = 27/30.

### 4. Bonded Shade — insured MPC clearinghouse for perp intents

**One-liner:** A private perp clearinghouse where bonded servers run publicly verifiable MPC matching, threshold custody holds collateral, and cheating is slashed.

**Why it wins:** It is the strongest settlement/security story: optimistic two-transaction settlement, public verifiability, refunds, and no single server custody. It is a natural execution layer for VeilPerp.

**Once-a-decade primitive: Challengeable Private Clearing with Privacy-Loss Bonds.** A private batch clearing where any participant can challenge a match; the dispute proof reveals only the minimal disputed edge, not the order book; and the server bond compensates traders whose edge was exposed. It turns privacy into an insurable, adjudicable property instead of a promise.

**Workflow:**

1. Bonded server set registers a threshold key and a privacy-loss bond.
2. Traders submit secret-shared perp intents + collateral proofs.
3. Servers run publicly verifiable MPC matching and compute clearing.
4. Optimistic on-chain settlement; positions become commitments.
5. CipherClock funding after correction.
6. Threshold liquidation with challenge period.
7. A dispute reveals only the disputed edge; cheating slashes the bond and compensates exposed traders.
8. Public aggregate risk proof.

**Impossible demo:** a colluding server tilts a match. A challenger proves the bad share without revealing any other order; the bond is slashed, the honest book stays sealed, and the traders whose edge was exposed are paid from the privacy-loss bond. The happy path still settles in two transactions.

**Moat:** the dispute protocol and the privacy-loss bond market. Anyone can copy MPC; the adjudication and insurance layer is the trust asset.

**Cons:** selective dispute still leaks the disputed edge, and privacy-loss bonds price that leak but do not eliminate it; bond sizing and dispute latency are adversarial game-design problems; MPC is heavy for continuous perps; the server/bond layer is a trust assumption; perp state is not one-shot; deposits are capital-intensive; WAN latency; matching must extend to variable-size positions; no production audit.

**Scoring:** Novelty 4, technical 5, demo 5, feasibility 3, judge 4, moat 4 = 25/30.

## Recommended portfolio

**Build VeilPerp around the Sealed-Book Solvency Envelope, with CipherClock's manipulation-immunity certificate and VeilPassport's adversary-equivalence certificate as the public guarantees, and Bonded Shade's challengeable private clearing as the settlement mode.**

Why this portfolio:

- **VeilPerp** gives the judges a complete product: private trading, private positions, a public worst-case solvency envelope, and private liquidation.
- **CipherClock** gives a novel, research-backed mechanic that no other team will have: a funding rule with a proof-carrying immunity bound.
- **VeilPassport** gives the demo its "aha" moment: a privacy claim that becomes a proof or a counterexample, not a badge.
- **Bonded Shade** gives the security story: challengeable clearing, privacy-loss bonds, threshold custody, public verifiability, and refunds.

A weaker alternative is to build only VeilPassport. It is feasible and judge-friendly, but it is an audit tool, not a perp. The clean-sweep play is the full stack with a tightly scoped demo.

## Reference architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                        VeilPerp frontend                     │
│  trade · batch · portfolio · passport · risk · liquidation  │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                     Private intent layer                     │
│  encrypted order · collateral proof · compliance proof      │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                     ShadeMatch / MPC                         │
│  batch discovery · private matching · clearing price        │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                     VeilNotes / commitments                  │
│  position notes · nullifiers · aggregate OI commitments     │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                     CipherClock funding                      │
│  reveal · correct · measure · eligibility snapshot · pay     │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                 ThresholdLiq + BondedSettle                  │
│  keeper proof · bond · refund · oracle quorum · settlement   │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                     ProofOfRisk + Passport                   │
│  solvency · OI bounds · insurance · leakage graph            │
└─────────────────────────────────────────────────────────────┘
```

## Demo script (5 minutes)

**Act 1 — The leak (60s).** Show a transparent perp. A whale's size, entry, and liquidation price are visible. A bot front-runs the funding/liquidation. The judge sees the problem.

**Act 2 — The private trade (60s).** Submit the same trade to VeilPerp. The explorer shows a batch, a commitment, and aggregate OI — not the side or size. ShadeMatch clears privately.

**Act 3 — The funding attack (60s).** Turn off CipherClock: the whale's own batch moves the mark, funding is measured immediately, and the whale profits. Turn on CipherClock: correction window, eligibility snapshot, funding measured after the transient. The profit collapses.

**Act 4 — The liquidation (60s).** A keeper proves an underwater position with a threshold proof. The public sees a liquidation occurred, not whose or how large. A wrongful liquidation attempt is rejected and the bond is slashed.

**Act 5 — The passport (60s).** VeilPassport shows the phase-by-observer leakage map. The judge tries to infer the whale's position from public data. The inference fails, and the passport states exactly what remains public. ProofOfRisk shows the venue is solvent without revealing positions.

**Close:** "Privacy is not a promise. It is a measured, auditable, and economically secured information schedule."

## Team and agent split

5 humans, 10 coding agents. No 36-hour constraint.

| Workstream | Humans | Agents | Output |
|---|---|---|---|
| Protocol / circuits | 1 | 2 | Private notes, commitments, funding eligibility, solvency proof |
| Matching / MPC | 1 | 2 | Batch matching, clearing, transcript verification |
| Risk / liquidation | 1 | 2 | Margin engine, keeper proofs, bond/slashing |
| Frontend / demo | 1 | 2 | Trade UI, batch view, passport, inference attack |
| Research / narrative | 1 | 2 | Paper mapping, judge Q&A, leakage taxonomy, deck |
| Integration / QA | all | shared | End-to-end testnet, failure modes, demo rehearsal |

## Judge Q&A

**"Isn't this just ZKPerp?"**
ZKPerp is a private position system on Aleo with no dynamic funding and a trusted orchestrator. VeilPerp adds private batch matching, decryption-aware funding, threshold liquidation proofs, public risk attestation, and a leakage passport. ZKPerp is one architecture; VeilPerp is the privacy/risk stack.

**"Isn't this just Rialto/P2DEX?"**
Rialto and P2DEX are spot, one-shot matching systems. They do not handle perpetual funding, margin, or liquidation. We borrow their matching and insured-MPC patterns and add the perp-specific risk layer.

**"How can you prove solvency without revealing positions?"**
We prove aggregate bounds and coverage, not individual positions. The proof shows that total liabilities are within the insurance fund and OI limits under a stress scenario. It is a ZK/attested aggregate proof, not a full audit of every position.

**"Who can liquidate?"**
A threshold keeper set with proofs of underwaterness. The set is a trust assumption; the passport states it. A fully permissionless private liquidation design is open research, and we do not claim it.

**"What leaks?"**
Batch membership, aggregate OI changes, oracle prices, settlement activity, and timing. VeilPassport enumerates these per observer and phase. We do not claim end-to-end privacy.

**"Why would institutions use this?"**
They can trade size without telegraphing, keep positions private from competitors, and give LPs a solvency attestation. The compliance layer can prove allowlist membership without publishing identity.

**"What is the moat?"**
The composition, the leakage dataset/passport standard, the funding rule, and the risk-proof circuits. The cryptography is available; the integration and evidence are the moat.

**"Is it audited?"**
No. It is a testnet/research prototype. We state the trust assumptions and the open problems.

## What to avoid

- "Fully private perps."
- "Trustless liquidation."
- "Permissionless private liquidation."
- "Production-ready."
- "The passport proves privacy."
- "The funding model is calibrated to a real venue."
- "MPC/ZK alone guarantees privacy."

## Final positioning

The clean-sweep story is:

> **Private perps fail at the seams.** Encrypted orders leak through funding; private positions leak through OI and liquidation; private matching leaks through rank and settlement; private settlement needs bonds and refunds. The answer is not another private DEX. It is a perp with **sealed books and public invariants**: a Sealed-Book Solvency Envelope over hidden positions, a manipulation-immunity certificate for funding, an adversary-equivalence passport for privacy claims, and challengeable clearing with privacy-loss bonds. Hide what must be hidden; prove what must be safe.

That is a product, a research contribution, and a demo in one.
