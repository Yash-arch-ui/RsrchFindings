# Crazy Flagship Ideas: The Perp Safety Grid

> **Mandate.** Read all seven papers, ignore the 36-hour clock, and design flagship-level products that can clean-sweep ETHGlobal. This file is the new flagship slate. It is deliberately more ambitious than the earlier idea lab and the earlier clean-sweep memo. It contains ten full flagship concepts, three bonus concepts, a scoring matrix, a recommended build portfolio, a team/agent split, a six-act demo, and an honest "cons" section for every idea.

**Operating assumptions**

- 4 humans and 4-6 coding agents by default; the plan scales to 5 humans and 10 agents without changing the architecture.
- No time-boxing. Build order is dependency-ordered, not hour-ordered.
- Public data and testnet contracts only. No custody, no live funds, no investment advice.
- Every idea must be understandable in one sentence, demonstrable in under six minutes, technically real, and useful after the hackathon.
- The target is not a sponsor-track win. The target is the general competition and the "this should exist" reaction.

---

## 0. The thesis in one paragraph

The seven papers describe the same missing layer from seven angles. Perpetual futures are the largest crypto derivative market, yet the market has no **systemic risk sensor**, no **independent design court**, no **tradeable risk-factor market**, no **liquidation-tail clearinghouse**, no **private risk passport**, and no **enforceable market constitution**. Every venue copies a funding template; every trader guesses at liquidation risk; every lender treats perp collateral as spot; every insurer prices a tail it cannot measure. The winning project is not another perp DEX. It is the **safety grid for perpetual markets**: sense the cascade, certify the design, launch the safe market, and transfer the tail. In one line:

> **"We built the missing safety infrastructure for the $90T perpetual market: a nervous system, a court, and an insurance market."**

---

## 1. What this slate adds beyond the earlier files

The earlier files built a strong "rate, launch, insure" story around PerpScore, Perp Forge, and PerpShield. This slate adds five things those files do not have:

1. **A real-time systemic sensor** that links venues, funding, basis, marks, depth, and liquidations into one cascade-risk index.
2. **An adversarial certification protocol** that puts a perp design on trial, replays exploits, and bonds the verdict.
3. **A self-tuning market OS** where funding, margin, and mark policy adapt inside pre-committed risk bounds.
4. **A market for perp risk factors** so funding risk, basis risk, mark risk, and liquidation risk become tradeable and hedgeable.
5. **A privacy-preserving underwriting layer** so risk can be verified without exposing positions.

The original three modules remain good. This slate is what the team should build if the goal is not just to win a track, but to create a category that judges remember after the event.

---

## 2. Paper-to-superpower map

| Paper | Core result | Superpower it unlocks | Used by |
|---|---|---|---|
| 1. Angeris et al. (2022) | Any smooth payoff `phi` has funding `F = 1/2 sum (sigma sigma^T)_ij S_i S_j d_i d_j phi - (phi - S . grad phi) r`; discount `D = F/phi`; jump funding integral | Turn any payoff into a complete perp spec, including hedge ratios and jump premium | Perp OS, Perp Genome, Perp Markets, Perp Court |
| 2. He et al. (2024) | Random-maturity no-arbitrage price `F = (1 - Phi^{-1}(r))^{-1} S`; clamp-aware Binance benchmark; mean absolute deviation 52-90% per year | Detect when a perp is outside its fair-value band and when the deviation is a tradable dislocation | Perp Nervous System, Perp Markets, Perp Safe, Perp Court |
| 3. Alexander et al. (2023) | Dual-objective hedge `theta* = omega theta_0`; liquidation probability cut to 0.1-3%; optimal leverage mostly below 5x | Size a hedge or a position so the hedge survives the crash it is meant to hedge | Perp OS, Perp CDX, Perp Safe, Perp Passport, Perp Court |
| 4. He, Wang, Zhang (2026) | Capital-dependent stability band; weak feedback persists, intermediate stabilises, strong feedback creates expanding reversals; liquidation reduces corrective capacity | Measure effective funding feedback, not the printed template, and design rules that the market can absorb | Perp Genome, Perp OS, Perp Nervous System, Perp Constitution |
| 5. Zhivkov (2026) | Two-tier CEX/DEX market; 17% of observations show >=20 bps spreads; only 8/20 top trades profitable after costs; average Sharpe -7.40; CEX leads | Price execution realistically, model reversal hazard, and route only when net expected value survives costs | Perp Nervous System, Perp Safe, Perp Court |
| 6. Erez and Smirnov (2026) | Funding timing determines bound testability; Drift observable-at-entry yields 3-5% out-of-band exits; dYdX caps never bind and the bound is vacuous | Audit whether a funding rule is actually testable, and decouple caps from margin parameters | Perp Court, Perp Constitution, Perp Genome, Perp OS |
| 7. Pindza and Mashele (2026) | Fixed-delay overshoot `sigma sqrt(delta/(2 pi))`; bad-debt tail `Phi(log(1-m)/(sigma sqrt(delta)))`; TWAP crossing overshoot ~1.6x fixed delay; breach 0.07-0.23 at 10x, 0.39-0.56 at 20x, 0.76-0.83 at 50x | Forecast hidden liquidation loss, score mark quality, and price the tail that venues do not disclose | Perp CDX, Perp Nervous System, Perp OS, Perp Safe, Perp Passport, Perp Court |

---

## 3. Evaluation rubric

Every idea is scored from 1 to 5 on:

| Criterion | What a 5 means |
|---|---|
| Novelty | A new primitive, not a clone of an existing dashboard or DEX |
| Technical depth | Real math, real data, real contracts, verifiable results |
| Demo | A judge can see the thing work and change behavior in under six minutes |
| Feasibility | 4 humans and 4-6 agents can build a credible version without hiding the hard parts |
| Judge appeal | The problem is already believed, and the one-sentence pitch is memorable |
| Moat | Data, methodology, integrations, or a standard that compounds |
| Public good | Other teams, venues, lenders, or regulators can build on it |

A score of 35/35 is not a claim of certainty. It means the idea is designed against the criteria that general-competition judges actually reward.

---

## 4. The ten flagship ideas

### 4.1 PERP NERVOUS SYSTEM

> **One sentence.** A real-time sensor network and cascade oracle for the entire perp market that detects systemic stress before liquidation and lets protocols de-risk automatically.

**The crazy bet.** Build the "nervous system" for perps: not a dashboard, but an onchain fire alarm with financial consequences. The system senses funding stress, basis dislocation, mark latency, depth thinning, and liquidation clustering across venues, then publishes a cascade-risk index that lending protocols, perp DEXs, DAOs, and insurers consume. It is the first systemic-risk primitive for the largest crypto derivative market.

**Problem and buyer.** A lender cannot see that a perp venue's collateral is one funding spike away from a cascade. A DAO cannot see that its restaking hedge is correlated with the venue it uses. A perp DEX cannot see that its own funding rule is amplifying a move. A regulator has no index to watch. The buyer is every protocol that touches perp risk: lenders, insurers, vaults, DAOs, market makers, and venues that want to prove they are safe.

**Paper spine.**

| Paper | What the system takes |
|---|---|
| 2 | Clamp-aware basis band and `rho` deviation; deviations comove across assets |
| 3 | Liquidation probability as a survival object, not a price level; hedge effectiveness |
| 4 | Effective feedback regimes and the corrective-capacity feedback loop |
| 5 | CEX-to-DEX transmission, executable spread, reversal hazard, venue tiering |
| 6 | Funding timing and cap-binding risk; a bound can be vacuous |
| 7 | Mark delay, TWAP penalty, depth concession, funding memory, bad-debt tail |

**The primitive.** A `CascadeRiskIndex` (CRI) with venue-level subscores:

```
CRI = f(funding stress, basis dislocation, mark quality, depth, liquidation clustering,
        cross-venue correlation, funding timing, corrective-capacity proxy)
```

Each subscore has a confidence interval and a methodology hash. The index is not a price forecast. It is a conditional risk state: "given the current funding, basis, mark, and depth, how likely is a cascade over the next 24-72 hours, and which venues are the transmission nodes?"

**Full workflow.**

1. **Data plane.** Ingest one-minute funding, mark, index, mid, open interest, depth snapshots, long/short liquidations, stablecoin flows, and bridge/withdrawal status from CEXs and DEXs. Normalize funding to an 8-hour equivalent. Tag every field with source, timestamp, and missingness.
2. **Entity graph.** Build a graph of assets, venues, collateral assets, bridges, and stablecoins. Estimate edges from funding correlation, basis lead-lag, price-impact correlation, and shared collateral. The graph is a model, not a fact; publish edge confidence.
3. **State estimation.** Estimate a latent stress state per venue: arbitrage capital `K_t`, effective funding feedback `kappa_t`, basis `b_t`, mark delay `delta`, depth utilization `z/D`, and liquidation intensity. Use a state-space model or a calibrated agent-based simulator.
4. **Cascade engine.** Run a scenario ensemble: funding shock, spot drawdown, stablecoin depeg, oracle delay, exchange outage, and cross-venue collateral spiral. Each scenario produces a distribution of liquidation volume, bad debt, and venue impairment.
5. **Index and alerts.** Publish `CRI`, `VenueStressScore`, `CascadeProbability24h/72h`, and `TransmissionRank`. Alerts are signed reports with inputs, model version, and a dispute window.
6. **Consumer adapters.** A lending protocol lowers collateral factors when `VenueStressScore` crosses a threshold. A perp DEX raises maintenance margin or pauses new listings. A DAO de-risks a treasury. An insurer re-prices. A vault exits a basis trade.
7. **Stress bonds.** Venues and consumers can post a bond against a certified risk bound. If the venue's realized stress exceeds the bound and the alert was valid, the bond pays affected users or the insurance fund.
8. **Replay and calibration.** Backtest the index on historical cascades and synthetic paths. Publish lead time, false-positive rate, and false-negative rate. The system is only credible if it can show what it would have said before a known event.
9. **Governance.** A methodology registry, a multisig or optimistic oracle for updates, and an emergency pause. The index must be hard to manipulate but fast enough to matter.

**Architecture.**

```
CEX/DEX APIs, onchain indexers, depth feeds, liquidation feeds
                    |
              Data lake + normalizer
                    |
        Entity graph + state estimator
                    |
   Cascade simulator (historical + synthetic + adversarial)
                    |
     CRI engine + venue subscores + confidence
                    |
   Signed reports -> onchain oracles -> alert bus
                    |
  Lending adapter | Perp DEX adapter | DAO vault | Insurer
                    |
             StressBond + CircuitBreaker
```

**Onchain contracts.**

- `CascadeOracle.sol`: stores signed `CRI`, `VenueStressScore`, timestamps, and methodology hash.
- `StressIndex.sol`: computes a composite from oracle inputs; supports multiple index versions.
- `VenueRiskRegistry.sol`: maps venue, asset, and contract type to risk parameters and disclosure status.
- `AlertBus.sol`: publishes threshold events; consumers subscribe.
- `StressBond.sol`: bond terms, slashing conditions, and payout waterfall.
- `CircuitBreakerAdapter.sol`: a standard interface a lending protocol or perp DEX can call.
- `MethodologyRegistry.sol`: versioned formulas, parameter changes, and dispute records.
- `DisputeManager.sol`: optimistic dispute with escalation.

**Offchain services.**

- Data ingestion and normalization; a time-series store.
- Graph construction and edge-confidence estimation.
- State-space or agent-based cascade simulator.
- Scenario library and stress-test runner.
- Signed-report keeper with deterministic reproducibility.
- Backtest and monitoring dashboard.

**Demo (six acts).**

1. **Calm.** Live board shows six venues, their subscores, and a low CRI. The judge sees this is not a static chart: values move with live funding and basis.
2. **Shock.** A scripted BTC drawdown and funding spike hit the data plane. The CRI rises before the simulated liquidations. The transmission graph lights up the venue with the thinnest depth and the slowest mark.
3. **Action.** A mock lending protocol reads the oracle and lowers the collateral factor for the stressed venue. A mock vault closes a basis trade. A venue's stress bond enters "at risk."
4. **Cascade.** The scenario engine runs the cascade. The naive protocol that ignored the index is liquidated; the adapter-protected protocol survives. The judge sees a before/after, not a chart.
5. **Proof.** The signed report is onchain. The judge can click a subscore and see the raw inputs, formula, and confidence.
6. **Replay.** Show a historical or synthetic episode where the index rose before the cascade. State the false-positive and false-negative rates honestly.

**Build order.**

1. Data normalizer and one-venue, one-asset state estimator.
2. Basis, funding, and liquidation subscores from Papers 2, 4, and 5.
3. Mark-quality and depth subscores from Paper 7.
4. Cascade simulator and scenario library.
5. CRI aggregation, confidence intervals, and backtest.
6. Signed-report oracle and consumer adapters.
7. Stress bond and dispute flow.
8. Multi-venue graph and transmission ranking.
9. Demo script and historical replay.
10. Security review, invariant tests, and open-source release.

**Team and agent split.**

- Human 1: quant lead, owns state estimation, cascade model, and index weights.
- Human 2: protocol lead, owns oracles, alert bus, stress bond, and adapters.
- Human 3: data lead, owns ingestion, entity graph, and backtests.
- Human 4: frontend and narrative lead, owns the live board and six-act demo.
- Agent 1: CEX/DEX data ingestion and normalization.
- Agent 2: basis/funding subscores (Papers 2 and 4).
- Agent 3: liquidation and mark/depth subscores (Papers 3 and 7).
- Agent 4: cascade simulator and scenario library.
- Agent 5: Solidity oracles, adapters, and tests.
- Agent 6: frontend charts, replay, and documentation.

