# SoK: Privacy-Preserving Order Matching

## Metadata

- **Authors:** Prerna Arote (IMDEA Networks Institute), Adrian Saiz (EPFL), Lucianna Kiffer (IMDEA Networks Institute)
- **Date:** 8 October 2026
- **Identifier:** arXiv:2610.12535v1 [cs.CR]
- **Length:** 39 pages
- **Type:** Systematization of knowledge. No new protocol; a taxonomy and comparative analysis.
- **Read status:** Full main text and phase-by-phase findings read; protocol tables and appendix descriptions sampled.

## One-paragraph summary

This SoK systematizes **privacy-preserving order matching** across 22 protocol families, 13 for financial instruments and 9 for blockchain-native assets. It organizes the order lifecycle into four phases — order representation/submission, discovery/collection, matching, and settlement — and characterizes each phase by what information is protected or exposed, which role can access it, the privacy mechanism, and the trust assumption. The central finding is negative and important: **privacy is not a single protocol-level property**. Protecting order attributes or one phase does not prevent leakage elsewhere. Order existence is exposed in 21 of 22 protocols, order side is the most frequently exposed attribute, matching outputs are often opened in plaintext, and settlement frequently makes trades public. The paper identifies recurring trade-offs among privacy, functionality, trust, and verifiability, and proposes phase-, observer-, and information-specific claims as the only honest way to describe privacy.

## Why order matching is a privacy problem

An executable order reveals:

- **Direction** (buy/sell).
- **Instrument** (asset pair).
- **Price** (limit or market).
- **Quantity**.
- **Timing**.

On a lit exchange, resting orders are visible before execution. Dark pools hide orders from the market but not from the operator, who must see them to match. SEC enforcement actions illustrate the operator-trust problem: proprietary desks used live subscriber order feeds, order information was shared with third parties, and internal access was not restricted.

Cryptography can reduce operator trust, but it moves trust rather than eliminating it:

- **MPC** → threshold of non-colluding parties.
- **HE/FHE** → cryptographic assumptions plus a decryptor.
- **TEE** → hardware vendor and attestation.
- **DP** → a privacy budget and noise.
- **Commitments/ZKPs** → binding/hiding and proof assumptions, often with a trusted setup.
- **Centralized operator** → contractual/legal trust.

## Model

### Order

An order is:

```text
o = (u, a_in, a_out, q, p, aux)
```

where `u` is the trader, `a_in`/`a_out` are the assets exchanged, `q` is quantity, `p` is price/price constraint, and `aux` is optional auxiliary information. Everything except `u` is an **order attribute**.

Two orders are compatible when their constraints permit a common execution, e.g. a buy with maximum `p_b` and a sell with minimum `p_s` are compatible when `p_b >= p_s`.

### Roles

1. **Trader** — creates and submits orders.
2. **Protocol participant** — participates in the protocol/computation, not necessarily entrusted with private order information.
3. **Trusted intermediary** — a participant entrusted with a function involving private order information (relayer, broker, operator).
4. **Settlement mechanism** — executes/records matched trades.
5. **Other observer** — gathers information without participating.

Roles are functional, not mutually exclusive. A protocol participant becomes a trusted intermediary when a trader delegates a private-order function to it.

### Information categories

- **Order existence** — whether a trader submitted an order.
- **Trader–order linkage** — whether an order can be associated with its trader.
- **Order attributes** — pair/side, price, quantity, auxiliary fields.
- **Matching information** — compatibility, candidate existence, selected orders, allocation.
- **Settlement information** — trade details, resulting balances/positions, activity/timing.

Privacy is relative to **both the observer and the phase**. A protocol may hide an attribute from one party while revealing it to another, or protect it before matching while exposing it at settlement.

### Four-phase lifecycle

