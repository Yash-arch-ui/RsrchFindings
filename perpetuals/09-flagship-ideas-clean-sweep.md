# Clean-Sweep Flagship Ideas: The Perp Risk Bureau

## 0. The thesis in one paragraph

The seven papers point to the same gap. Perpetual futures are the largest crypto derivative market, but the market has **no credit rating, no stress test, no mark-quality standard, no liquidation-tail insurance, and no systemic fear index**. Every venue copies a funding template, every trader guesses at liquidation risk, every lender treats perp collateral as if it were spot, and every insurance product is bespoke. The winning ETHGlobal project is not another perp DEX. It is the **trust layer for perpetual markets**: a compiler that launches perps with a risk certificate, an oracle that rates them, a systemic risk index that anyone can hedge, and an insurance market that covers the tail. In one line: **"Moody's + VIX + FDIC for perpetual futures."**

This is a category-creation play, not a feature play. It uses all seven papers, real public data, and a live onchain demo. It is chain-agnostic, sponsor-agnostic, open-source, and useful to every perp venue, lender, DAO, insurer, and regulator. That is what a general-competition clean sweep looks like.

## 1. What general-competition judges actually reward

From repeated ETHGlobal general-competition winners, the pattern is consistent:

1. **A new primitive, not a clone.** "Another perp DEX" loses to "the first credit rating agency for perps."
2. **A live, legible demo.** The judge must see the thing work in under five minutes, with real data and a clear before/after.
3. **A problem the judge already believes in.** Liquidation cascades, opaque risk, and bad debt are universally understood.
4. **Technical depth that is visible.** Real math, real oracles, real contracts, real tests. Not a wrapper around an API.
5. **A public-good or ecosystem primitive.** Something other teams can build on after the weekend.
6. **A credible team and a credible claim boundary.** No "risk-free," no fake yields, no production claims.
7. **A story that fits in one sentence.** "We built the trust layer for the $90T perp market" is a sentence a judge can repeat.
8. **No dependence on a sponsor's SDK.** The project wins on its own merits; sponsor tracks are upside, not the thesis.

The ideas below are designed against those eight criteria.

## 2. The world context that makes this urgent

- Perpetual futures dominate crypto derivatives. Paper 5 cites about 93% of crypto futures volume and over USD 90 trillion cumulative volume.
- Centralized exchanges still dominate, but onchain perps are growing. Hyperliquid alone was processing about USD 8.6 billion daily notional as of May 2026; Drift and dYdX v4 intermediated about USD 370 billion cumulative notional on BTC/ETH/SOL over the windows studied in Paper 6.
- Liquidation risk is enormous. Paper 3 cites almost USD 80 billion liquidated in 2021, over USD 200 million per day on average, and USD 8.6 billion on 19 May 2021 alone.
- Funding is not a neutral implementation detail. Paper 4 shows the same template produces different effective feedback. Paper 6 shows the same no-arbitrage theory is testable on Drift and vacuous on dYdX because of funding timing and cap design.
- Marks are not the price. Paper 7 shows a delayed mark can hide a `sqrt(delta)` overshoot and a bad-debt tail in `sigma sqrt(delta)/m`; a "safer" TWAP can be 1.6x worse at the liquidation crossing.
- Cross-venue execution is not free. Paper 5 shows 17% of observations look like arbitrage, but only 8 of 20 top trades are profitable after costs; the average Sharpe is -7.40.
- Restaking, RWA perps, prediction perps, and AI-agent trading are creating new exposures that existing risk tooling does not cover.
- Regulators and institutions increasingly ask for disclosure, stress testing, and risk ratings. There is no standard for perps.

The market has price oracles. It does not have **risk oracles**. That is the opening.

## 3. The three flagship ideas

### 3.1 Flagship 1 — PerpScore: The Onchain Credit Rating Agency for Perpetual Markets

**One sentence.** PerpScore rates every perp venue, market, and position on funding integrity, basis integrity, liquidation tail, mark quality, execution quality, and survival, and publishes the rating onchain for lenders, insurers, DAOs, and regulators.