**Judge Q&A.**

- **"Isn't this just a dashboard?"** No. The dashboard is the UI. The product is an onchain oracle and alert bus that changes collateral factors, margin requirements, and bond payouts.
- **"How do you know the cascade model is right?"** We do not claim certainty. We publish lead time, false-positive rate, false-negative rate, and confidence intervals, and we show a historical replay.
- **"Can a whale trigger an alert?"** The index uses multi-source data, medians, time weighting, and a dispute window; a single venue or print cannot move it. We show the manipulation cost.
- **"Why would venues participate?"** Lower insurance premiums, better lending terms, and a credible safety signal. Venues that refuse are visible by their absence.
- **"What is the moat?"** The cross-venue graph, the calibrated cascade model, the historical replay, and the integrations.

**Cons (hard truths).**

- **False alarms are expensive.** A false positive can force a protocol to de-risk at the worst price, creating the very cascade it fears. The threshold design is a product risk, not a detail.
- **Missed cascades are worse.** A false negative destroys trust. The system needs a clear "unknown" state, not a false sense of safety.
- **The cross-venue graph is unobservable.** Correlations are not exposures. The graph can be wrong in exactly the stress regime where it matters.
- **Data quality is the bottleneck.** Exchange APIs are incomplete, delayed, or manipulated; liquidation data is often wrong. The index can inherit those errors.
- **Liability.** If a protocol loses money after following an alert, who is responsible? The hackathon version must be advisory, with explicit disclaimers and no custody.
- **Oracle manipulation.** An attacker may try to trigger a stress bond or a circuit breaker. Multi-source aggregation, commit-reveal, and a dispute window are mandatory.
- **Adoption cold start.** Consumers need a reason to integrate before the index has a track record. A single strong integration is better than ten mock ones.
- **Demo risk.** A simulated cascade can look fake. Use a historical replay or a deterministic scenario with real data, and label it clearly.
- **"Systemic risk" is a crowded narrative.** The differentiator is the executable adapter and the bond, not the chart.

**Kill criteria.**

- No historical replay showing useful lead time.
- The index cannot produce a stable value under minor data perturbations.
- No consumer contract that changes behavior.
- The team cannot explain the difference between a risk state and a price prediction.

**Score.** Novelty 5, technical depth 5, demo 5, feasibility 4, judge appeal 5, moat 5, public good 5. **34/35.**

---

### 4.2 PERP COURT

> **One sentence.** An adversarial simulation court that puts a perp design on trial, replays exploits against it, certifies what survives, and bonds the verdict onchain.

**The crazy bet.** Create the missing standards body for perps: an "Underwriters' Laboratory" that is adversarial, reproducible, and bonded. Anyone can file a case against a funding rule, a mark design, a margin schedule, or a liquidation engine. Independent agents attack the design in simulation. A verdict is published with evidence, confidence, and a bond. Venues that pass get a certificate that lowers their cost of capital; venues that fail pay.

**Problem and buyer.** A perp venue can claim "audited" while its mark is a slow TWAP, its funding cap never binds, and its liquidation tail is hidden. A lender or insurer cannot verify the claim. A trader cannot compare designs. A regulator cannot see the assumptions. The buyer is the venue that wants credible differentiation, the insurer that needs a standard, the lender that needs a risk opinion, and the user who needs recourse.

**Paper spine.**

| Paper | What the court tests |
|---|---|
| 1 | Does the payoff/funding spec match the implementation? Are hedge ratios and jump terms coherent? |
| 2 | Is the no-arbitrage band computed correctly? Does the clamp change the conclusion? |
| 3 | Does the hedge survive? What is the liquidation probability under stress? |
| 4 | Is the effective feedback inside the stability band, or does the template create expanding reversals? |
| 5 | Are execution costs and reversal hazard included, or is the backtest fantasy? |
| 6 | Is the funding timing observable at entry? Is the bound testable, or vacuous? |
| 7 | What is the mark quality, crossing-conditioned overshoot, depth concession, and bad-debt tail? |

**The primitive.** A `Case` with:

- **Market spec:** contracts, oracles, funding rule, mark rule, margin schedule, liquidation engine, fee schedule.
- **Claims:** precise, falsifiable statements, e.g., "the funding cap binds in stress," "the TWAP is safer than a fixed delay," "the liquidation tail at 20x is below 5%."
- **Evidence:** onchain data, CEX data, historical marks, depth snapshots, user reports.
- **Test suite:** historical scenarios, synthetic scenarios, adversarial scenarios, and model-specific tests.
- **Verdict:** pass, conditional pass, fail, or inconclusive, with a per-test breakdown, confidence, and model-risk disclosure.
- **Certificate:** scope, expiry, methodology hash, and bond.
- **Bond:** venue bond against the certified claims; challenger bond against frivolous cases.

**Full workflow.**

1. **Case intake.** The plaintiff (or the court itself) submits a market and a claim. The court checks that the claim is falsifiable and that the data are sufficient. Incomplete cases are rejected, not guessed.
2. **Discovery.** Pull all public data: funding history, mark/index history, contract parameters, margin tiers, liquidation data, order-book depth, and governance changes. Hash and timestamp the evidence. Record missing data explicitly.
3. **Reconstruction.** Rebuild the market's cash flows, funding payments, mark path, and liquidation boundary. If the reconstruction cannot be validated, the case is "inconclusive," not "safe."
4. **Test suite.** Run the paper-derived tests:
   - **Funding integrity:** clamp-aware band, effective feedback, stability band, cap-binding frequency, funding-timing testability.
   - **Basis integrity:** out-of-band rate, duration, reversion after costs, reversal hazard.
   - **Liquidation survival:** breach probability at 10x/20x/50x, hedge effectiveness, liquidation probability under the venue's margin schedule.
   - **Mark quality:** fixed-delay vs TWAP crossing overshoot, stale-error distribution, mark disclosure.
   - **Depth and execution:** average concession, depth stress, liquidation size relative to book.
   - **Adversarial:** pre-settlement funding pressure, oracle manipulation, liquidity crunch, exchange outage, stablecoin depeg, governance change.
5. **Adversarial phase.** Red-team agents propose new attacks. The court runs them. A failed attack is recorded as evidence too; the goal is not to manufacture a failure.
6. **Verdict.** Each test gets pass/fail/unknown. The overall verdict is not an average: a single critical failure can fail the market. Confidence intervals and model-risk disclosures are mandatory.
7. **Bond and certificate.** The venue posts a bond. If a certified claim is violated, the bond can be slashed after a dispute window. A challenger posts a bond to file a case; frivolous cases lose it.
8. **Appeal.** An optimistic oracle or a human panel reviews disputes. Methodology upgrades are versioned; old certificates expire.
9. **Consumer registry.** Lenders, insurers, and users read the certificate. A "certified" badge is not marketing; it is a bounded, expiring claim with a bond behind it.

**Architecture.**

```
Case intake (market spec + claim + evidence)
                    |
        Evidence registry (hashes, timestamps, provenance)
                    |
   Reconstruction engine (funding, mark, margin, liquidation)
                    |
        Test suite (papers 1-7) + adversarial agents
                    |
   Verdict engine + confidence + model-risk disclosure
                    |
   Certificate registry + bond vault + dispute manager
                    |
  Lenders | Insurers | Venues | Users | Regulators
```

**Onchain contracts.**

- `CaseRegistry.sol`: case metadata, status, evidence hash, and deadlines.
- `EvidenceRegistry.sol`: immutable evidence hashes and provenance.
- `TestSuiteRegistry.sol`: versioned test definitions.
- `VerdictOracle.sol`: signed verdicts with per-test results.
- `CertificationNFT.sol`: non-transferable certificate with scope and expiry.
- `BondVault.sol`: venue and challenger bonds; slashing waterfall.
- `DisputeManager.sol`: optimistic dispute and escalation.
- `MethodologyRegistry.sol`: formulas, parameters, and changelog.

**Offchain services.**

- Data lake and reconstruction engine.
- Deterministic simulation runner.
- Red-team agent framework.
- Report generator with citations to data and formulas.
- Reproducibility package: one command re-runs a case.

**Demo (six acts).**

1. **The claim.** A venue claims its 60-minute TWAP makes liquidation safer. The court accepts the case because the claim is falsifiable.
2. **The trial.** The test suite runs. The naive fixed-time variance test says the TWAP is safer; the crossing-conditioned test says the overshoot is about 1.6x the fixed delay, and the bad-debt tail is worse above the latency-margin crossover. The judge sees the contradiction.
3. **The exploit.** A red-team agent finds a funding-timing gap: the cap never binds, so the no-arbitrage bound is vacuous. The venue's "transparency" claim fails.
4. **The verdict.** The court publishes a per-test verdict, not a vague score. The venue is "conditional" and must change its mark or its disclosure.
5. **The fix.** The team deploys a fixed design. The court re-runs the suite. The critical tests pass. A certificate is minted with a bond and an expiry.
6. **The consequence.** A mock insurer lowers the premium for the certified market; a mock lender raises the collateral factor; the uncertified venue's bond is slashed after a violation.

**Build order.**

1. Case and evidence schema.
2. Reconstruction engine for one venue and one asset.
3. Funding/basis tests from Papers 2, 4, and 6.
4. Liquidation/mark/depth tests from Papers 3 and 7.
5. Adversarial scenario library.
6. Verdict engine and certificate.
7. Bond, dispute, and appeal flow.
8. Consumer registry and mock integrations.
9. Reproducibility package and historical case studies.
10. Security review and open-source release.

**Team and agent split.**

- Human 1: chief adjudicator, owns test design and verdict logic.
- Human 2: reconstruction lead, owns data and cash-flow rebuilding.
- Human 3: protocol lead, owns certificates, bonds, and disputes.
- Human 4: narrative and demo lead, owns the trial presentation.
- Agent 1: data ingestion and evidence hashing.
- Agent 2: funding/basis tests.
- Agent 3: liquidation/mark/depth tests.
- Agent 4: red-team agent framework.
- Agent 5: Solidity and test harness.
- Agent 6: report generator and reproducibility package.

**Judge Q&A.**

- **"Isn't this just a consulting report?"** No. The verdict is onchain, the certificate is consumable by contracts, and the bond has financial consequences. The report is the evidence, not the product.
- **"Who decides what is safe?"** The test suite and the methodology are public and versioned. The court does not have arbitrary authority; it has reproducible tests and a dispute process.
- **"What if the model is wrong?"** Every verdict carries a model-risk disclosure and a confidence interval. A certificate is a bounded claim, not a guarantee.
- **"Why would a venue submit?"** Certification lowers insurance and lending costs, differentiates the venue, and reduces the risk of a post-incident bond slash. A venue that refuses is a signal.
- **"Is this legal?"** The hackathon version is a research prototype with testnet bonds. A production version needs legal review and jurisdiction-specific structuring.
- **"What is the moat?"** The test library, the historical case record, the methodology standard, and the network of consumers who require certification.

**Cons (hard truths).**

- **Subjectivity is unavoidable.** "Safe" is not a theorem. The court can reduce discretion but cannot eliminate it. The framing must be "certified against a published test suite," not "objectively safe."
- **The name "court" invites legal scrutiny.** It may be better to call it a "certification lab" or "underwriters' laboratory" in the pitch, while keeping the adversarial demo.
- **Data gaps can make a case inconclusive.** A venue can hide behind missing data. The court must treat missing data as a failure of disclosure, not as a pass.
- **Simulation is not proof.** A design can pass the suite and still fail in a new regime. The certificate must expire and the suite must evolve.
- **Adversarial agents can overfit.** A red team that knows the test suite can find theatrical exploits. The suite needs held-out scenarios.
- **Bond adequacy.** A small bond does not compensate a large loss. The bond is a commitment device, not full insurance.
- **Adoption is hard.** Venues may boycott an independent court. Start with a public-good case study and one insurer or lender that requires it.
- **Demo risk.** A trial can feel like a lecture. Keep it visual: run the test, show the failing number, show the bond slash.
- **Liability and defamation.** A public "fail" can harm a venue. The process needs notice, reply, appeal, and a clear factual basis.

**Kill criteria.**

- The test suite cannot reproduce a known historical failure.
- The verdict is not actionable by a consumer contract.
- The team cannot explain the claim boundary.
- The bond and dispute flow is a mock with no real consequence.

**Score.** Novelty 5, technical depth 5, demo 5, feasibility 4, judge appeal 5, moat 5, public good 5. **34/35.**

---

### 4.3 PERP OS

> **One sentence.** An operating system for perpetual markets: any payoff becomes a self-tuning perp with adaptive funding, dynamic margin, a mark-quality-aware oracle, and a live risk certificate.

**The crazy bet.** Most perp launchpads stop at deployment. Perp OS keeps the market alive. It compiles the payoff, calibrates the funding and margin controllers, chooses a mark design, launches the market, and then monitors and adapts within pre-committed risk bounds. It is not a static contract; it is a controlled system with a certificate that updates as the market regime changes.

**Problem and buyer.** Launching a perp is bespoke. Teams copy a funding template, pick a mark, set a margin, and hope. Paper 4 shows the same template produces different effective feedback. Paper 6 shows a rule can be theoretically valid but empirically vacuous. Paper 7 shows a mark can hide a liquidation tail. No one compiles the whole design and keeps it inside a risk budget. The buyer is a perp DEX, a structured-product desk, an RWA issuer, a DAO launching a novel market, or a protocol that wants a certified instrument without building a risk team.