| Phase | Question | What the SoK records |
|---|---|---|
| 1. Representation and submission | How is the order represented and introduced? | Commitment object, mechanism, location, holder, submission representation, pre-discovery privacy |
| 2. Discovery and collection | How are orders/counterparties identified and collected? | Participation, collection mechanism, timing, exposure |
| 3. Matching | How are compatible executions determined and allocated? | Scope, policy, mechanism, trust assumption, inputs/outputs, no-match disclosure |
| 4. Settlement | How is the match converted into an asset/state transition? | Method, verification, bindingness, privacy |

### Mechanisms and trust assumptions

- **MPC** — joint computation without revealing inputs; security usually threshold.
- **HE/FHE** — compute over ciphertexts; high cost; decryptor trust.
- **Commitments** — hiding + binding; bind but do not hide by themselves.
- **ZKPs** — prove validity without revealing witness; verifiability and selective disclosure, not private computation alone.
- **DP** — bounds output leakage; noise/dummy orders.
- **TEE** — hardware isolation; trust in vendor/firmware/attestation.
- **Trust categories** — cryptographic, threshold, hardware, centralized, hybrid.

## Phase 1: Order representation and submission

### Findings

- **Encrypted state is the most common commitment mechanism** (9 protocols), followed by secret-shared state (8) and cryptographic commitments (5).
- **Most commitments are off-chain** (15 of 22). On-chain storage does not make orders public: only commitments, hashes, or ciphertexts are posted.
- **Commitments bind but do not hide.** In CryptSec, BlockAuction, and Renegade, the operator/relayer still sees orders in plaintext.
- **Confidential representation does not imply confidential submission.** All protocols except KickBucket expose order existence; KickBucket uses dummy orders.
- **Order side (pair) is the most exposed attribute**, often while price and quantity stay hidden.
- Half of the protocols submit directly to protocol participants, seven via a trusted intermediary, and only PrivacyDP, BlockAuction, and COMMON leave orders with the trader.
- Six protocols commit to state beyond the order, such as wallet or balance state.

### Implication for perps

A private perp position is not just an order. It has **persistent state**: size, entry, collateral, leverage, funding accrual, and liquidation level. The SoK's phase-1 lens asks what is committed and who holds it. A perp design that hides the opening order but publishes a position commitment may still leak existence, timing, and side.

## Phase 2: Discovery and collection

### Findings

- **Collection is centralized in 12 protocols and distributed in 10.**
- **Continuous discovery is most common** (12), supporting price–time priority but revealing arrival time.
- **Time-based batching** appears in 8 protocols, coarsening arrival to batch membership at the cost of latency.
- **Event-triggered discovery** appears only in blockchain-native protocols.
- **Order existence is exposed during discovery in 19 of 22**; KickBucket is the only one explicitly hiding it with dummy orders.
- **Order pair is the most frequently exposed attribute.**
- **Compatible candidates are revealed in only 3 protocols**: Indiff-Priv and DeepOcean (operator selects candidate pairs) and Renegade (traders discover each other through handshakes). In DeepOcean and Renegade, a counterparty learns of a compatible order before its value is compared.
- Oblivious MPC-based selection (MPCJoins, KickBucket, P2DEX) avoids revealing compatible candidates before matching.

### Implication for perps

Discovery is where **liquidation hunting** and **copy trading** begin. If a perp venue reveals that a large position exists, even without size, it reveals a candidate for adversarial flow. Batch discovery hides arrival time but reveals batch membership; continuous discovery supports priority but leaks timing.

## Phase 3: Order matching

### Findings

- **Multi-order matching dominates** (16 of 22), most often priority-based; five of six volume-maximizing protocols batch by time.
- **Pairwise matching** is used by three blockchain-native protocols; it simplifies private computation but provides no global allocation.
- **MPC is the dominant mechanism** (14 protocols), usually with a threshold assumption. FutureMEX and Renegade rely on cryptographic assumptions alone because each party contributes its own order.
- **The mechanism does not determine what is hidden.** Several protocols apply cryptography to only part of matching and do the rest in plaintext:
  - Rialto sorts rates in MPC, then a smart contract matches the sorted list.
  - COMMON decrypts homomorphically aggregated amounts before matching in the clear.
  - DeepOcean protects only the size comparison between two traders.
  - CryptSec and BlockAuction decrypt orders and match in plaintext; cryptography serves verification.