**The problem.** A lender cannot tell whether a perp LP token or a hedged position is safe collateral. An insurer cannot price liquidation cover. A DAO cannot compare venues. A regulator cannot see systemic risk. A trader sees a funding rate, not a risk grade. There is no Moody's for perps.

**The primitive.** A standardized, methodology-hashed, onchain **PerpScore** (0-100) with six subscores:

1. **Funding Integrity.** Is funding observable at entry or forward-looking? Does the cap ever bind? What is the effective feedback coefficient, stability band, and cap-binding risk? (Papers 4 and 6.)
2. **Basis Integrity.** What is the no-arbitrage band, executable spread, reversal hazard, and CEX-DEX lead-lag? (Papers 2 and 5.)
3. **Liquidation Tail.** At reference leverage (10x/20x/50x), what is the breach probability, bad-debt probability, catch-up overshoot, and funding-memory premium? (Papers 3 and 7.)
4. **Mark Quality.** What is the mark delay or smoothing window, crossing-conditioned overshoot, TWAP penalty, and disclosure quality? (Paper 7.)
5. **Execution Quality.** What is the depth, average concession, failure rate, and venue concentration? (Paper 7 and Paper 5.)
6. **Survival.** For a hedged position, what is the liquidation probability and hedge effectiveness under stress? (Paper 3.)

Each subscore comes with a confidence interval, data provenance, and a model-risk disclosure. The overall score is not a black box: the judge can click any subscore and see the inputs, formulas, and historical replay.

**The killer feature: Perp VIX.** Aggregate the scores into a single **Perp VIX**: a real-time systemic liquidation-risk index for the perp market. It rises when funding is stressed, basis is dislocated, liquidation tails widen, mark latency increases, and depth thins. It is the first fear index for perps. It can be published onchain, charted, hedged, and insured. This is the memorable headline: **"We built the VIX for perpetual futures."**

**What it uses from the papers.**
- Paper 2: no-arbitrage band, clamp-aware benchmark.
- Paper 3: liquidation probability and survival.
- Paper 4: effective funding feedback and stability band.
- Paper 5: CEX-DEX lead-lag, executable spread, reversal hazard.
- Paper 6: funding timing and bound testability.
- Paper 7: mark delay, TWAP penalty, bad-debt tail, depth.
- Paper 1: the general payoff/funding language that lets the score extend to exotic perps.

**Architecture.**

```
Data layer
  CEX/DEX APIs: prices, funding, OI, volume, liquidations, mark/index, depth
  Onchain indexers: Drift, dYdX, Hyperliquid, GMX, Ostium, Vertex, Aevo
  Options/stablecoin context: Deribit, DeFiLlama
        |
Risk engine (off-chain, reproducible)
  Funding Integrity  -> Papers 4 + 6
  Basis Integrity    -> Papers 2 + 5
  Liquidation Tail   -> Papers 3 + 7
  Mark Quality       -> Paper 7
  Execution Quality  -> Papers 5 + 7
  Survival           -> Paper 3
  Perp VIX           -> weighted composite
        |
Oracle layer (onchain)
  PerpScoreOracle.sol
  PerpVIXOracle.sol
  MethodologyRegistry.sol
  DisputeManager.sol
        |
Consumers
  Lending protocol: collateral factors
  Insurer: premium multiplier
  DAO: treasury allocation limits
  Perp DEX: listing and risk dashboard
  Regulator: disclosure portal
  Trader: position risk grade
```

**Demo.**

Act 1: Live scoreboard of Hyperliquid, dYdX, Drift, GMX, Ostium, and a CEX benchmark. Each venue has six subscores and a PerpScore. The Perp VIX is charted.

Act 2: Historical replay of a stress episode. The Perp VIX rises before the cascade; a naive venue's Liquidation Tail subscore collapses; a well-designed venue's Mark Quality and Funding Integrity hold up. The judge sees the score lead the event.

Act 3: A mock lending protocol consumes the oracle. When a venue's PerpScore falls below a threshold, the collateral factor drops automatically. A mock insurer raises the premium. A DAO rebalances. The score is not a dashboard; it changes onchain behavior.

Act 4: The judge clicks a venue and sees the full decomposition: funding timing, cap-binding risk, band width, mark delay, TWAP penalty, depth concession, bad-debt probability. Every number is traceable to a paper and a public data point.