**Paper spine.**

| Paper | What Perp OS does with it |
|---|---|
| 1 | Symbolically derive `F`, `D`, `Delta`, and the jump integral from the payoff |
| 2 | Compute the no-arbitrage band and the clamp-aware benchmark for the chosen rule |
| 3 | Size the hedge and set a liquidation-probability budget |
| 4 | Calibrate the funding controller to the asset's effective feedback and stability band |
| 6 | Audit funding timing and cap testability before launch |
| 7 | Design the mark and margin jointly against the liquidation tail and depth |

**The primitive.** A `PerpKernel` with five controllers:

1. **Payoff kernel:** the payoff `phi`, collateral, settlement, and oracle inputs.
2. **Funding controller:** piecewise rule, interval, cap, flat band, and anti-manipulation logic.
3. **Margin controller:** base maintenance margin plus dynamic terms driven by volatility, funding, mark delay, and depth.
4. **Mark controller:** fixed delay, TWAP, exponential, or VWAP, with a bounded maximum delay and a fallback.
5. **Risk monitor:** live breach probability, bad-debt probability, funding feedback, basis band, and depth utilization.

The `RiskCertificate` records the certified bounds: target bad-debt probability, maximum funding gap, mark delay, depth assumption, and the conditions under which the market must pause or retune.

**Full workflow.**

1. **Author.** The user writes a payoff in a constrained DSL or natural language. They choose the asset, funding preference, mark preference, margin budget, and target users.
2. **Compile.** The compiler derives `F`, `D`, `Delta`, and the jump integral. It checks that the payoff is smooth enough, that the funding is well-defined, and that the oracle inputs exist.
3. **Audit.** The system computes the no-arbitrage band, the funding-timing classification, the cap-binding risk, the stability band, the mark-quality score, and the liquidation tail at 10x/20x/50x. If the design fails, the compiler returns a fix, not a contract.
4. **Simulate.** Run historical and synthetic scenarios, including a funding spike, a liquidity crunch, a mark delay, and an oracle outage. Calibrate the controllers. Choose parameters on the Pareto frontier of capital efficiency and tail risk.
5. **Certify.** Issue a risk certificate with the test results, confidence intervals, and the exact bounds the market promises to respect.
6. **Deploy.** The factory deploys the market, oracle adapters, controllers, and certificate. The deployer posts a stress bond.
7. **Run.** The risk monitor tracks the live state. The margin controller adjusts within pre-committed bounds. The funding controller can retune only inside the certified envelope. If the market leaves the envelope, it pauses or de-risks.
8. **Govern.** Parameter changes are timelocked and bounded. A material change requires a new certificate. The old certificate expires.
9. **Upgrade.** The kernel is versioned. The market can migrate to a new kernel with a new certificate, or stay on the old one with its original bounds.

**Architecture.**

```
Payoff DSL / natural language
            |
     Symbolic compiler (Paper 1)
            |
  Design audit: basis, funding, timing, mark, tail (Papers 2,4,6,7)
            |
  Scenario simulator + controller calibration (Papers 3,4,7)
            |
     Risk certificate + stress bond
            |
     PerpOSFactory -> PerpMarket + controllers + oracles
            |
     Live risk monitor -> alerts / retune / pause
```

**Onchain contracts.**

- `PerpOSFactory.sol`: deploys a market from a certified spec.
- `PerpMarket.sol`: positions, collateral, funding, liquidation.
- `FundingController.sol`: piecewise rule with bounded updates.
- `MarginController.sol`: dynamic maintenance margin with hard caps.
- `MarkController.sol`: mark operator with maximum delay and fallback.
- `RiskMonitor.sol`: onchain checks against the certificate bounds.
- `RiskCertificate.sol`: scope, metrics, expiry, methodology hash.
- `StressBond.sol`: bond against certificate violations.
- `OracleRegistry.sol`: approved oracle adapters and fallback rules.
- `KernelRegistry.sol`: versioned kernels and migration rules.

**Offchain services.**

- DSL parser and symbolic engine.
- Scenario simulator and calibration service.
- Certificate generator and report renderer.
- Monitoring keeper and alert service.
- Governance dashboard.

**Demo (six acts).**

1. **Author.** The team types "a perp on restaking slashing risk" or "a perp on the Perp VIX." The compiler shows the payoff, funding formula, hedge ratio, and jump term.
2. **Audit.** The system rejects a naive design because the funding cap never binds and the mark is a slow TWAP. It proposes a tighter cap and a bounded-delay mark.
3. **Simulate.** A slashing event or a VIX spike hits. The naive design has a hidden tail; the calibrated design keeps bad debt inside the budget.
4. **Launch.** Deploy to testnet. Mint the certificate. Post the stress bond. Show the live market.
5. **Adapt.** A stress regime arrives. The margin controller raises margin inside its certified band; the funding controller retunes; the market survives. The certificate updates.
6. **Accountability.** A naive market's bond is slashed; the certified market's bond is intact. The judge sees the full loop.

**Build order.**

1. Payoff DSL and symbolic engine for a small template set.
2. Funding controller and basis band.
3. Margin controller and liquidation simulator.
4. Mark controller and mark-quality audit.
5. Certificate schema and report.
6. Factory and generated market.
7. Live risk monitor and bounded retune.
8. Stress bond and governance.
9. Historical and adversarial scenarios.
10. Security review and generated-code tests.

**Team and agent split.**

- Human 1: compiler and symbolic math lead.
- Human 2: protocol and controller lead.
- Human 3: risk engine and certificate lead.
- Human 4: frontend, authoring UX, and demo lead.
- Agent 1: DSL parser and code generator.
- Agent 2: funding and basis engine.
- Agent 3: liquidation and margin engine.
- Agent 4: mark and depth engine.
- Agent 5: Solidity controllers and tests.
- Agent 6: simulator, certificate, and docs.

**Judge Q&A.**

- **"Isn't this another launchpad?"** No. A launchpad deploys and leaves. Perp OS compiles, certifies, monitors, and adapts. The market has a risk budget and a bond.
- **"Why adaptive controllers?"** Because the effective feedback and the tail are regime-dependent. A static template is stable in one regime and unstable in another.
- **"Can the controller be gamed?"** The controller's bounds are pre-committed and timelocked. It cannot move outside the certificate without a new certificate and a bond update.
- **"What stops a bad design from launching?"** The compiler and the certificate are gates. A deployer can launch an uncertified market elsewhere, but it will not get the integrations or the bond.
- **"What is the moat?"** The compiler, the controller library, the certificate standard, the generated-code test suite, and the integration network.

**Cons (hard truths).**

- **Compiler correctness is hard.** A wrong symbolic derivative or a missed edge case can generate a market that loses money. The DSL must be constrained and the generated code heavily tested.
- **Adaptive controllers can be procyclical.** Raising margin in stress can amplify a cascade. The controller must be designed with the same care as the funding rule, and the certificate must bound the response.
- **Governance risk.** A bounded retune is still a parameter change. An attacker who captures governance can push the market to the edge of the certificate.
- **Oracle manipulation.** A dynamic margin that depends on mark delay or depth can be manipulated if those inputs are weak.
- **Complexity for judges.** A compiler plus controllers plus a certificate is a lot to explain. One flagship instrument is essential.
- **"Another perp DEX" perception.** The project must lead with the OS and the certificate, not the market.
- **Regulatory classification.** A self-tuning derivative with a bond may attract securities or derivatives regulation.
- **Demo risk.** The adaptive behavior must be visible and deterministic, not a black box. Show the parameter change and the before/after survival.

**Kill criteria.**

- The compiler cannot generate a market that passes the same tests as a hand-written market.
- The controller cannot be bounded tightly enough to be safe.
- The certificate is marketing, not a constraint.
- The demo needs a hidden manual step to work.

**Score.** Novelty 5, technical depth 5, demo 5, feasibility 3, judge appeal 5, moat 5, public good 4. **32/35.**

---

### 4.4 PERP CDX

> **One sentence.** A clearinghouse and tranched CDS index for perp liquidation tails that turns hidden bad debt into a tradeable, insurable asset class.

**The crazy bet.** Build the "CDX for perps" and the "FDIC for perps" in one protocol. The market currently socializes bad debt or hides it in an insurance fund. Perp CDX makes the tail explicit: single-name venue or position cover, a basket index, tranches, and a capital vault. It is a new derivative with a real pricing kernel, real yield, and a systemic hedge.

**Problem and buyer.** A trader can be liquidated at a price worse than the displayed liquidation level because the mark is delayed. A venue can suffer bad debt when the mark catches up. A DAO cannot hedge the systemic risk of the perp venues it uses. An insurer cannot price a tail without a standard. The buyer is the trader, the venue, the DAO, the lender, and the capital provider who wants yield from underwriting a measurable risk.

**Paper spine.**

| Paper | What Perp CDX takes |
|---|---|
| 3 | Liquidation probability, hedge effectiveness, optimal leverage, survival |
| 7 | `P_bd`, overshoot, TWAP penalty, depth concession, funding memory, mark quality |
| 4 | Funding-feedback stress and liquidation-capacity feedback |
| 6 | Funding-timing risk and cap-binding risk |
| 5 | Cross-venue reversal hazard and execution friction |
| 2 | Basis dislocation as a stress indicator |

**The primitive.**

- **Perp CDS:** a contract that pays if a reference venue or position cohort suffers a defined credit event over a period.
- **Perp CDX:** a basket of single-name CDS contracts, with a rolling series and tranches.
- **Credit events:** bad-debt ratio above a threshold; realized liquidation loss beyond the mark-implied loss by more than a threshold; mark delay beyond the disclosed maximum; funding-cap breach combined with a persistent basis gap; venue insolvency or withdrawal freeze.
- **Tranches:** equity, mezzanine, senior, each with a different attachment and detachment point.
- **Capital vault:** senior and junior capital, a loss waterfall, and a target return.

**Full workflow.**

1. **Define the reference.** Choose a venue, a market, or a position cohort. Define the credit event precisely, including the data source, the measurement window, and the dispute process.
2. **Price the tail.** Use Paper 7's `P_bd`, overshoot, and depth concession, plus Paper 3's breach probability and Paper 4's funding stress. Produce a distribution of loss, not a point estimate.
3. **Structure the tranches.** Attach and detach points based on the loss distribution. Price each tranche with expected loss, risk margin, and capital cost. Publish the correlation assumption.
4. **Issue and collateralize.** A policy NFT or CDS contract records the reference, notional, premium, maturity, and trigger. The protection seller posts collateral or joins a vault.
5. **Monitor.** The claims oracle ingests venue data and verified reports. It checks the trigger continuously or at settlement. A dispute window protects against false events.
6. **Settle.** If the event occurs, the claims oracle certifies it. The vault pays the protection buyer. The loss waterfall hits equity first, then mezzanine, then senior.
7. **Manage capital.** The vault rebalances, hedges correlated exposure, and buys reinsurance or index protection. It publishes loss ratios and stress tests.
8. **Index.** A rolling basket of venue CDS contracts creates a liquid systemic hedge. A DAO can buy index protection without shorting a specific asset.
9. **Govern.** Parameter changes, trigger definitions, and oracle sources are versioned and timelocked. A material change creates a new series.

**Architecture.**

```
PerpScore / TailMark / PNS feeds
                |
        Pricing engine (expected loss + margin + capital)
                |
   PerpCDS / PerpCDX / TrancheVault / PolicyNFT
                |
     ClaimsOracle + DisputeManager
                |
   CapitalVault (senior/junior) + reinsurance
                |
  Traders | Venues | DAOs | Lenders | Insurers
```

**Onchain contracts.**

- `PerpCDS.sol`: single-name protection, premium, collateral, settlement.
- `PerpCDX.sol`: basket index, series, tranche attachment points.
- `TrancheVault.sol`: senior/junior capital and loss waterfall.
- `ClaimsOracle.sol`: trigger verification and event certification.
- `PolicyNFT.sol`: transferable or non-transferable policy representation.
- `CapitalVault.sol`: deposits, withdrawals, and risk limits.
- `DisputeManager.sol`: event disputes and appeals.
- `RiskMarketFactory.sol`: deploys new reference entities and series.

**Offchain services.**

- Pricing engine and loss-distribution calibration.
- Claims monitoring and evidence collection.
- Capital and correlation stress testing.
- Risk dashboard and investor reporting.
- Legal and disclosure package for the hackathon boundary.

**Demo (six acts).**

1. **The hidden tail.** A 20x SOL position looks safe on a one-minute mark. The judge sees the tail: at a 60-minute delay, `P_bd` is 0.135 fixed and higher under the TWAP crossing approximation.
2. **The cover.** The trader buys Perp CDS protection for a 60-minute mark-delay event. The premium is priced from the tail, not from a guess.
3. **The stress.** A mark-delay event hits. The position is liquidated with a hidden loss. The claims oracle verifies the event.
4. **The payout.** Perp CDX pays. The trader survives; the vault's equity tranche takes the first loss; senior capital is intact.
5. **The index.** A DAO buys Perp CDX index protection. A venue stress event triggers a payout. The DAO hedged systemic risk without shorting ETH.
6. **The capital.** The vault dashboard shows premiums, loss ratio, capital, and the tail distribution. The judge sees that the payout came from the same math that priced the cover.

**Build order.**