- **Matching output is usually opened for execution:** 15 protocols deliver it at least partly in plaintext; Block Auction publishes it to all observers.
- **Counterparty identity is revealed in 8 protocols.**
- **No-match disclosure:** 9 protocols disclose that specific orders did not match; 8 disclose no explicit no-match result.
- **Matching leaks beyond intended output:** total rate ordering in Rialto, relative limit ordering in SPCEX, comparison results in PETExchange, and which of two orders is larger in DeepOcean.

### Implication for perps

Matching output is not the end. A perp matching engine also needs to know **who bears the risk** after the match. If the output reveals counterparties, size, or clearing price, the subsequent funding and liquidation phases inherit that leakage. A "private matching" claim says nothing about the private position after the trade.

## Phase 4: Settlement

### Findings

- **Settlement is where the two contexts diverge most.** Every blockchain-native protocol settles on-chain; 10 of 13 financial-market protocols leave settlement unspecified, so their privacy guarantees end with the matching output.
- Only CryptSec and FutureMEX settle internally; Block Auction is the only financial-market protocol that settles on-chain.
- **Cryptographic verification is used by 11 of 12 protocols that specify settlement**, sometimes combined with MPC.
- **Bindingness varies:** four protocols are binding, five non-binding. Non-binding designs rely on economic incentives (margin forfeiture, bonds). Binding designs still need refund paths for aborts/unmatched orders.
- **Settlement shifts privacy toward the public:** in 8 of 12 protocols that specify settlement, settlement information is accessible to all observers; settlement activity is exposed in 8.
- **Singularity is the only protocol that hides trade information, trader–trade linkage, and settlement state on-chain**, publishing only nullifiers and new note commitments.
- Partial mitigations target linkage rather than activity: Rialto opens only the aggregate fee across matched pairs; COMMON reveals pair and round but not individual amounts; SPCEX exposes escrow transfers but mixes them to unlink traders.
- **Even protocols that hide orders throughout matching reveal that a settlement took place.**

### Implication for perps

Settlement is not a one-time event in perps. Funding payments, liquidations, and PnL realization are repeated settlement events. If each one reveals activity or links to a position, the "private" position becomes a public time series.

## Cross-phase findings

### 1. Privacy is phase- and information-dependent

No protocol uniformly protects an order from submission through settlement. Information protected in one phase is often exposed later:

- 15 protocols expose matching outputs at least partly in plaintext.
- 8 of 12 protocols that specify settlement expose settlement information publicly.
- Order existence is exposed in all but one protocol.
- Order side is the most exposed attribute.
- Settlement can expose activity or enable linkage even when order contents remain hidden.

### 2. "Private matching" is not end-to-end privacy

A protocol can hide price and quantity during matching and still leak:

- candidate compatibility before matching,
- relative rate ordering,
- counterparty identity,
- the fact that a settlement occurred,
- the resulting state or balance change.

### 3. Privacy–functionality trade-off

Pairwise matching simplifies private computation but provides no global allocation. Multi-order matching enables better allocation but requires more complex joint computation. Continuous discovery enables price–time priority but reveals arrival times; batching coarsens timing at the cost of latency.

### 4. Privacy–trust trade-off

Every mechanism shifts trust:

- MPC → threshold of non-colluding parties.
- TEE → hardware vendor/firmware.
- Trusted intermediary → operator honesty.
- Commitments/ZKPs → cryptographic assumptions and setup.

Seven protocols use a trusted intermediary; three of those receive orders in plaintext. Where each party contributes only its own input (FutureMEX, Renegade), privacy can rest on cryptographic assumptions alone.