**Why it wins.**
- It is a new category: credit ratings for perps.
- It is a public good with a clear commercial model (API, oracle fees, integrations).
- It is composable: lending, insurance, DAOs, regulators, traders.
- The Perp VIX is a memorable, chartable headline.
- It uses all seven papers without feeling academic.
- It does not require launching another perp DEX or a token.

**Moat.** The methodology, the historical dataset, the cross-venue integrations, the calibration track record, and the standard itself. If PerpScore becomes the default risk rating, every venue wants to be rated and every lender wants to consume it.

**Risks.** Data quality, venue gaming, model risk, regulatory perception. Mitigations: multi-source data, signed reports, dispute windows, confidence intervals, methodology hashes, and a clear "not investment advice" boundary.

**Clean-sweep score.** Novelty 5, technical depth 5, demo 5, feasibility 5, judge appeal 5, moat 5, public good 5. Total 35/35.

---

### 3.2 Flagship 2 — Perp Forge: The Perpetual Market Compiler with Risk Certificates and Stress Bonds

**One sentence.** Perp Forge turns a payoff into a complete perpetual market — funding, hedge ratios, basis band, funding-timing verdict, mark design, liquidation-tail forecast, generated contracts, and a bonded risk certificate — then launches it with skin in the game.

**The problem.** Launching a perp is bespoke. Teams copy a funding template, pick a mark, set a margin, and hope. Paper 4 shows copied templates do not deliver the same feedback. Paper 6 shows a funding rule can be theoretically valid but empirically vacuous. Paper 7 shows a mark can hide a liquidation tail. There is no compiler that checks all of this before launch.

**The primitive.** A **risk-certified perp market**. Every market launched by Forge gets:

1. **Payoff and funding spec** from Paper 1.
2. **No-arbitrage band and basis index** from Paper 2.
3. **Liquidation-aware hedge and survival score** from Paper 3.
4. **Effective-funding calibration and stability band** from Paper 4.
5. **Execution-aware route and reversal hazard** from Paper 5.
6. **Funding-timing verdict and cap-binding audit** from Paper 6.
7. **Mark-quality and liquidation-tail distribution** from Paper 7.
8. **A risk certificate** (onchain SBT/NFT) with the certified metrics, methodology hash, confidence bands, and expiry.
9. **An optional stress bond**: the deployer posts a bond that is slashed or locked if the market violates its certified risk bounds (e.g., funding cap never binds, liquidation tail exceeds the certificate, mark delay exceeds disclosure, bad-debt probability above target).

The **stress bond** is the out-of-world primitive. It aligns incentives: if you launch a perp with a copied template and a hidden tail, you pay. If you launch a certified market, you earn trust, lower insurance premiums, and better lending terms. It is "skin in the game" for perp deployers.

**Flagship instrument.** Launch one novel perp that could not exist without the stack:

- **Slashing Perp**: perpetual cover on restaking slashing events, with jump funding (Paper 1) and liquidation-aware risk (Paper 3).
- **Prediction Perp**: continuous probability exposure with a bounded payoff and a funding-timing audit (Papers 1, 4, 6).
- **Perp VIX Perp**: a perp on the Perp VIX index from Flagship 1. This is the most "new asset class" option: a tradeable systemic risk hedge for the entire perp market.

Recommended for the clean sweep: **Perp VIX Perp**, because it ties Flagship 1 and 2 together and gives the demo a unique instrument: "the first tradeable fear index for perps."

**Architecture.**

```
Input
  Natural language or payoff DSL
  Asset / index / event
  Funding rule preferences
  Margin and mark constraints
        |
Compiler
  Parser -> symbolic engine -> funding/discount formulas (Paper 1)
  Basis band and no-arbitrage check (Paper 2)
  Liquidation-aware sizing (Paper 3)
  Effective-funding calibration (Paper 4)
  Execution route and reversal hazard (Paper 5)
  Funding-timing and cap audit (Paper 6)
  Mark-quality and tail forecast (Paper 7)
        |
Output
  Risk report + certificate
  Generated Solidity + tests
  Oracle spec + deployment script
  Stress-bond terms
  One-click testnet deploy
        |
Onchain
  PerpForgeFactory.sol
  PerpMarket.sol
  RiskCertificate.sol
  StressBond.sol
  Oracle adapters
```