1. Credit-event taxonomy and trigger specification.
2. Tail pricing engine from Papers 3 and 7.
3. Single-name CDS contract and claims oracle.
4. Capital vault and loss waterfall.
5. Tranche pricing and tranche vault.
6. Index basket and series.
7. Dispute and governance flow.
8. Mock trader, DAO, and lender integrations.
9. Stress tests and historical calibration.
10. Security review and legal boundary documentation.

**Team and agent split.**

- Human 1: structured products and pricing lead.
- Human 2: protocol and vault lead.
- Human 3: claims and data lead.
- Human 4: investor product and demo lead.
- Agent 1: tail model and pricing engine.
- Agent 2: CDS/CDX contracts.
- Agent 3: vault, tranches, and waterfall.
- Agent 4: claims oracle and dispute flow.
- Agent 5: index and series deployment.
- Agent 6: dashboard, stress tests, and docs.

**Judge Q&A.**

- **"Is this insurance?"** It is a parametric risk-transfer contract with a defined trigger. The hackathon version is a testnet research prototype, not a licensed insurance product.
- **"How do you price it?"** Expected loss from the paper-calibrated tail, plus a risk margin, plus the cost of capital. The distribution is published, not hidden in a single premium.
- **"What about adverse selection?"** The trigger is objective and the reference is public. Underwriting limits, waiting periods, and exclusions for protocol bugs reduce selection.
- **"Why would capital providers participate?"** Real yield from a measurable risk with a transparent loss waterfall and a diversified index.
- **"What is the moat?"** The pricing model, the claims data, the trigger standard, the capital base, and the index series.

**Cons (hard truths).**

- **Insurance and derivatives regulation.** This is the biggest risk. The hackathon version must be testnet-only, parametric, and explicit that it is not licensed insurance.
- **Adverse selection.** The trader who buys cover may know more about the position than the vault. Objective triggers and position-level data help, but do not eliminate it.
- **Moral hazard.** A covered trader may take more risk. Caps, co-pays, and waiting periods are necessary.
- **Correlation.** A cascade can trigger many policies at once. The vault can be solvent in calm markets and insolvent in the stress it was designed for.
- **Trigger ambiguity.** "Bad debt" and "mark-delay loss" are hard to observe. A vague trigger creates disputes; a narrow trigger creates basis risk.
- **Capital intensity.** A credible vault needs capital and diversification. A hackathon vault is a demonstration, not a balance sheet.
- **Data availability.** User-level liquidation losses are not public. Position-level cover may require attested data or a trusted reporter.
- **"Not real insurance" perception.** Judges may discount a testnet vault. Show the full lifecycle: premium, trigger, payout, waterfall.
- **Model risk.** The tail model is calibrated on a short sample and a Gaussian benchmark. Publish uncertainty and stress the tail.

**Kill criteria.**

- The trigger cannot be verified from public or attested data.
- The vault cannot survive a correlated stress scenario.
- The product cannot be explained without a legal disclaimer longer than the pitch.
- The pricing model is a single number with no distribution.

**Score.** Novelty 5, technical depth 5, demo 5, feasibility 3, judge appeal 5, moat 4, public good 5. **32/35.**

---

### 4.5 PERP GENOME

> **One sentence.** An evolutionary design arena that breeds funding rules, stress-tests them, and licenses the winners as onchain "funding DNA" with royalties.

**The crazy bet.** Treat funding-rule design as genetic engineering. Encode a funding rule as a genome, simulate it against adversarial markets, breed the best rules, and license them to venues. The winning genome is not a copied template; it is a calibrated control system with a stability band, a cap-binding profile, and a testability certificate. Create a market for mechanism design itself.

**Problem and buyer.** Every venue copies the same funding template. Paper 4 shows the same template produces different effective feedback across assets. Paper 6 shows a cap can be so wide that the no-arbitrage bound is vacuous. Paper 7 shows funding memory and liquidation drain matter in crowded regimes. There is no systematic way to design a funding rule for a specific asset, liquidity profile, and trader mix. The buyer is a perp DEX, an RWA issuer, a structured-product desk, or a DAO that wants a market with a funding rule that actually works.

**Paper spine.**

| Paper | What the genome encodes and tests |
|---|---|
| 1 | The payoff and funding relationship; the rule must be coherent with the instrument |
| 2 | The no-arbitrage band and the clamp-aware benchmark |
| 4 | Effective feedback, stability band, weak/intermediate/strong regimes, liquidation capacity |
| 6 | Funding timing, cap binding, bound testability |
| 7 | Funding memory, drain variance, breach probability, liquidation tail |

**The primitive.** A `FundingGenome`:

```
genome = {
  rule_type: piecewise | EMA | PID | adaptive,
  sensitivity: kappa,
  flat_band: b_flat,
  cap: phi_max,
  interval: h,
  averaging: none | interval | EMA,
  index: mark | oracle | impact,
  anti_manipulation: none | commit_reveal | median | delay,
  emergency: pause | widen | decouple
}
```

A `Fitness` vector, not a single score:

- basis persistence half-life;
- reversal frequency and amplitude;
- cap-binding frequency;
- liquidation-tail impact;
- funding-timing testability;
- manipulation cost;
- capital efficiency;
- stability-band margin.

**Full workflow.**

1. **Genome schema.** Define the space of legal rules and the constraints. A genome must be deployable and auditable; no arbitrary code.
2. **Simulator.** Build an agent-based market with extrapolative traders, capital-constrained arbitrageurs, liquidity suppliers, and a liquidation engine. Calibrate to historical data for the target asset. Run historical, synthetic, and adversarial scenarios.
3. **Fitness.** Score each genome on the vector. Use a Pareto front rather than a single weighted sum; a rule that is stable but capital-inefficient is different from a rule that is efficient but fragile.
4. **Evolution.** Use a genetic algorithm or Bayesian optimization to breed the next generation. Include human-in-the-loop constraints: no rule that violates a hard safety bound.
5. **Red team.** Adversarial agents try to manipulate the rule, front-run settlement, or force a cap. A genome that only works against a naive simulator is rejected.
6. **Certification.** The best genomes go to Perp Court or Perp OS for a formal certificate. The certificate records the tested regimes, the stability band, and the conditions under which the genome should not be used.
7. **Licensing.** The genome is minted as a license NFT. A venue pays a license fee or a revenue share to deploy it. The royalty is enforced onchain.
8. **Deployment.** The venue deploys the rule through a standard adapter. The genome registry records the deployment and the parameters.
9. **Live monitoring.** The original fitness is compared with live performance. Drift triggers a re-evaluation or a new generation.
10. **Governance.** The genome registry and the fitness methodology are versioned. A license can be revoked if the genome is used outside its certified scope.

**Architecture.**

```
Genome schema + constraints
            |
  Agent-based simulator + historical calibration
            |
  Fitness vector + Pareto front + adversarial red team
            |
  Evolutionary engine + human safety constraints
            |
  Certification (Perp Court / Perp OS)
            |
  LicenseNFT + RoyaltyVault + GenomeRegistry
            |
  Venue adapter -> live market -> drift monitor
```

**Onchain contracts.**

- `GenomeRegistry.sol`: genome hash, parameters, fitness vector, and certification.
- `LicenseNFT.sol`: license terms, scope, expiry, and transfer rules.
- `RoyaltyVault.sol`: license fees and revenue share.
- `FitnessOracle.sol`: certified fitness results and methodology hash.
- `Arena.sol`: generation records, leaderboard, and challenges.
- `Governance.sol`: methodology updates and safety constraints.

**Offchain services.**

- Agent-based simulator.
- Evolutionary engine and experiment tracker.
- Historical data calibration.
- Red-team agent framework.
- Report generator and license documentation.

**Demo (six acts).**

1. **The copied template.** Two assets use the same funding rule. One mean-reverts; the other drifts and hits the cap. The judge sees that the template is not the mechanism.
2. **The genome.** Show the encoding: flat band, sensitivity, cap, interval, index, anti-manipulation. The judge can see what is being evolved.
3. **The arena.** Run a generation. The fitness vector improves: shorter half-life, fewer expanding reversals, lower tail impact, better testability.
4. **The attack.** A red-team agent front-runs settlement or forces the cap. The winning genome survives; a naive genome fails.
5. **The certificate.** The winner is certified with a stability band and a scope. A venue licenses it.
6. **The deployment.** The rule goes live on a testnet market. A shock hits; the basis converges without expanding reversals. The royalty flows.

**Build order.**

1. Genome schema and a small rule library.
2. Agent-based simulator for one asset.
3. Fitness vector and scoring.
4. Historical calibration and scenario library.
5. Evolutionary loop and experiment tracking.
6. Red-team agent framework.
7. Certification handoff.
8. License NFT and royalty vault.
9. Venue adapter and live monitoring.
10. Documentation and reproducibility.

**Team and agent split.**

- Human 1: mechanism design and fitness lead.
- Human 2: simulator and calibration lead.
- Human 3: protocol and licensing lead.
- Human 4: demo and narrative lead.
- Agent 1: genome schema and rule library.
- Agent 2: agent-based simulator.
- Agent 3: evolutionary engine.
- Agent 4: red-team agents.
- Agent 5: Solidity registry, license, and royalties.
- Agent 6: dashboard, experiments, and docs.

**Judge Q&A.**

- **"Isn't this just parameter optimization?"** No. It searches the space of mechanisms, including interval, cap, flat band, index, and anti-manipulation design, and it evaluates a vector of stability, testability, and tail outcomes.
- **"How do you avoid overfitting?"** Train/test splits, held-out regimes, adversarial scenarios, and a Pareto front rather than a single backtest winner.
- **"Why would a venue license a rule?"** Because a calibrated rule can reduce basis persistence and liquidation tail, and the license comes with a certificate and a safety scope.
- **"Can the genome be gamed?"** The genome is constrained and audited. The fitness includes manipulation cost and cap-binding behavior. A rule that only works in the simulator is rejected.
- **"What is the moat?"** The simulator, the fitness data, the certified genome library, and the licensing network.

**Cons (hard truths).**

- **Simulator misspecification.** The evolved rule can exploit the simulator, not the market. This is the central risk. Held-out data and adversarial agents are mandatory, and the pitch must be honest about it.
- **Overfitting.** A genome that wins on 2023-2024 may fail in 2026. The certificate must have an expiry and a regime scope.
- **Interpretability.** Evolved rules can be weird. A venue's governance may reject a rule it cannot explain. Constrain the genome and require a human-readable summary.
- **Fitness gaming.** A venue can choose a favorable fitness weighting. Publish the vector, not just the rank.
- **Licensing law.** A funding rule may not be enforceable as intellectual property in every jurisdiction. The hackathon version is a registry and a royalty demo, not legal advice.
- **Adoption.** Venues may prefer to copy a rule rather than license it. The certificate and the liability shield are the value, not the code.
- **Compute.** A large agent-based simulation is expensive. Start with a small asset set and a fast surrogate model.
- **"Research toy" perception.** A live deployment and a real license are essential.

**Kill criteria.**

- The evolved genome cannot beat a simple calibrated rule on held-out data.
- The red team can break every winner with a trivial attack.
- The license and royalty flow is not enforceable onchain.
- The team cannot explain why a genome is safe, not just fit.

**Score.** Novelty 5, technical depth 5, demo 4, feasibility 4, judge appeal 4, moat 5, public good 4. **31/35.**

---

### 4.6 PERP MARKETS

> **One sentence.** The first exchange for perp risk factors: funding-rate perps, basis perps, mark-quality futures, liquidation-tail forwards, and a tradeable Perp VIX.

**The crazy bet.** Create a new asset class by making the inputs of perp risk tradeable. Today traders can hedge ETH, but a venue cannot hedge its funding risk, a DAO cannot hedge its basis risk, and an insurer cannot hedge its liquidation-tail exposure. Perp Markets turns each risk factor into a standardized index and a contract, so the market can price and transfer the risk that the seven papers measure.

**Problem and buyer.** Funding risk, basis risk, mark risk, and liquidation-tail risk are not directly hedgeable. A market maker can hedge price but not a funding spike. A DAO can hedge ETH but not the perp venue's mark delay. An insurer can underwrite a tail but cannot lay it off. The buyer is the venue, the market maker, the DAO treasury, the insurer, and the basis trader who wants a pure exposure.

**Paper spine.**

| Paper | What the market lists |
|---|---|
| 1 | Generic payoff and funding for an index perp; the index is the underlying |
| 2 | Basis band and clamp-aware fair value; a basis index |
| 5 | Cross-venue funding spread and executable spread; a two-tier index |
| 6 | Funding timing and cap-binding risk; a funding-timing index |
| 7 | Mark quality, latency, depth, bad-debt tail; a mark-quality and tail index |

**The primitive.** An index family:

- **Funding Rate Index (FRI):** normalized cross-venue funding, 8-hour equivalent, robust median.
- **Basis Index (BI):** clamp-aware deviation `rho`, aggregated across venues.
- **Mark Quality Index (MQI):** delay, smoothing, crossing-conditioned overshoot, disclosure.
- **Liquidation Tail Index (LTI):** breach probability and bad-debt probability at reference leverage.
- **Perp VIX:** a composite systemic stress index.
- **Two-Tier Index (TTI):** CEX-DEX integration and executable spread.

Contracts:

- **Risk-factor perp:** perpetual exposure to an index, with its own funding rule.
- **Risk-factor future:** fixed-maturity, cash-settled contract.
- **Perp VIX option:** a cap or floor on the index.
- **Tail forward:** a forward on the liquidation-tail index.

**Full workflow.**