### 5. Privacy–verifiability trade-off

Verification typically requires disclosure. On-chain settlement is publicly checkable precisely because it exposes execution. Protocols that limit exposure decide what to reveal:

- Rialto opens only the aggregate fee.
- COMMON reveals pair and round but not amounts.
- Singularity hides trade, linkage, and settlement state, but publishes nullifiers and commitments.

### 6. The reporting gap

Settlement information is unspecified for 10 of 13 financial-market protocols. The SoK argues for a standardized framework covering:

- protected information,
- observer,
- phase,
- representation,
- trust assumption.

Without that, privacy claims are not comparable.

## Paper-specific findings for privacy x perpetuals

1. **Perps need a five-phase privacy map, not four.** The SoK's four phases cover order matching, but perps add continuous **position, funding, liquidation, and oracle** phases. A private order can become a public position through OI, funding, or liquidation.
2. **Existence is the hardest secret to keep.** If a perp venue reveals that a position exists, an adversary can watch funding payments, oracle moves, and liquidation timing to infer the rest. Dummy orders/positions are one option; they are expensive and can distort risk.
3. **Side is the most leaky attribute.** Hiding size and price while revealing long/short is often enough to front-run funding or hunt liquidations.
4. **Matching output is not the privacy boundary.** The post-trade position, funding accrual, and liquidation path are part of the same information lifecycle.
5. **Settlement activity is a metadata leak.** Even a fully encrypted perp reveals that someone opened, funded, or closed. Timing and frequency can be linked across rounds.
6. **Verifiability requires selective disclosure.** A perp cannot prove solvency, OI, or liquidation correctness without revealing something. The design question is *what* to reveal, to *whom*, in *which phase*.
7. **Trust is moved, not removed.** A private perp with an operator, keeper set, oracle quorum, or compliance server has a trust surface. The SoK's role taxonomy makes that explicit.
8. **Privacy claims should be per-observer.** "Private" against the public is different from "private" against the operator, the keeper, the oracle, or the compliance server.

## Limitations of the SoK

- **Claims are not independently validated.** The authors record what protocols claim; they do not audit implementations. Renegade is cited as an example whose claimed guarantees failed under closer analysis.
- **Corpus scope.** 22 protocol families; selection is keyword/citation-based. Some protocols may be omitted. Financial-market and blockchain-native contexts are not exhaustive.
- **No perps, funding, or liquidation.** The taxonomy is about order matching, not perpetual-specific risk. Its phases stop at settlement; perps continue.
- **"Not specified" is not a guarantee.** Tables mark unspecified properties with "-"; this is not evidence of privacy or leakage.
- **No quantitative leakage metric.** The SoK compares qualitative categories, not measured information leakage. It does not compute a privacy score.
- **No transport/account privacy.** The scope excludes privacy mechanisms that protect only transaction transport, account state, or post-matching settlement outside the matching process.
- **Mechanism ≠ property.** A protocol using MPC may still reveal outputs; a protocol using ZKPs may still publish plaintext settlement.
- **No economic analysis.** It does not model front-running profitability, liquidation hunting, or funding manipulation.
- **No implementation or performance comparison.** It is a taxonomy, not a benchmark.

## Hackathon preparation

### What this paper supports building

- A **lifecycle leakage scanner** for perp DEXs: input contracts, docs, and observed transactions; output a phase-by-observer leakage graph.
- A **privacy passport/badge**: standardized claims such as "order existence hidden from public until batch close; size hidden from keeper; settlement activity public."
- A **design checklist** for a private perp: what is protected, from whom, in which phase, under what trust assumption.
- A **leakage explorer**: show how a single public field (side, OI, funding payment, liquidation) can be combined with timing to reconstruct a position.
- A **judge-friendly comparison** of existing perp DEXs (dYdX, GMX, Hyperliquid, ZKPerp-style) along the SoK's dimensions.