**Demo.**

Act 1: Type "I want a perpetual on restaking slashing risk" or "a perp on the Perp VIX." Forge parses the payoff, emits the funding formula, hedge ratios, and generated contract.

Act 2: The risk certificate. Forge shows the funding-timing verdict (observable vs forward-looking), cap-binding risk, mark-quality score, liquidation tail at 10x/20x/50x, and the stress-bond terms. The judge sees exactly what could go wrong.

Act 3: Launch. Deploy the perp to testnet. Post the stress bond. Mint the certificate. Show the market live.

Act 4: Stress. A simulated slashing event or a VIX spike hits. The perp pays. The hedge survives. The certificate updates. The stress bond is intact because the market stayed within its certified bounds. A naive copied perp breaks.

**Why it wins.**
- It is a compiler, not a clone: technical depth and a platform story.
- It creates a new primitive: bonded, risk-certified perp markets.
- It has a live deployment and a novel instrument.
- It uses every paper at full depth.
- It gives judges a "before/after": naive perp vs certified perp under stress.
- It has a clear moat: the compiler, the certificate standard, the stress-bond mechanism, and the risk oracles.

**Moat.** The payoff library, the risk-certificate standard, the stress-bond terms, the generated-contract templates, and the integration with PerpScore/PerpShield.

**Risks.** Compiler safety, generated-code bugs, legal classification of bonds/insurance, oracle manipulation. Mitigate with a constrained DSL, invariant tests, human review, capped bonds, testnet-only claims.

**Clean-sweep score.** Novelty 5, technical depth 5, demo 5, feasibility 4, judge appeal 5, moat 5, public good 4. Total 33/35.

---

### 3.3 Flagship 3 — PerpShield: Liquidation-Tail Insurance and Perp CDS

**One sentence.** PerpShield is a parametric insurance market for perpetual liquidation tails: it prices cover using the PerpScore and TailMark distributions, and pays when a position or venue suffers a hidden liquidation loss beyond a disclosed threshold.

**The problem.** A trader can be liquidated at a price worse than the stated liquidation level because the mark is delayed. A venue can suffer bad debt when the mark catches up. An insurer cannot price this risk because there is no standardized tail model. There is no CDS for perp venues.

**The primitive.** A **liquidation-tail CDS**:

1. **Position cover**: pays if a covered position is liquidated with a realized loss beyond the mark-implied loss by more than a threshold, or if the mark delay causes a bad-debt event.
2. **Venue tail cover**: pays if a venue's bad-debt or liquidation-tail metric exceeds a threshold over a period.
3. **Perp VIX cover**: pays if the Perp VIX spikes above a strike, giving DAOs and funds a systemic hedge.

Pricing uses:
- `P_bd`, expected overshoot, TWAP penalty, funding-memory premium, depth concession (Paper 7).
- Breach probability and survival (Papers 3 and 7).
- Funding-timing/cap risk (Paper 4 and 6).
- Basis/reversal hazard (Papers 2 and 5).

**Architecture.**

```
PerpScore + PerpVIX oracles
        |
Pricing engine
  Expected loss = P_bd * severity * notional
  Risk margin = confidence buffer
  Capital cost = vault target return
  Premium = expected loss + risk margin + capital cost
        |
InsuranceVault.sol
  Senior / junior tranches
  PolicyNFT.sol
  ClaimsOracle.sol
  DisputeManager.sol
  CircuitBreaker.sol
        |
Claims
  Parametric trigger: verified bad-debt event, mark-delay event, or VIX threshold
  Dispute window
  Payout capped and collateralized
```

**Demo.**

Act 1: A 20x SOL position looks safe on a 1-minute mark. The user buys PerpShield cover for a 60-minute mark-delay event. The premium is priced from `P_bd = 0.135` and the severity distribution.

Act 2: A stress episode hits. The mark catches up; the position is liquidated with hidden loss; the claims oracle verifies the event; PerpShield pays. The trader survives; the vault remains solvent.

Act 3: A DAO buys Perp VIX cover. The VIX spikes; the parametric policy pays automatically. The DAO hedged systemic risk without shorting a specific asset.