1. **Index design.** Define each index with a transparent formula, data sources, normalization, outlier handling, and a fallback. Publish the methodology and the historical backfill.
2. **Oracle.** Aggregate across venues with a median or trimmed mean. Use time weighting and a dispute window. Publish confidence and missingness. The oracle is the product's weakest point; treat it as such.
3. **Contract design.** For each index, choose perp or future, margin, funding, cap, and settlement. Use Paper 1 to derive the funding for an index perp. Use a cash-settled future for a fixed maturity.
4. **Market making.** Seed liquidity with a vault or a professional market maker. Publish risk limits and inventory rules. A cold-start market needs a subsidy or a committed quote.
5. **Hedging.** A venue can short a funding-rate perp to hedge a funding spike. A DAO can buy Perp VIX protection. An insurer can buy a tail forward to lay off correlated exposure.
6. **Portfolio margin.** Allow cross-margining between the risk-factor contract and the underlying perp, with a risk model that accounts for correlation.
7. **Settlement and disputes.** Cash settlement from the oracle. A dispute window and a fallback index. No physical delivery.
8. **Risk monitoring.** Track index volatility, oracle deviation, liquidity, and concentration. Pause a market if the index is unreliable.
9. **Governance.** Index methodology, contract parameters, and oracle sources are versioned and timelocked.

**Architecture.**

```
Venue data (CEX/DEX) + onchain indexers
                |
        Index engine + robust oracle
                |
  FRI | BI | MQI | LTI | Perp VIX | TTI
                |
  RiskFactorPerp | RiskFactorFuture | VIX option | Tail forward
                |
     Margin engine + liquidity vault + risk limits
                |
  Venues | Market makers | DAOs | Insurers | Basis traders
```

**Onchain contracts.**

- `IndexOracle.sol`: index values, confidence, and methodology hash.
- `RiskFactorPerp.sol`: perp on an index with its own funding rule.
- `RiskFactorFuture.sol`: fixed-maturity cash-settled future.
- `RiskFactorOption.sol`: Perp VIX cap/floor.
- `MarketFactory.sol`: deploys index markets from a spec.
- `MarginEngine.sol`: portfolio margin and liquidation.
- `LiquidityVault.sol`: seeded liquidity and risk limits.
- `DisputeManager.sol`: index disputes and fallback.

**Offchain services.**

- Index calculation and backfill.
- Oracle aggregation and deviation monitoring.
- Market-making bot or vault strategy.
- Risk dashboard and historical charts.
- Legal and regulatory boundary documentation.

**Demo (six acts).**

1. **The risk nobody can hedge.** Show a venue with a funding spike and a DAO with basis risk. Neither has a direct hedge.
2. **The index.** Show the FRI, BI, and Perp VIX charts. Explain the formula and the data.
3. **The hedge.** A venue shorts a funding-rate perp; a DAO buys Perp VIX protection. The judge sees the exposure transfer.
4. **The stress.** A funding spike and a basis dislocation hit. The funding-rate perp pays; the VIX option pays. The venue and DAO are protected.
5. **The tail.** An insurer buys a tail forward and lays off a correlated liquidation exposure. The judge sees a new risk market.
6. **The market.** Show the order book, the margin, and the settlement. The judge sees a real market, not an index dashboard.

**Build order.**

1. Index definitions and historical backfill.
2. Robust oracle and deviation monitor.
3. One index perp (Perp VIX or FRI) with a simple margin engine.
4. Liquidity vault and market-making strategy.
5. Basis and mark-quality indices.
6. Tail index and forward.
7. Portfolio margin and cross-hedging.
8. Dispute and governance.
9. Historical stress tests.
10. Security review and market launch.

**Team and agent split.**

- Human 1: index and quantitative lead.
- Human 2: market and protocol lead.
- Human 3: oracle and data lead.
- Human 4: market-making and demo lead.
- Agent 1: index engine and backfill.
- Agent 2: oracle and deviation monitor.
- Agent 3: perp/future/option contracts.
- Agent 4: margin engine and liquidation.
- Agent 5: liquidity vault and quoting bot.
- Agent 6: frontend, charts, and docs.

**Judge Q&A.**

- **"Why trade an index instead of the underlying?"** Because the risk factor is not the underlying. A funding spike, a basis dislocation, and a mark-quality change are separate exposures that the underlying does not isolate.
- **"Who provides liquidity?"** A seeded vault and a committed market maker, with published risk limits. The hackathon version demonstrates the mechanism, not a deep market.
- **"How do you prevent index manipulation?"** Multi-venue aggregation, median/trimmed mean, time weighting, outlier filters, a dispute window, and a fallback.
- **"Is this a security?"** The hackathon version is a testnet research prototype. A production version requires legal analysis and jurisdiction-specific structuring.
- **"What is the moat?"** The index methodology, the historical data, the oracle, the market-making relationships, and the first-mover liquidity.

**Cons (hard truths).**

- **Liquidity cold start.** A new risk-factor market has no natural flow. Without a committed market maker or a subsidy, the order book is empty and the demo is a chart.
- **Index manipulation.** The underlying data are fragmented and sometimes wrong. A manipulated venue can move the index. Robust aggregation helps but does not eliminate the risk.
- **Regulatory complexity.** New derivatives on novel indices may be classified as swaps, securities, or insurance. The legal boundary is a real constraint.
- **Basis and correlation risk.** A risk-factor hedge may not track the exposure it is meant to hedge. The basis between the index and the venue's actual risk is a product risk.
- **Complexity for judges.** "Trade the VIX for perps" is memorable; "five indices and four contract types" is not. Lead with one instrument.
- **Capital and margin.** A risk-factor perp needs a margin model and a liquidation engine. That is another perp DEX, with the same hard problems.
- **Oracle disputes.** A settlement dispute can freeze the market. The fallback and dispute process must be designed before launch.
- **"Why not just use options?"** Options on the underlying do not isolate funding, basis, or mark quality. The index is the point, but the pitch must make that obvious.

**Kill criteria.**

- No committed liquidity for the demo instrument.
- The index cannot be computed reproducibly from public data.
- The legal boundary is unclear even for a testnet prototype.
- The contract is a clone of a perp with a different label.

**Score.** Novelty 5, technical depth 5, demo 5, feasibility 3, judge appeal 5, moat 5, public good 4. **32/35.**

---

### 4.7 PERP TWIN

> **One sentence.** A continuously calibrated digital twin of every perp market, with a scenario marketplace and an agentic risk analyst that answers "what breaks, when, and who pays?"

**The crazy bet.** Give every perp venue, lender, and insurer a flight simulator. The twin is not a static backtest; it is a live model of the venue's funding, mark, margin, liquidity, and liquidation behavior, calibrated from public data and updated as the market changes. A scenario marketplace lets anyone sell a stress test, and an agentic analyst explains the result with citations to the data and the papers.

**Problem and buyer.** Risk teams cannot test a design without building a proprietary model. Data are fragmented. There is no common scenario language. A lender cannot ask "what happens to this collateral if funding spikes and the mark is delayed?" A venue cannot ask "which parameter change reduces the tail?" An insurer cannot ask "what is the loss distribution under a correlated cascade?" The buyer is the venue, the lender, the insurer, the DAO, and the researcher.

**Paper spine.**

| Paper | What the twin models |
|---|---|
| 1 | Payoff, funding, hedge ratios, jump terms |
| 2 | Basis band, clamp, deviation dynamics |
| 3 | Liquidation probability, hedge effectiveness, survival |
| 4 | Effective funding feedback and stability regimes |
| 5 | CEX-DEX transmission, execution costs, reversal hazard |
| 6 | Funding timing and cap testability |
| 7 | Mark latency, depth, funding memory, bad-debt tail |

**The primitive.** A `Twin`:

- **State:** venue, asset, contract specs, funding rule, mark rule, margin schedule, depth, open interest, collateral.
- **Parameters:** volatility, funding persistence, feedback sensitivity, depth curve, mark delay, correlation.
- **Simulator:** agent-based or path-level, with a liquidation engine and a funding engine.
- **Uncertainty:** posterior or ensemble over parameters; every output is a distribution.
- **Scenario:** a named, versioned stress test with inputs and expected outputs.
- **TwinScore:** a compact risk summary for onchain consumption.
- **Report:** a reproducible document with charts, formulas, and data citations.

**Full workflow.**

1. **Onboard a market.** Ingest the venue spec and the available data. Tag missing data. If the market cannot be reconstructed, the twin says so.
2. **Calibrate.** Estimate volatility, funding persistence, feedback, depth, mark delay, and correlation. Use rolling windows and uncertainty quantification. Do not pretend a point estimate is a truth.
3. **Build the twin.** Instantiate the simulator with the calibrated parameters. Validate on a holdout period: can it reproduce the observed funding, basis, and liquidation behavior?
4. **Scenario library.** Curate historical scenarios, synthetic scenarios, and adversarial scenarios. Each scenario has a clear narrative and a version.
5. **Run.** Execute the scenario ensemble. Produce distributions of breach probability, bad debt, funding cost, and basis path. Report the median and the tail.
6. **Agentic analyst.** An LLM agent uses deterministic tools to query the twin, explain the result, and generate a report. It must cite the data and the formula, and it must not invent numbers.
7. **Marketplace.** A scenario author can mint a scenario or a report. A venue or insurer can buy it. Peer review and reproducibility are part of the value.
8. **Onchain score.** Publish `TwinScore`, `LiquidationTail`, and `MarkQuality` for consumers. The score is a summary, not a substitute for the report.
9. **Monitor.** Update the twin as new data arrive. Flag drift, regime change, and parameter instability.
10. **Govern.** Methodology, scenario versions, and model changes are public and versioned.

**Architecture.**

```
Venue specs + CEX/DEX data + onchain indexers
                    |
        Data lake + calibration engine
                    |
        Digital twin (state + parameters + simulator)
                    |
   Scenario library + ensemble runner + agentic analyst
                    |
   TwinScore / reports / scenario NFTs / API
                    |
  Venues | Lenders | Insurers | DAOs | Researchers
```

**Onchain contracts.**

- `TwinRegistry.sol`: twin ID, venue, asset, parameter hash, and version.
- `ScenarioNFT.sol`: scenario metadata, inputs, author, and license.
- `ReportRegistry.sol`: report hash, twin version, and reproducibility link.
- `TwinScoreOracle.sol`: compact risk score and confidence.
- `PaymentSplitter.sol`: revenue share for scenario authors and reviewers.
- `MethodologyRegistry.sol`: model versions and changelog.

**Offchain services.**

- Data lake and calibration.
- Digital twin simulator.
- Scenario runner and ensemble engine.
- Agentic analyst with deterministic tool calls.
- Report renderer and reproducibility package.
- Marketplace backend and peer review.

**Demo (six acts).**

1. **The question.** A lender asks: "What happens to this 20x SOL collateral if funding spikes and the mark is delayed by 60 minutes?"
2. **The twin.** The system calibrates the venue live, shows the parameter distributions, and validates on a holdout period.
3. **The scenario.** Run the stress. The twin produces a loss distribution, not a point estimate: `P_bd`, overshoot, bad debt, and the path.
4. **The analyst.** The agent explains the result, cites the mark-delay formula and the depth assumption, and flags the model risk. The judge can click the evidence.
5. **The action.** The lender lowers the collateral factor; the venue changes its mark design; the insurer re-prices. The twin re-runs and shows the improvement.
6. **The marketplace.** A scenario author mints a new stress test; a venue buys it; the report is reproducible. The judge sees a knowledge market, not a dashboard.

**Build order.**

1. Data ingestion and one-market twin.
2. Calibration and holdout validation.
3. Funding, basis, and liquidation engine.
4. Mark/depth and funding-memory models.
5. Scenario library and ensemble runner.
6. Agentic analyst with tool calls.
7. Report and reproducibility package.
8. Onchain score and registry.
9. Scenario marketplace and payments.
10. Multi-venue expansion and security review.

**Team and agent split.**

- Human 1: modeling and calibration lead.
- Human 2: simulation and data lead.
- Human 3: agent and product lead.
- Human 4: frontend and demo lead.
- Agent 1: data ingestion and normalization.
- Agent 2: calibration and uncertainty.
- Agent 3: simulator and scenarios.
- Agent 4: agentic analyst tools.
- Agent 5: onchain registry and score.
- Agent 6: report renderer and marketplace.

**Judge Q&A.**

- **"Isn't this just a backtest?"** A backtest is fixed. The twin is calibrated live, validated on a holdout, and updated as the market changes. It produces distributions and supports what-if analysis.
- **"Why an agent?"** The agent is an interface to deterministic tools, not a source of truth. It explains and navigates; every number comes from the simulator or the data.
- **"How do you avoid hallucination?"** The agent can only call tools and cite results. It cannot emit a number that is not in the tool output. The report includes a reproducibility link.
- **"Who pays for a scenario?"** Venues, lenders, insurers, and DAOs that need a specific stress test. The marketplace is a way to crowdsource scenarios and share the cost.
- **"What is the moat?"** The calibrated twin library, the scenario data, the reproducibility record, and the integrations.

**Cons (hard truths).**

- **"Just a simulation" perception.** The twin must change a decision or a contract, not just produce a report. The onchain score and the consumer adapter are essential.
- **Data gaps.** Public data do not reveal user-level collateral, liquidation paths, or historical marks. The twin must label what it cannot see.
- **Model risk.** A calibrated twin can be confidently wrong. Uncertainty quantification and holdout validation are not optional.
- **Agent hallucination.** An LLM that invents a number destroys trust. Constrain the agent to tools and show the citations.
- **Compute cost.** A high-dimensional ensemble is expensive. Use a fast surrogate and a smaller scenario set for the demo.
- **Marketplace cold start.** A scenario marketplace needs authors and buyers. Start with a curated library and one paid report.
- **IP and privacy.** A venue may not want its data or its scenario public. Support private twins and selective disclosure.
- **"Why onchain?"** If the score is only a report, the onchain part is decorative. Make it consumable by a contract.