### What this paper does not support building directly

- A full private matching engine.
- A perp risk engine.
- A quantitative privacy guarantee.
- A claim that a protocol is private end-to-end without implementation analysis.

### Demo script (3 minutes)

1. Pick three perp venues. Show their public fields: position size, entry, liquidation price, OI, funding.
2. Run the leakage scanner. Show that one venue hides order contents but leaks side and settlement activity; another hides size but reveals OI and liquidation clusters.
3. Overlay a simulated attack: use only the leaked fields to infer a large position and front-run its funding/liquidation.
4. Show the Privacy Passport: a phase-by-observer table with explicit claims and trust assumptions.
5. Apply the checklist to a new private perp design and show the remaining leaks.

### Judge Q&A prep

- **"Isn't this just a survey?"** The product is the operational leakage scanner and passport, not the survey. The SoK supplies the taxonomy and the evidence that phase-level claims do not compose.
- **"Can you prove a venue is private?"** No. We can prove what it exposes in each phase and under which trust assumption. End-to-end privacy requires implementation analysis and, ideally, formal guarantees.
- **"Why perps?"** Perps add funding, liquidation, and persistent positions, so the order-matching lifecycle is only the beginning. That is where the most valuable leakage occurs.
- **"What is novel?"** A perp-specific extension of the SoK's four phases with funding, liquidation, oracle, and position state, plus a scanner that turns the taxonomy into an artifact.
- **"Is the score objective?"** No. It is a structured disclosure map, not a universal privacy ranking. We show the assumptions.

### Claims to avoid

- "This venue is fully private."
- "MPC guarantees privacy."
- "ZKPs guarantee privacy."
- "Encrypted orders mean end-to-end privacy."
- "Our score is a formal privacy metric."

## Hackathon-level idea: VeilMap

**One-liner:** A lifecycle leakage scanner and privacy passport for perpetual DEXs.

**User:** Perp DEX teams, institutional traders, auditors, and privacy-conscious LPs.

**Core flow:**

1. **Ingest:** contract ABIs/source, docs, public state, and sample transactions for a perp venue.
2. **Classify:** map each observable to the SoK's information categories and lifecycle phases, extended with funding, liquidation, and oracle phases.
3. **Attribute:** for each observable, record the observer role (public, operator, keeper, oracle, compliance server, counterparty) and the trust assumption.
4. **Infer:** run a leakage graph that shows how combining fields (side + OI + timing + funding) can reconstruct a position.
5. **Score:** produce a phase-by-observer passport, not a single "privacy score"; optionally show a scenario-based risk score.
6. **Attest:** publish the passport hash on-chain and let the venue make verifiable claims about specific protections.
7. **Recommend:** suggest concrete fixes: batch discovery, eligibility snapshots, decoy positions, threshold keepers, delayed settlement, aggregate-only disclosure.

**Demo moment:** A venue claims "private perps." VeilMap shows that its public OI plus funding payments plus liquidation timing reveal a whale's direction and approximate size. Then it shows the same venue with batching and delayed funding, and the inference collapses.

**Moat:** The perp-specific leakage graph, the cross-venue dataset, and the passport standard.

**Cons:**

- The scanner is only as good as the public data and docs it ingests.
- Inferring leakage is probabilistic; false positives can unfairly brand a venue.
- A passport can be gamed by a venue that hides fields but leaks them off-chain.
- The product is an audit/analytics tool, not a trading venue.
- Judges may see it as a dashboard unless the inference demo is strong.
- It overlaps with VeilRisk (paper 2); the two should be merged into one privacy/risk layer in the final portfolio.

**Integration with the existing portfolio:** VeilMap is the disclosure layer for Perp Forge and the privacy counterpart to PerpScore. It tells users and LPs what a venue actually hides, while CipherClock protects funding and VeilRisk attests solvency.