Act 4: The vault dashboard shows premiums, loss ratios, capital, and the tail distribution. The judge sees that the product is priced from the same math that predicted the event.

**Why it wins.**
- It is consumer protection with a real payout.
- It is a new derivative (perp CDS) with a clear pricing kernel.
- It creates real yield for capital providers.
- It has an emotional demo: hidden liquidation loss becomes visible and covered.
- It is a natural consumer of PerpScore and TailMark, so the three flagships reinforce each other.
- It is a strong "public good + DeFi + insurance" narrative.

**Moat.** The pricing model, the claims oracle, the capital vault, the underwriting data, and the PerpScore integration.

**Risks.** Insurance regulation, adverse selection, moral hazard, oracle ambiguity, correlated events. Mitigate with parametric triggers, caps, waiting periods, exclusions for protocol bugs, and a testnet/research boundary.

**Clean-sweep score.** Novelty 5, technical depth 5, demo 5, feasibility 4, judge appeal 5, moat 4, public good 5. Total 33/35.

## 4. The combined clean-sweep entry: The Perp Risk Bureau

Do not present three disconnected projects. Present one system with three modules:

**"The Perp Risk Bureau: Rate. Launch. Insure."**

- **PerpScore** rates the market and publishes the Perp VIX.
- **Perp Forge** launches a certified perp (the Perp VIX Perp or Slashing Perp).
- **PerpShield** insures the tail.

The modules share data, oracles, and a single risk engine. The demo is a single story:

1. **Rate:** the live scoreboard shows a venue's PerpScore and the Perp VIX.
2. **Launch:** Forge compiles a new perp on the Perp VIX, with a risk certificate and a stress bond.
3. **Stress:** a simulated cascade hits; the VIX spikes; the certified perp pays; a naive perp breaks.
4. **Insure:** PerpShield pays the hidden liquidation loss; the DAO's VIX cover pays; the vault stays solvent.
5. **Prove:** every score, certificate, and payout is onchain and traceable to a paper and public data.

This is the "clean sweep" architecture: a public good (PerpScore), a platform (Forge), and a consumer product (PerpShield), all powered by the same risk engine.

## 5. Why this can win the general competition, not just a sponsor track

| Criterion | How the Perp Risk Bureau wins |
|---|---|
| Novelty | First credit rating, VIX, risk certificate, stress bond, and CDS for perps |
| Technical depth | Seven papers, real math, real oracles, real contracts, historical replay |
| Demo | Live scoreboard, VIX spike, certified perp launch, insurance payout, stress-bond slashing |
| Problem | Liquidation cascades, opaque risk, bad debt, no standards |
| Public good | Open methodology, open-source risk engine, composable oracles |
| Commercial | Oracle fees, API, certification fees, insurance spread, integrations |
| Moat | Data, methodology, standard, integrations, track record |
| Sponsor independence | Chain-agnostic, no proprietary SDK, deploy on Ethereum/L2, public data |
| Judge recall | "Moody's + VIX + FDIC for perps" |
| Claim discipline | Testnet, confidence bands, model-risk disclosures, no risk-free claims |

## 6. The out-of-world extensions

These are not separate projects; they are stretch features that make the flagship feel inevitable.

### 6.1 Perp VIX futures and options

Once the Perp VIX exists, the natural next step is a full volatility surface for perps: VIX futures, VIX options, and VIX-linked structured products. The Perp VIX Perp is the first step. This creates a new asset class: **systemic risk as a tradeable asset**.

### 6.2 Stress Bonds

A deployer posts a bond that is slashed if the market violates its risk certificate. This is a new accountability primitive. It can be extended to:
- Venue bonds for oracle uptime.
- Insurance-fund bonds for bad-debt coverage.
- Governance bonds for parameter changes.

### 6.3 Proof of Survival

A periodic onchain "stress certificate" that proves a perp market survives a library of historical and synthetic scenarios. It is like a bank stress test, but for perps. Consumers can require a valid certificate before listing, lending, or insuring.

### 6.4 Liquidation-tail CDS index

A basket of venue-tail CDS contracts, like a CDX for perps. It lets funds hedge systemic liquidation risk and lets insurers lay off correlated exposure.