**Kill criteria.**

- The twin cannot reproduce a known holdout behavior within a stated tolerance.
- The agent cannot answer a question without inventing data.
- No consumer contract reads the score.
- The scenario marketplace has no real buyer.

**Score.** Novelty 4, technical depth 5, demo 5, feasibility 4, judge appeal 5, moat 4, public good 5. **32/35.**

---

### 4.8 PERP SAFE

> **One sentence.** A risk-adjusted execution firewall and router for perps that simulates liquidation before the trade, routes to the safest executable venue, and attaches tail insurance at the point of trade.

**The crazy bet.** "Best execution" for perps should mean best survival-adjusted execution, not the lowest fee. Perp Safe puts a risk engine in front of every trade: it simulates the liquidation tail, checks the venue's mark quality and depth, routes the order to the best risk-adjusted price, and optionally attaches insurance. It is the consumer-facing layer that makes the seven papers usable by a normal trader or DAO.

**Problem and buyer.** A trader chooses a venue by fee and liquidity, not by mark quality, liquidation tail, or funding timing. A DAO treasury does the same. Wallets and aggregators show price, not survival. Insurers cannot underwrite at the point of trade. The buyer is the trader, the DAO, the wallet, the aggregator, and the insurer who wants distribution.

**Paper spine.**

| Paper | What Perp Safe uses |
|---|---|
| 2 | Clamp-aware fair value and basis deviation |
| 3 | Liquidation-aware sizing and hedge effectiveness |
| 5 | CEX-DEX execution, costs, reversal hazard |
| 6 | Funding timing and cap testability |
| 7 | Mark quality, depth, and bad-debt tail |

**The primitive.** A `PreTradeRiskCheck` and a `Route`:

- **Intent:** asset, side, size, leverage, horizon, collateral, venue constraints.
- **Risk check:** liquidation probability, bad-debt probability, mark delay, depth concession, funding cost, basis deviation, venue score.
- **Route:** expected utility = expected return - risk penalty - execution cost; split across venues if needed.
- **Attach:** optional parametric insurance or a liquidation buffer.
- **Proof:** a signed execution report with the pre-trade check and the post-trade outcome.

**Full workflow.**

1. **Intent capture.** The user specifies the trade. The system asks for the missing risk inputs: leverage, horizon, collateral, and whether the position is a hedge or a speculation.
2. **Market data.** Pull live quotes, funding, mark, index, depth, and venue risk scores. Handle stale data explicitly.
3. **Pre-trade simulation.** Run the liquidation-tail model for each candidate venue and position size. Compute the probability of breach and the conditional bad debt. Include funding cost and execution concession.
4. **Risk-adjusted routing.** Rank routes by expected utility, not price. Split the order if a single venue is too thin. Avoid venues with a mark-quality red flag.
5. **Guardrails.** Enforce position limits, leverage limits, venue concentration limits, and a liquidation-probability budget. The user can override only with an explicit acknowledgement.
6. **Execution.** Route the order through the available adapters. For the hackathon, use a testnet DEX or a simulated CEX adapter. Do not custody funds.
7. **Insurance attach.** If the user chooses, attach a parametric policy or a liquidation buffer. The premium is priced from the pre-trade tail.
8. **Post-trade monitoring.** Track the position, funding, mark, and liquidation distance. Alert or auto-deleverage within the user's mandate.
9. **Proof and reporting.** Publish a signed report: the pre-trade risk, the route, the execution, and the outcome. This is the data flywheel for the insurer and the venue score.
10. **Governance.** Risk parameters, venue scores, and insurance terms are versioned and auditable.

**Architecture.**

```
User intent (wallet / DAO / API)
            |
  Pre-trade risk check (liquidation tail, mark, depth, funding)
            |
  Risk-adjusted router (expected utility, limits, splitting)
            |
  Execution adapters (testnet DEX / simulated CEX)
            |
  Insurance attach (policy / buffer)
            |
  Post-trade monitor + signed execution report
```

**Onchain contracts.**

- `RiskRouter.sol`: route selection and guardrails.
- `PreTradeOracle.sol`: venue risk scores and tail parameters.
- `InsuranceAttach.sol`: policy attachment and premium.
- `ExecutionProof.sol`: signed execution report.
- `PolicyNFT.sol`: parametric cover.
- `RiskLimitRegistry.sol`: user and DAO limits.

**Offchain services.**

- Quote aggregation and normalization.
- Pre-trade simulation engine.
- Router and order splitter.
- Execution adapters and sandbox.
- Monitoring keeper and alert service.
- Report generator.

**Demo (six acts).**

1. **The tempting trade.** A user wants a 20x SOL long on the cheapest venue. The naive router shows the lowest fee.
2. **The hidden risk.** Perp Safe runs the pre-trade check: the venue has a slow mark, thin depth, and a high liquidation tail. The "cheapest" route is not the safest.
3. **The route.** The router chooses a venue with a slightly higher fee but a much lower tail, and splits the order to reduce impact.
4. **The cover.** The user attaches a parametric policy for a mark-delay event. The premium is priced from the tail.
5. **The stress.** A drawdown and a mark delay hit. The naive position is liquidated with a hidden loss; the Perp Safe position survives or the policy pays.
6. **The proof.** The signed report shows the pre-trade warning, the route, and the outcome. The insurer and the venue score update.

**Build order.**

1. Intent and risk-check schema.
2. Pre-trade liquidation and funding model.
3. Venue risk score integration.
4. Router and order splitter.
5. Testnet execution adapter.
6. Insurance attach and policy.
7. Post-trade monitor and alerts.
8. Signed execution report.
9. Historical comparison and backtest.
10. Security review and wallet integration.

**Team and agent split.**

- Human 1: trading and risk lead.
- Human 2: router and execution lead.
- Human 3: insurance and policy lead.
- Human 4: frontend and demo lead.
- Agent 1: quote aggregation and normalization.
- Agent 2: pre-trade risk engine.
- Agent 3: router and order splitter.
- Agent 4: execution adapter.
- Agent 5: insurance and policy contracts.
- Agent 6: monitoring, reports, and docs.

**Judge Q&A.**

- **"Isn't this just a DEX aggregator?"** No. An aggregator optimizes price. Perp Safe optimizes survival-adjusted execution and attaches insurance. It is a risk layer, not a price comparison.
- **"Why would a user pay for this?"** To avoid a hidden liquidation loss and to get a better risk-adjusted outcome. The fee is small relative to the tail it avoids.
- **"How do you know the venues' risk?"** From public data and the PerpScore/TailMark oracles. We show the inputs and the confidence.
- **"Is the insurance real?"** The hackathon version is a testnet parametric prototype. The production version requires licensing and capital.
- **"What is the moat?"** The execution integrations, the risk-adjusted routing data, the insurance attach, and the outcome track record.

**Cons (hard truths).**

- **Execution integration is hard.** CEX APIs, custody, and regulation are not hackathon-friendly. The demo may be a simulator, which weakens the "real product" claim.
- **Latency.** A pre-trade simulation takes time. A trader may not wait. The system needs a fast path and a slow path.
- **False safety.** A risk model can be wrong. A user who buys insurance may take more risk. The product must not imply "safe."
- **Adverse selection.** The users most likely to buy cover are the ones most likely to claim. Underwriting limits and objective triggers are necessary.
- **Fee sensitivity.** A trader chasing a few basis points may not pay for risk. The value is clearest for large or leveraged positions.
- **Regulatory perimeter.** Routing orders and attaching insurance can trigger broker-dealer, derivatives, or insurance regulation.
- **Venue conflict.** A venue may not want a router that steers users away. The router must be transparent and independent.
- **"Just a feature" perception.** The product needs a clear standalone demo and a measurable outcome, not a widget.

**Kill criteria.**

- The router cannot show a better risk-adjusted outcome than a naive route.
- The insurance attach is a mock with no payout.
- The execution adapter is not real enough to believe.
- The product cannot explain its regulatory boundary.

**Score.** Novelty 4, technical depth 4, demo 5, feasibility 4, judge appeal 5, moat 4, public good 4. **30/35.**

---

### 4.9 PERP CONSTITUTION

> **One sentence.** A bonded commitment protocol where perp venues lock their funding, mark, margin, and liquidation rules for a fixed term, and a monitoring oracle enforces the promises onchain.

**The crazy bet.** Turn market-design promises into enforceable, tradeable guarantees. A venue publishes a "constitution": the funding formula, the cap, the mark method, the maximum delay, the margin schedule, the liquidation fees, and the oracle sources. It posts a bond. A monitoring oracle verifies compliance. If the venue breaks the constitution, the bond is slashed and users are compensated. It is an SLA for perp markets.

**Problem and buyer.** Venues change funding caps, mark rules, and margin schedules with little notice. Users cannot verify what they are trading. Insurers and lenders cannot price rule risk. A venue that wants to differentiate cannot credibly commit. The buyer is the venue that wants trust, the trader who wants recourse, the insurer who wants a stable reference, and the lender who wants a disclosure standard.

**Paper spine.**

| Paper | What the constitution commits to |
|---|---|
| 4 | Funding template, effective feedback, stability band, and the conditions for a retune |
| 6 | Funding timing, cap, interval, and bound testability |
| 7 | Mark method, maximum delay, margin schedule, liquidation fees, and depth assumptions |
| 2 | No-arbitrage band and clamp-aware benchmark |
| 5 | Execution and cross-venue disclosure |

**The primitive.** A `Constitution`:

- **Committed parameters:** funding rule, interval, cap, flat band, mark method, max delay, margin schedule, liquidation fee, oracle sources, pause conditions.
- **Bounds:** the allowed range for each parameter and the process for a change.
- **Disclosure:** the exact data sources, fallback rules, and historical uptime.
- **Bond:** the venue's collateral against a violation.
- **Compliance oracle:** a monitoring service that checks the committed parameters against onchain and public data.
- **Violation proof:** a verifiable proof of a material change or a breach.
- **SLA NFT:** a transferable or non-transferable representation of the commitment, with an expiry.

**Full workflow.**

1. **Draft.** The venue selects a template or a custom constitution. The system checks that every parameter is measurable and that the disclosure is complete.
2. **Stress test.** Run the constitution through the test suite: funding stress, mark delay, liquidity crunch, oracle outage, governance change. The venue sees the trade-offs before it commits.
3. **Bond.** The venue posts a bond sized to the potential user loss and the market's risk. The bond terms are public.
4. **Publish.** The constitution is hashed and stored onchain. The data sources and the monitoring rules are public.
5. **Monitor.** The compliance oracle ingests onchain and public data. It checks the funding, mark, margin, and liquidation parameters. It publishes a compliance score and any deviations.
6. **Challenge.** A user or a watchdog can challenge a suspected violation with evidence. The challenge requires a bond.
7. **Adjudicate.** An optimistic oracle or a human panel reviews the evidence. A material violation triggers a slash; a frivolous challenge loses its bond.
8. **Compensate.** The slashed bond pays affected users or the insurance fund according to a published waterfall.
9. **Consume.** Lenders, insurers, and users read the constitution and the compliance score. A compliant venue gets better terms; a violator is visible.
10. **Upgrade.** A parameter change requires a timelock, a new bond, and a new certificate. A material change creates a new constitution version.

**Architecture.**

```
Venue constitution (parameters + bounds + disclosure)
                    |
        Stress test + bond sizing
                    |
     ConstitutionRegistry + BondVault + SLANFT
                    |
   ComplianceOracle (onchain + public data)
                    |
   Challenge -> DisputeManager -> Slash / compensation
                    |
  Users | Lenders | Insurers | Watchdogs
```

**Onchain contracts.**

- `ConstitutionRegistry.sol`: constitution hash, parameters, bounds, and version.
- `ComplianceOracle.sol`: compliance checks and deviation records.
- `BondVault.sol`: venue and challenger bonds.
- `ViolationManager.sol`: violation proofs and slashing.
- `SLANFT.sol`: commitment representation and expiry.
- `DisputeManager.sol`: optimistic dispute and escalation.
- `CompensationVault.sol`: user compensation waterfall.

**Offchain services.**

- Constitution template and validator.
- Parameter monitoring and data ingestion.
- Violation detection and evidence packaging.
- Stress-test runner and bond sizing.
- Dashboard and public registry.

**Demo (six acts).**

1. **The promise.** A venue publishes a constitution: a specific funding rule, a 60-minute maximum mark delay, and a margin schedule.
2. **The bond.** The venue posts a bond. The judge sees the terms and the compensation waterfall.
3. **The violation.** The venue changes its mark delay to 120 minutes without notice. The compliance oracle detects the deviation.
4. **The challenge.** A user challenges with evidence. The dispute window opens.
5. **The slash.** The panel confirms the violation. The bond is slashed; the user is compensated.
6. **The market.** A lender and an insurer read the constitution and the compliance score. The compliant venue gets better terms; the violator is excluded. The judge sees enforceable trust.

**Build order.**

1. Constitution schema and validator.
2. Parameter monitoring for funding, mark, and margin.
3. Compliance oracle and deviation records.
4. Bond vault and compensation waterfall.
5. Challenge and dispute flow.
6. SLA NFT and registry.
7. Mock lender and insurer integrations.
8. Stress test and bond sizing.
9. Historical case study.
10. Security review and legal boundary.