### 6.5 Risk-weighted perp aggregator

A router that chooses a venue not by price alone, but by price × PerpScore × executable spread. It is the "best execution" layer for perps, with a risk-adjusted score. This is a natural consumer of TierBridge and PerpScore.

### 6.6 Autonomous risk DAO

An AI-assisted risk committee that monitors PerpScore, updates parameters, and buys insurance. The deterministic risk engine does the math; the AI explains and proposes; humans approve. This is a governance primitive, not a trading bot.

### 6.7 Perp Passport

A portable, privacy-preserving risk score for a wallet's perp positions. It could be used by lenders, insurers, and DAOs. Zero-knowledge proofs could let a user prove a risk grade without revealing positions. This is a strong ZK/privacy extension.

## 7. The 5-act demo script

**Act 1: The invisible risk.**
- A live 20x SOL position looks healthy on a 60-minute mark.
- The judge sees the PerpScore: Liquidation Tail 31/100, Mark Quality 22/100, Funding Integrity 74/100.
- The Perp VIX is rising.

**Act 2: The rating.**
- The scoreboard compares venues. One venue's score drops before a historical cascade.
- A mock lending protocol automatically lowers its collateral factor.
- A mock insurer raises its premium.
- The judge sees the rating change behavior, not just a chart.

**Act 3: The launch.**
- The team types a payoff: "a perp on the Perp VIX."
- Forge compiles the market, emits the funding formula, hedge ratios, mark design, risk certificate, and stress bond.
- Deploy to testnet. Mint the certificate. Show the live market.

**Act 4: The stress.**
- A simulated cascade hits. The VIX spikes. The certified perp pays.
- A naive copied perp breaks: funding cap never binds, mark delay hides the loss, bad debt spikes.
- The stress bond is intact for the certified market; the naive market's bond is slashed.

**Act 5: The insurance.**
- A trader's hidden liquidation loss is verified by the claims oracle.
- PerpShield pays. A DAO's VIX cover pays. The vault remains solvent.
- The judge sees the full loop: rate → launch → stress → insure → prove.

## 8. Judge Q&A for the combined entry

**"Isn't this just a dashboard?"**
No. The dashboard is the UI. The product is three protocol primitives: an onchain rating oracle, a risk-certified perp factory with stress bonds, and a parametric insurance vault. The score changes lending collateral, insurance premiums, and payouts.

**"Why would a venue submit to a rating?"**
Better lending terms, lower insurance premiums, user trust, listing requirements, and a competitive moat. Ratings can start permissionless from public data; venues can later submit private data for a higher-confidence score.

**"How do you prevent gaming the rating?"**
Multi-source data, signed reports, methodology hashes, confidence intervals, dispute windows, and a track record. A venue that games one input will fail the cross-checks. Stress bonds add skin in the game.

**"How do you price the Perp VIX?"**
A transparent composite of funding stress, basis dislocation, liquidation tail, mark quality, depth, and execution friction. The weights and formulas are published and backtested. The index is a risk indicator, not a prediction of a specific price.

**"Is the insurance legal?"**
The hackathon version is a testnet research prototype with parametric triggers and no real funds. A production version requires jurisdiction-specific licensing, capital, and disclosures. We are explicit about that boundary.

**"What if the model is wrong?"**
Every score has a confidence band and a model-risk disclosure. The insurance vault has senior/junior tranches, caps, and a capital buffer. The stress bond is capped. We do not claim risk-free.

**"Why Ethereum?"**
Ethereum is the neutral trust layer for composable oracles, certificates, and insurance. The system can deploy on an L2 for cost and still settle proofs/attestations on Ethereum. It is chain-agnostic by design.

**"What is the moat?"**
The methodology, the historical dataset, the cross-venue integrations, the certificate standard, and the track record. A rating agency's moat is trust and coverage; both compound.

**"What do you build first?"**
PerpScore + Perp VIX, because it is the data and trust layer. Then Forge for a live certified perp. Then PerpShield for the consumer payout. The modules are separable, so the demo works even if one is cut.

## 9. Team split for 4-5 humans and 7-8 coding agents

### Humans

**Human 1 — Quant and risk lead.**
Owns the risk engine: funding integrity, basis, liquidation tail, mark quality, Perp VIX weights. Validates every formula against the papers. Defines confidence intervals and model-risk disclosures.

**Human 2 — Protocol lead.**
Owns Solidity: `PerpScoreOracle`, `PerpVIXOracle`, `PerpForgeFactory`, `PerpMarket`, `RiskCertificate`, `StressBond`, `InsuranceVault`, `PolicyNFT`, `ClaimsOracle`. Owns security, invariants, and deployment.

**Human 3 — Frontend and demo lead.**
Owns the scoreboard, Perp VIX chart, Forge UI, certificate viewer, insurance marketplace, stress-bond dashboard, and the five-act demo.

**Human 4 — Data and oracle lead.**
Owns CEX/DEX ingestion, funding/mark/depth/liquidation data, historical replay, backtests, signed reports, and oracle updates.

**Human 5 — Product, narrative, and partnerships.**
Owns the pitch, the "Moody's + VIX + FDIC for perps" story, judge Q&A, integrations with mock lending/DAO/insurer, and the open-source release.

### Agents

| Agent | Responsibility |
|---|---|
| Agent 1 | Data ingestion and normalization across CEX/DEX |
| Agent 2 | Funding/basis risk engine (Papers 2, 4, 6) |
| Agent 3 | Liquidation-tail and mark-quality engine (Papers 3, 7) |
| Agent 4 | Oracle contracts and signed-report infrastructure |
| Agent 5 | Perp Forge compiler and generated perp contracts |
| Agent 6 | Insurance vault, policy NFT, claims oracle |
| Agent 7 | Frontend components, charts, and demo polish |
| Agent 8 | Backtest, historical replay, stress scenarios, and docs |

If an extra agent is available, add **Agent 9: security/fuzzing** and **Agent 10: demo video**.

## 10. Build order without time pressure

1. **Risk engine v0:** funding integrity, basis integrity, liquidation tail, mark quality, and Perp VIX on historical data.
2. **PerpScore oracle v0:** signed reports, methodology registry, scoreboard.
3. **Historical replay:** show the score leading a cascade; calibrate confidence.
4. **Perp VIX:** aggregate index, chart, and historical backtest.
5. **Perp Forge v0:** payoff parser, funding compiler, risk certificate, generated contract for one flagship.
6. **Stress bond:** bond terms, slashing conditions, certificate registry.
7. **PerpShield v0:** pricing engine, vault, policy NFT, claims oracle, one parametric cover.
8. **Integrations:** mock lending protocol, mock insurer, mock DAO.
9. **Demo:** five acts, live data, testnet contracts, fallback recordings.
10. **Open-source release:** methodology, contracts, data pipeline, and a reproducibility guide.

## 11. What not to build

- Do not build another perp DEX. The market does not need a 30th clone.
- Do not build a generic AI trading bot. It is crowded and hard to trust.
- Do not build a dashboard without an oracle or a payout. Dashboards do not win general competitions.
- Do not issue a token. It distracts from the primitive and invites the wrong questions.
- Do not depend on a single sponsor's SDK. The thesis must stand alone.
- Do not claim risk-free yield, guaranteed survival, or production readiness.
- Do not hide the model assumptions. The honesty is part of the pitch.

## 12. Final recommendation

If the team can build one thing: **PerpScore + Perp VIX**. It is the most category-defining, data-moated, composable idea. It wins on public good, technical depth, and a memorable headline.

If the team can build two things: **PerpScore + Perp Forge**. Add a live certified perp (the Perp VIX Perp or Slashing Perp) and a stress bond. This gives the demo a product and a "before/after" under stress.

If the team can build three things: **PerpScore + Perp Forge + PerpShield**. This is the full Perp Risk Bureau: rate, launch, insure. It is the clean-sweep entry, and it is feasible with 4-5 humans and 7-8 agents because the modules share the same data and risk engine.

The one-sentence pitch:

> **"We built the trust layer for the $90T perpetual market: a credit rating, a VIX, a certified launchpad, and insurance for the liquidation tail."**

That is a sentence a judge will remember, a demo a judge will feel, and a primitive the ecosystem can build on after the weekend.