**Team and agent split.**

- Human 1: market design and constitution lead.
- Human 2: protocol and bond lead.
- Human 3: monitoring and data lead.
- Human 4: legal/narrative and demo lead.
- Agent 1: constitution schema and validator.
- Agent 2: compliance oracle and monitoring.
- Agent 3: bond, slash, and compensation contracts.
- Agent 4: dispute and challenge flow.
- Agent 5: stress test and bond sizing.
- Agent 6: dashboard, registry, and docs.

**Judge Q&A.**

- **"Why would a venue lock its parameters?"** To credibly differentiate, lower its insurance and lending costs, and attract users who value predictability. The bond is a commitment device.
- **"What if the venue needs to change a parameter in a crisis?"** The constitution defines the emergency process and the bounds. A change is timelocked, bonded, and disclosed; it is not silent.
- **"How do you prove a violation?"** The compliance oracle compares the committed parameters with onchain and public data. A material change is a verifiable event.
- **"Is the bond enough to compensate users?"** The bond is sized to the potential loss and the market risk. It is not full insurance; it is a credible commitment.
- **"What is the moat?"** The constitution standard, the compliance data, the bond market, and the integrations that require it.

**Cons (hard truths).**

- **Legal enforceability.** A bond on a testnet is not a legal contract. A production version needs jurisdiction-specific legal review.
- **Rigidity.** Locking parameters can prevent a venue from responding to a crisis. The emergency process must be credible, or the constitution becomes a trap.
- **Bond sizing.** A bond that is too small is not credible; too large is capital-inefficient. The sizing model is a hard problem.
- **Monitoring gaps.** A venue can change a parameter off-chain or in a fallback path that the oracle does not see. The disclosure must cover the full rule.
- **Adoption.** Venues may prefer opacity. Start with a venue that wants to differentiate and an insurer or lender that requires the standard.
- **Gaming.** A venue can create a new constitution version to escape a violation. The versioning process must treat a material change as a new commitment with a new bond.
- **"Niche legal product" perception.** The pitch must lead with the user protection and the bond, not the legal structure.
- **Demo risk.** A parameter change and a slash can look bureaucratic. Make the compensation visible and the before/after clear.

**Kill criteria.**

- The compliance oracle cannot detect a material change from public data.
- The bond and compensation flow is not enforceable onchain.
- The emergency process is undefined.
- No consumer reads the constitution.

**Score.** Novelty 5, technical depth 4, demo 4, feasibility 4, judge appeal 4, moat 4, public good 5. **30/35.**

---

### 4.10 PERP PASSPORT

> **One sentence.** A zero-knowledge risk and solvency passport for perp traders and DAOs, enabling private underwriting, better collateral terms, and portable liquidation protection.

**The crazy bet.** Make perp risk verifiable without exposing the position. A trader can prove "my liquidation distance is above 20%, my leverage is below 5x, my venue is certified, and I have no bad debt" without revealing size, entry, or strategy. A lender can underwrite with a proof, an insurer can price with a proof, and a DAO can prove treasury solvency. It is privacy-preserving risk infrastructure, native to Ethereum.

**Problem and buyer.** Lenders and insurers cannot assess perp risk without invasive data. Traders will not reveal positions. DAOs need to prove solvency without doxxing the treasury. Insurance suffers from adverse selection. The buyer is the trader who wants better terms, the DAO that needs privacy, the lender that wants a risk signal, and the insurer that wants a distribution channel.

**Paper spine.**

| Paper | What the passport proves |
|---|---|
| 3 | Liquidation probability, hedge effectiveness, survival |
| 7 | Mark quality, depth, bad-debt probability, funding memory |
| 2 | Basis deviation and venue fair-value context |
| 4 | Funding regime and stability context |
| 6 | Funding timing and cap testability |

**The primitive.** A `RiskPassport` with a zero-knowledge proof of predicates:

```
prove:
  liquidation_distance >= d
  leverage <= L
  hedge_effectiveness >= h
  venue_score >= s
  bad_debt_probability <= p
  position_size is within a committed range
without revealing:
  exact size, entry, collateral, strategy, or wallet identity
```

The passport is an SBT or a revocable attestation with an expiry. It can be selectively disclosed to a lender, insurer, or auditor.

**Full workflow.**

1. **Connect.** The user connects a wallet and, optionally, a read-only exchange account or an onchain position. The data stay local or in a trusted execution environment if they cannot be proven onchain.
2. **Compute.** A local risk engine computes liquidation distance, leverage, hedge effectiveness, venue score, and bad-debt probability using the paper models.
3. **Prove.** A ZK circuit proves the predicates over private inputs. For data that cannot be proven onchain (CEX positions), use a signed attestation or a TEE with a clear trust assumption.
4. **Mint.** The passport is minted with the predicate set, the expiry, the methodology hash, and the trust mode (ZK, TEE, attested).
5. **Consume.** A lender reads the passport and lowers the collateral haircut. An insurer reads it and offers a policy. A DAO proves treasury solvency. A venue offers a better margin tier.
6. **Monitor.** The user refreshes the proof as the position changes. A stale passport expires. A liquidation alert can be sent without revealing the position.
7. **Claim.** If the insured event occurs, the policy and the passport are used to verify the claim, with selective disclosure to the claims oracle.
8. **Revoke.** A passport can be revoked if the underlying position is closed or the proof is invalidated.
9. **Govern.** The predicate library, the circuits, and the methodology are versioned and audited.

**Architecture.**

```
Wallet / exchange / onchain position
                |
     Local risk engine (Papers 3,7,2,4,6)
                |
   ZK prover / TEE attestation / signed report
                |
   RiskPassport (SBT) + predicate registry
                |
  Lender | Insurer | DAO | Venue | Auditor
```

**Onchain contracts.**

- `PassportRegistry.sol`: passport owner, predicate set, expiry, and revocation.
- `Verifier.sol`: ZK verifier for the predicate circuit.
- `AttestationHub.sol`: trusted attestations for offchain data.
- `UnderwritingPool.sol`: capital for passport-based policies.
- `PolicyNFT.sol`: policy tied to a passport.
- `SelectiveDisclosure.sol`: auditor and regulator view keys.
- `PredicateRegistry.sol`: predicate definitions and methodology.

**Offchain services.**

- Local risk engine and data connectors.
- ZK circuit compiler and prover.
- TEE or attestation service for CEX data.
- Underwriting and pricing engine.
- Monitoring and alert service.

**Demo (six acts).**

1. **The privacy problem.** A trader has a 20x position and wants better terms, but will not reveal size or entry.
2. **The proof.** The local engine computes the risk, and the ZK proof shows: liquidation distance above 20%, leverage below 20x, venue score above 70, bad-debt probability below 2%. The judge sees the proof, not the position.
3. **The lender.** A mock lender reads the passport and lowers the collateral haircut. The rate improves.
4. **The insurer.** A mock insurer writes a parametric policy priced from the proven risk band. The premium is lower than the uninformed price.
5. **The stress.** A drawdown hits. The position is still above the proven liquidation distance; the payer monitors without revealing the position. A claim, if any, uses selective disclosure.
6. **The DAO.** A DAO proves treasury solvency with the same primitive. The judge sees a portable, privacy-preserving risk standard.

**Build order.**

1. Predicate schema and local risk engine.
2. ZK circuit for a small predicate set.
3. Passport registry and verifier.
4. Attestation path for CEX data.
5. Mock lender and insurer integrations.
6. Underwriting pool and policy.
7. Selective disclosure and claims.
8. Monitoring and expiry.
9. Security audit of the circuit and contracts.
10. Documentation and trust-model disclosure.

**Team and agent split.**

- Human 1: ZK and cryptography lead.
- Human 2: risk engine and predicate lead.
- Human 3: protocol and underwriting lead.
- Human 4: product, privacy, and demo lead.
- Agent 1: local risk engine.
- Agent 2: circuit and prover.
- Agent 3: passport and verifier contracts.
- Agent 4: attestation and data connectors.
- Agent 5: underwriting pool and policy.
- Agent 6: frontend, selective disclosure, and docs.

**Judge Q&A.**

- **"How do you prove a CEX position?"** You cannot prove it purely onchain without a trusted bridge. We support three modes: onchain ZK, TEE attestation, and signed exchange reports, each with a clear trust label. We do not pretend they are equivalent.
- **"Why not just reveal the position?"** Privacy is a feature: traders and DAOs will not share size, entry, or strategy. A proof of the relevant predicate is enough for underwriting.
- **"What if the proof is stale?"** The passport has an expiry and a refresh requirement. A lender can require a recent proof.
- **"Is the risk model hidden?"** The methodology is public; the inputs are private. The proof verifies the predicate, not a black-box score.
- **"What is the moat?"** The predicate standard, the circuit library, the attestation network, and the underwriting integrations.

**Cons (hard truths).**

- **ZK complexity.** Proving a full risk model in a circuit is hard. Start with a small predicate set and a clear trust boundary.
- **Data authenticity.** A ZK proof is only as good as the private inputs. If the position data come from a CEX, you need a trusted attestation. That is a weaker trust model, and the pitch must say so.
- **Proving cost.** A heavy circuit is slow and expensive. Use a focused predicate and an optimized prover.
- **Privacy vs regulation.** Selective disclosure and view keys are necessary, but they complicate compliance and user expectations.
- **Adoption.** Lenders and insurers need to accept the passport. Start with one integration and a clear economic benefit.
- **Gaming.** A user can choose a favorable snapshot or a favorable venue score. The passport must bind to a timestamp, a venue, and a position, and the predicate must include the relevant risk horizon.
- **"Solution looking for a problem" perception.** The value is concrete: better collateral terms, lower insurance premiums, and privacy. Show the price difference, not just the proof.
- **Security.** A circuit bug or a verifier bug is catastrophic. Audit and test extensively.

**Kill criteria.**

- The circuit cannot prove the core predicate set.
- The data authenticity model is not credible.
- No lender or insurer will consume the passport.
- The privacy claim is weaker than the pitch implies.

**Score.** Novelty 5, technical depth 5, demo 4, feasibility 3, judge appeal 5, moat 4, public good 5. **31/35.**

---

## 5. Bonus ideas

These are not in the main ten, but they are strong enough to keep in the notebook. They can become features or side demos.

### 5.1 PERP WAR GAMES

**One sentence.** A live adversarial arena where red-team agents attack perp designs and defenders patch them, with an onchain leaderboard and bounties.

**Why it is crazy.** It turns risk research into a spectator sport and a continuous bug bounty. It feeds Perp Court with exploits and gives the community a reason to care.

**Workflow.** A venue submits a design. Red-team agents run attacks: funding-timing games, oracle manipulation, mark delay, cap evasion, liquidity crunch. Each attack produces a reproducible exploit. Defenders patch. The arena scores the design and the attack. A leaderboard and a bounty pool reward the best attacks and the most robust designs.

**Cons.** It can be entertainment without a product; the attacks can overfit the test suite; the legal and ethical boundaries of attacking a live venue are unclear; the scoring is subjective; the demo needs a real exploit to be credible.

**Best use.** A demo layer for Perp Court, not a standalone entry.

### 5.2 PERP SOVEREIGN

**One sentence.** A Shiller-style launchpad for perpetual claims on illiquid real-world assets, with legal wrappers, risk certificates, and oracle design.

**Why it is crazy.** It applies the original Shiller vision to RWAs: private companies, real estate, carbon, compute, and energy. It is a new market for assets that cannot be tokenized directly.

**Workflow.** Choose an RWA. Design the payoff and the oracle. Calibrate the funding and the mark. Run the legal and disclosure analysis. Launch a certified perp with a risk certificate and a stress bond.

**Cons.** Legal and regulatory complexity is severe; the oracle is the hard part; liquidity is thin; the asset may be unhedgeable; the hackathon version cannot solve the legal wrapper; judges may see it as a thought experiment.

**Best use.** A track-specific entry or a stretch instrument inside Perp OS.

### 5.3 PERP LENDER OF LAST RESORT

**One sentence.** A risk-priced liquidity backstop for perp venues and hedgers during liquidation cascades, with a stability fund and a clear repayment waterfall.

**Why it is crazy.** It is the "central bank for perps": a last-resort lender that prevents fire sales and socialized losses. It uses the cascade index to price and trigger liquidity.

**Workflow.** A venue or hedger posts collateral and a risk score. The backstop extends a credit line with a risk-based rate and a draw trigger. During a cascade, the backstop provides liquidity, takes a senior claim, and unwinds as the market stabilizes. A stability fund absorbs first losses.

**Cons.** Capital intensity; moral hazard; adverse selection; regulatory perimeter; the backstop can become the systemic risk it is meant to reduce; the hackathon version cannot demonstrate real capital.

**Best use.** A long-term vision slide, not a hackathon build.

---

## 6. Comparison matrix

| Idea | Core primitive | Papers | Demo | Depth | Moat | Feasibility | Public good | Score |
|---|---|---|---|---|---|---|---|---|
| Perp Nervous System | Cascade-risk oracle + stress bonds | 2,3,4,5,6,7 | 5 | 5 | 5 | 4 | 5 | 34 |
| Perp Court | Adversarial certification + bonds | 1-7 | 5 | 5 | 5 | 4 | 5 | 34 |
| Perp OS | Self-tuning perp kernel + certificate | 1,2,3,4,6,7 | 5 | 5 | 5 | 3 | 4 | 32 |
| Perp CDX | Tranched liquidation-tail CDS | 2,3,4,5,6,7 | 5 | 5 | 4 | 3 | 5 | 32 |
| Perp Genome | Evolutionary funding-rule arena | 1,2,4,6,7 | 4 | 5 | 5 | 4 | 4 | 31 |
| Perp Markets | Risk-factor exchange + Perp VIX | 1,2,5,6,7 | 5 | 5 | 5 | 3 | 4 | 32 |
| Perp Twin | Digital twin + scenario marketplace | 1-7 | 5 | 5 | 4 | 4 | 5 | 32 |
| Perp Safe | Risk-adjusted execution + insurance | 2,3,5,6,7 | 5 | 4 | 4 | 4 | 4 | 30 |
| Perp Constitution | Bonded market-design SLA | 2,4,5,6,7 | 4 | 4 | 4 | 4 | 5 | 30 |
| Perp Passport | ZK risk and solvency attestations | 2,3,4,6,7 | 4 | 5 | 4 | 3 | 5 | 31 |

**Reading the matrix.** Perp Nervous System and Perp Court are the two strongest standalone ideas. Perp CDX is the strongest "consumer payout" idea. Perp OS and Perp Markets are the strongest "new market" ideas but have higher build risk. The recommended portfolio combines the top ideas rather than choosing one.

---

## 7. Recommended clean-sweep portfolio: The Perp Safety Grid

Do not present ten disconnected projects. Present one system with three modules and one certification engine:

> **The Perp Safety Grid: Sense. Certify. Insure.**

### Module 1: Perp Nervous System (sense)

- Live cross-venue data plane.
- Cascade-risk index, venue stress scores, transmission graph.
- Onchain oracle and alert bus.
- Consumer adapters: lending, perp DEX, DAO vault, insurer.
- Stress bonds for venues and consumers.

### Module 2: Perp OS (launch and certify)

- Payoff compiler and funding/margin/mark controllers.
- Risk certificate and stress bond.
- One flagship instrument: **Perp VIX Perp** or **Slashing Perp**.
- Perp Court is the certification engine inside this module; it runs the test suite and issues the certificate.
- The market is self-tuning inside pre-committed bounds.

### Module 3: Perp CDX (insure)

- Single-name and index protection for liquidation tails.
- Tranched capital vault and claims oracle.
- Perp VIX cover for DAOs.
- Pricing from the same tail model used by Perp OS and the Nervous System.

### Why this combination wins

| Criterion | How the grid wins |
|---|---|
| Novelty | First systemic-risk sensor, adversarial certifier, self-tuning perp OS, and perp CDS index in one stack |
| Technical depth | Seven papers, real formulas, real data, real contracts, historical replay |
| Demo | A cascade is sensed, a safe market is launched, the tail is insured, the naive market breaks |
| Problem | Liquidation cascades, hidden bad debt, opaque funding, no standards |
| Public good | Open methodology, open-source risk engine, composable oracles and certificates |
| Commercial | Oracle fees, certification fees, license fees, insurance spread, integrations |
| Moat | Data, methodology, standard, integrations, track record |
| Sponsor independence | Chain-agnostic, no proprietary SDK, public data, Ethereum/L2 deployment |
| Judge recall | "We built the safety grid for perps: sense the cascade, certify the market, insure the tail" |
| Claim discipline | Testnet, confidence intervals, model-risk disclosures, no risk-free claims |

**The one-sentence pitch.**

> **"We built the missing safety grid for the $90T perpetual market: a nervous system that senses cascades, a court that certifies designs, a compiler that launches safe markets, and an insurance market that covers the tail."**

---

## 8. Team and agent split for the grid

### Humans

**Human 1: Quant and risk lead.**
Owns the cascade index, funding/basis models, liquidation tail, mark quality, and certificate methodology. Validates every formula against the papers. Defines confidence intervals and claim boundaries.

**Human 2: Protocol lead.**
Owns the Solidity: cascade oracle, stress bond, perp OS factory, controllers, certificate, CDX contracts, capital vault, claims oracle. Owns security, invariants, and deployment.

**Human 3: Data and simulation lead.**
Owns CEX/DEX ingestion, normalization, entity graph, historical replay, scenario library, and backtests. Owns the reproducibility package.

**Human 4: Product, frontend, and narrative lead.**
Owns the live board, the trial UI, the authoring UX, the insurance marketplace, the six-act demo, and the judge narrative.

### Agents

| Agent | Responsibility |
|---|---|
| Agent 1 | CEX/DEX data ingestion, normalization, and entity graph |
| Agent 2 | Funding, basis, and cascade-risk engine |
| Agent 3 | Liquidation-tail, mark-quality, and depth engine |
| Agent 4 | Onchain oracles, alert bus, stress bonds, and adapters |
| Agent 5 | Perp OS compiler, controllers, certificate, and generated market |
| Agent 6 | CDX contracts, tranche vault, claims oracle, and policy NFT |
| Agent 7 | Perp Court test suite, evidence registry, and verdict engine |
| Agent 8 | Frontend, charts, replay, and demo polish |
| Agent 9 | Security, fuzzing, invariants, and generated-code tests |
| Agent 10 | Documentation, reproducibility, and demo video |

If only 4-6 agents are available: merge 1+2, 3+7, 5+9, and 6+10. The critical path is data + risk engine + oracle + one flagship instrument + one payout.

---

## 9. Six-act demo

**Act 1: The invisible risk.**
A live board shows a 20x SOL position and a venue with a slow mark, a funding cap that never binds, and thin depth. The Perp Nervous System shows a rising cascade-risk index and a red mark-quality subscore. The judge sees that the risk is not the price; it is the plumbing.

**Act 2: The sensor.**
A BTC drawdown and a funding spike hit the data plane. The cascade index rises before the simulated liquidations. The transmission graph highlights the venue with the thinnest depth and the slowest mark. A mock lending protocol reads the oracle and lowers the collateral factor. A mock vault closes a basis trade. A venue's stress bond enters "at risk."

**Act 3: The trial and the launch.**
The team puts the venue's mark design on trial in Perp Court. The crossing-conditioned test shows the TWAP is about 1.6x worse than the naive variance heuristic. The court issues a conditional verdict. Perp OS compiles a fixed market: a Perp VIX Perp or a Slashing Perp, with adaptive funding, dynamic margin, and a risk certificate. The market is deployed to testnet. The certificate is minted. The stress bond is posted.

**Act 4: The stress.**
A simulated cascade hits. The naive market breaks: the cap never binds, the mark delay hides the loss, bad debt spikes. The certified market survives: the margin controller adjusts inside its bound, the funding controller retunes, the certificate updates. The judge sees the before/after.

**Act 5: The insurance.**
A trader's hidden liquidation loss is verified by the claims oracle. Perp CDX pays. A DAO's Perp VIX cover pays. The vault's equity tranche takes the first loss; senior capital is intact. The judge sees the tail transfer.

**Act 6: The proof.**
Every score, verdict, certificate, bond, and payout is onchain and traceable to a paper and a public data point. The judge can click any number and see the input, the formula, and the confidence. The one-sentence pitch lands.

---

## 10. Judge Q&A for the grid

**"Isn't this too many things?"**
It is one system with three modules that share the same data and risk engine. Sense, certify, insure. Each module is independently useful, and the demo tells one story.

**"What is the new primitive?"**
The safety grid itself: a cascade oracle with financial consequences, an adversarial certification with a bond, a self-tuning perp kernel, and a tranched liquidation-tail market. No single dashboard or DEX has this stack.

**"Is this real or a simulation?"**
The data are real public data. The contracts are on testnet. The stress scenarios are labeled as scenarios. The models are calibrated and the results have confidence intervals. We do not claim to predict actual customer liquidations.

**"Why would venues participate?"**
Better insurance and lending terms, a credible safety signal, a bond that differentiates them, and a standard their users can verify. A venue that refuses is visible by its absence.

**"How do you prevent gaming?"**
Multi-source data, median/trimmed aggregation, methodology hashes, confidence intervals, dispute windows, stress bonds, and a track record. No single input can move the index or the verdict.

**"What is the moat?"**
The cross-venue data, the calibrated risk engine, the test library, the certificate standard, the bond market, and the integration network. Trust and coverage compound.

**"What is the legal boundary?"**
The hackathon version is a testnet research prototype. It does not custody funds, does not sell insurance, and does not provide investment advice. A production version requires jurisdiction-specific legal, regulatory, and capital analysis. We state this explicitly.

**"What if the model is wrong?"**
Every output has a confidence interval and a model-risk disclosure. The certificate expires. The bond is capped. The system has an "unknown" state. We do not claim risk-free or guaranteed.

**"What do you build first?"**
The Nervous System and the risk engine, because they feed every other module. Then one flagship instrument in Perp OS, then one CDX payout. The Court certification engine is built alongside the OS certificate.

**"Why Ethereum?"**
Ethereum is the neutral trust layer for composable oracles, certificates, bonds, and insurance. The system can deploy on an L2 for cost and settle attestations on Ethereum. It is chain-agnostic by design.

---

## 11. Consolidated cons and failure modes

This section exists because the HACKATHONN mandate asks for the cons of every idea. The per-idea cons are above. These are the cross-cutting failure modes that can sink the whole entry.

1. **The "dashboard" trap.** If the judge sees charts but no onchain action, the project is a dashboard. Every module must change a contract state, a collateral factor, a certificate, a bond, or a payout.
2. **The "simulation" trap.** If the stress is a movie, the judge will not believe it. Use real data, deterministic scenarios, reproducible results, and a clear label.
3. **The "too many modules" trap.** A three-module grid can feel unfocused. The six-act demo must tell one story, and the modules must share a single risk engine.
4. **The "model risk" trap.** The papers are honest about their limitations. The pitch must be equally honest. Do not claim to predict actual liquidations, guarantee safety, or eliminate risk.
5. **The "data gap" trap.** Public data do not reveal user-level collateral, historical marks, or liquidation paths. Label the gap and use attested data where needed.
6. **The "oracle manipulation" trap.** A risk oracle that can be pushed around is worse than no oracle. Use robust aggregation, delays, dispute windows, and bonds.
7. **The "legal" trap.** Insurance, derivatives, routing, and certification all have regulatory perimeters. Keep the hackathon version testnet-only and state the boundary.
8. **The "cold start" trap.** A two-sided market (CDX, risk-factor exchange, scenario marketplace) needs liquidity. Seed it with a vault or a committed counterparty, or demo a single transaction.
9. **The "complexity" trap.** A compiler, a court, a cascade model, and a CDX is a lot. The UI must hide the complexity and expose the one decision that matters.
10. **The "demo failure" trap.** Live APIs fail. Have a recorded fallback, a deterministic replay, and a local data snapshot. The demo must work offline.

**Global kill criteria.**

- The project cannot show a before/after where a contract changes behavior.
- The risk engine is a black box with no formula or confidence.
- The certificate or bond has no financial consequence.
- The team cannot explain the claim boundary in one sentence.
- The demo depends on a live API that is not cached.

---

## 12. Build order without clocks

The order is dependency-driven. Do not start a module until its inputs exist.

1. **Data spine.** Ingest and normalize funding, mark, index, depth, and liquidation data for one venue and one asset. Cache a historical snapshot. This is the foundation for every module.
2. **Risk engine v0.** Funding/basis band (Papers 2, 4, 6), liquidation tail (Papers 3, 7), mark quality and depth (Paper 7). Produce a signed report with confidence.
3. **Cascade index v0.** Aggregate the risk engine into a venue score and a system index. Backtest on a historical episode. Publish lead time and error rates.
4. **Oracle and alert bus.** Put the index onchain with a methodology hash and a dispute window. Build one consumer adapter.
5. **Perp Court v0.** Evidence schema, reconstruction, and the funding/basis/liquidation test suite. Run one real case end to end.
6. **Perp OS v0.** Payoff DSL, funding controller, margin controller, mark controller, certificate, and factory. Deploy one flagship instrument on testnet.
7. **Stress bond v0.** Bond terms, slashing conditions, and a compensation waterfall. Connect it to the Court verdict and the OS certificate.
8. **Perp CDX v0.** One reference entity, one trigger, one policy, one claims oracle, one capital vault, one payout.
9. **Integrations.** Mock lending protocol, mock insurer, mock DAO, and a wallet or aggregator. Each must read a real oracle or certificate.
10. **Demo and hardening.** Six acts, cached data, fallback recording, invariant tests, generated-code tests, and a reproducibility package.
11. **Open-source release.** Methodology, contracts, data pipeline, and a one-command reproduction guide. This is part of the public-good story.

---

## 13. Final recommendation

If the team can build one thing: **Perp Nervous System**. It is the most category-defining, data-moated, and immediately useful idea. It creates a new primitive, has a dramatic demo, and every other module can consume it.

If the team can build two things: **Perp Nervous System + Perp Court**. Sense the risk, then certify the design. The court gives the sensor teeth and the certificate gives the ecosystem a standard.

If the team can build three things: **Perp Nervous System + Perp OS + Perp CDX**, with Perp Court as the certification engine inside Perp OS. This is the full **Perp Safety Grid**: sense the cascade, launch the safe market, insure the tail, and prove it onchain.

The one-sentence pitch:

> **"We built the safety grid for the $90T perpetual market: a nervous system that senses cascades, a court that certifies designs, a compiler that launches safe markets, and an insurance market that covers the tail."**

That is a sentence a judge will remember, a demo a judge will feel, and a primitive the ecosystem can build on after the weekend.
