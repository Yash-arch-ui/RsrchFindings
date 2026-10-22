# Hackathon Idea Lab

Deep ideation built from the five perpetuals papers. The goal is not to list generic DeFi ideas. The goal is to find primitives that the math makes possible, that appear unbuilt or underexplored, and that can be demonstrated in a way judges remember. Sections 1-12 were built from Papers 1 and 2. Sections 13-17 add the liquidation and funding-feedback layer from Papers 3 and 4. Sections 18-22 add the cross-venue execution and two-tier market layer from Paper 5 and supersede the earlier final recommendations.

Companion files:

- [01-angeris-2022-primer-on-perpetuals.md](01-angeris-2022-primer-on-perpetuals.md)
- [02-he-2024-fundamentals-of-perpetual-futures.md](02-he-2024-fundamentals-of-perpetual-futures.md)
- [03-alexander-2023-hedging-liquidation-leverage.md](03-alexander-2023-hedging-liquidation-leverage.md)
- [04-zhang-2026-shared-template-funding.md](04-zhang-2026-shared-template-funding.md)
- [05-zhivkov-2026-two-tiered-funding-markets.md](05-zhivkov-2026-two-tiered-funding-markets.md)

## 0. First principles extracted from the papers

| Primitive | Paper | What it unlocks |
|---|---|---|
| Any smooth payoff `phi(S)` can be funded and replicated | Paper 1, Theorem 2 | Perps are a general derivative primitive, not just linear futures |
| Funding is gamma minus financing | Paper 1, Theorem 2 | Funding is computable from observable realized covariance |
| Discounting perp with `D = F / phi` | Paper 1, Theorem 7 | No-funding perp designs; LETF and power-perp equivalents |
| Variance, power, and CFMM payoffs are special cases | Paper 1, Examples 5, 8, 9 | Structured products and LP replacement |
| Jump funding is a jump integral | Paper 1, Theorem 10 | Perpetual insurance, event risk, slashing risk |
| Jump replication with power options | Paper 1, Theorem 12 | Hedging rare-event payoffs without a parametric volatility model |
| Random-maturity arbitrage | Paper 2, Definition 3 | A correct no-arbitrage notion for instruments with no expiry |
| Fundamental price `F = (1 - Phi^{-1}(r))^{-1} S` | Paper 2, Proposition 1 | The exchange funding rule determines the fair futures-spot ratio |
| Clamp-aware Binance benchmark and cost bounds | Paper 2, Corollary 2 and Proposition 3 | Realistic fair-value band and trading signals |
| Deviation `rho`, comovement, momentum | Paper 2, Section 4 and 5 | Basis factor, relative-value signals, regime detection |
| Liquidation-risk bounds | Paper 2, Section 4.4 | Capital-efficient arbitrage and risk dashboards |
| Dual-objective hedge with liquidation probability | Paper 3, Problem 3.1 and Theorem 3.2 | Survival-aware position sizing, margin oracle |
| Inverse P&L scaling `omega = F_t` | Paper 3, Theorem 3.2 | Correct inverse-contract accounting; avoid catastrophic hedge errors |
| Leverage-weighted liquidation and aggressiveness indices | Paper 3, Section 2.5 | Venue and product routing; speculation metrics |
| Optimal implied leverage | Paper 3, Section 4.5 | Collateral and leverage autopilot |
| Nominal versus effective funding | Paper 4, abstract | Funding DNA oracle; template audit |
| Capital-dependent stability band | Paper 4, abstract | Funding-rule calibration; regime detection |
| Liquidation reduces corrective capacity | Paper 4, abstract | Cascade monitor; stabilisation-risk score |
| Flat, capped, and interval-averaged funding regions | Paper 4, abstract | Funding-rule compiler; pre-settlement stress tests |
| Two-tier CEX/DEX funding structure | Paper 5, Sections 4.5-4.6 | Cross-venue lead-lag signal; market-structure oracle |
| CEX-to-DEX information flow | Paper 5, Section 4.5 | Execution routing and latency-aware arbitrage |
| Gross spread versus net executable spread | Paper 5, Sections 3.4-3.5 and 4.7 | Cost-aware router; rejection of false opportunities |
| Spread reversal and forced-exit risk | Paper 5, Section 4.7 | Reversal-hazard model; dynamic exit thresholds |
| Duration plus spread as the profitability condition | Paper 5, Section 5.2 | Trade selection; break-even time |
| 8h normalization across funding intervals | Paper 5, Section 3.1.2 | Cross-venue comparability; basis index |

The core insight for ideation: **Paper 1 is a factory for new payoffs. Paper 2 is a factory for new market signals and risk anchors. Paper 3 is a factory for survival-aware risk controls. Paper 4 is a factory for effective-feedback diagnostics and rule calibration. Paper 5 is a factory for cross-venue execution and market-structure signals.** The best hackathon idea combines a payoff from Paper 1, an anchor from Paper 2, a survival policy from Paper 3, a feedback calibration from Paper 4, and an execution router from Paper 5.

## 1. Evaluation rubric

Every idea is scored 1-5 on:

- **Novelty:** how unbuilt or underexplored it is.
- **Technical depth:** how much real math or protocol work is required.
- **Demo:** how clearly a judge can see it working in three minutes.
- **Feasibility:** how likely a team of five humans plus ten coding agents can ship a credible prototype.
- **Judge appeal:** narrative, sponsor fit, and memorability.
- **Moat:** what remains defensible after the hackathon.

Total is out of 30.

## 2. Evolve the current ideas

### 2.1 Payoff Forge becomes Perp Forge

The original idea was a no-code structured-perp designer. The stronger version is a compiler and launchpad.

**Perp Forge** takes a payoff function and produces:

1. The funding rate `F_t` and discount rate `D_t`.
2. The hedge ratios `Delta = grad phi`.
3. The jump-adjusted funding integral.
4. An oracle specification: which data, frequency, manipulation guards.
5. Solidity or Rust code skeletons plus tests.
6. A Monte Carlo and historical risk report.
7. A one-click testnet deployment.

This is the difference between a calculator and a compiler. The calculator demos a formula. The compiler creates a new financial instrument.

**Why it is stronger than the original idea:** It is a platform, not a single product. It can generate every other idea in this document. The demo can end with a novel perp that was compiled live.

### 2.2 Basis Radar becomes Basis Mesh

The original idea was a clamp-aware cross-venue basis scanner. The stronger version is a three-layer network:

1. **Data layer:** cross-venue, cross-chain funding and basis ingestion.
2. **Signal layer:** clamp-aware no-arbitrage band, deviation `rho`, common-factor index, liquidation bound, AI copilot.
3. **Trust layer:** an onchain basis/fair-value oracle consumed by other protocols.

The scanner is the UI. The oracle is the moat. The AI copilot is the demo.

**Why it is stronger than the original idea:** Dashboards are commoditized. A fair-value oracle plus a basis factor index plus an agentic risk copilot is a protocol primitive, not a table.

## 3. Tier 1: New primitives from Paper 1

These ideas use Paper 1's ability to fund and replicate arbitrary payoffs.

### 3.1 Shiller Perps: perpetual claims on illiquid real-world assets

**Thesis.** Shiller's original 1993 motivation for perpetual futures was to create perpetual claims on illiquid assets such as housing. Crypto built perps for liquid tokens instead. With better oracles and onchain settlement, Shiller's original use case becomes possible.

**Paper foundation.** Paper 1, Theorem 2 and Theorem 7. Choose a payoff `phi(I)` where `I` is an index level. Funding is the gamma of the index minus financing. If the index is smooth and monthly, use a discrete-time version.

**What appears never made.** RWA spot tokens exist. Onchain perpetual claims on illiquid RWA indices are rare. A general "Shiller Perp" framework with model-free funding is unbuilt.

**Why it could work.** The narrative is elegant: the paper's own origin story, finally onchain. Real users need to hedge home prices, private credit, carbon, or energy without selling the underlying. A perp gives leveraged, expiring-free exposure.

**Mechanism and oracle design.** Start with one synthetic index that has a credible public data source. Examples:

- US home price index from a public monthly series.
- Carbon price from a regulated market.
- Private credit yield index.
- A basket of RWA token prices.

Use a delayed TWAP with a dispute window. Do not pretend the oracle is real-time. Design the funding around the oracle lag: lower frequency, wider bands, circuit breakers on stale data.

**Build architecture.** Oracle adapter, perp contract, funding engine, margin engine, UI, compliance wrapper. For a hackathon, use testnet USDC and a mock index with a public data feed.

**Demo moment.** A homeowner or fund hedges $100,000 of home price exposure with a Shiller Perp. The screen shows the funding cost, the hedge ratio, and the payout under a 10% index move. Then show the same instrument on carbon or private credit.

**Moat.** Data partnerships, index methodology, regulatory positioning, and distribution to RWA issuers.

**Risks and kill criteria.** Oracle lag, legal classification, liquidity, and demand. If the index cannot be made credible, pivot to a synthetic RWA basket with a transparent methodology. If regulators treat it as a security, keep it testnet-only and position it as a research prototype.

**Score.** Novelty 5, technical 3, demo 4, feasibility 3, judge 5, moat 5. Total 25.

### 3.2 Perpetual Prediction and Event Perp

**Thesis.** Prediction markets expire. A perpetual contract on an event probability is continuous, capital-efficient, and composable. Funding anchors the perp price to an oracle probability.

**Paper foundation.** Paper 2, Proposition 1, with the "spot" replaced by a probability `p`. The fundamental price is `F = (1 - Phi^{-1}(r))^{-1} p`. Alternatively, Paper 1 with a convex payoff such as `phi(p) = p^2` or a logit transform.

**What appears never made.** Expiring binary prediction markets dominate. A no-expiry perp on a probability with funding is rare. The design challenge is that probabilities are bounded, so the linear perp price can exceed 1. A bounded payoff or a capped funding rule is needed.

**Why it could work.** Prediction markets suffer from capital lockup and expiry fragmentation. A perp would let traders express views continuously and let market makers hedge across events. It also creates a yield curve of probabilities.

**Mechanism and oracle design.** Use a TWAP of a liquid prediction market (Polymarket, Kalshi, Manifold) or an optimistic oracle for the event. The funding rule should be capped so the perp price stays in a sensible range. Add a resolution mechanism for the terminal event: either cash-settle at 1 or 0, or roll into a new event.

**Build architecture.** Oracle adapter, perp contract, funding engine, AMM or order book, margin, UI. For a hackathon, use a testnet event and a mock probability feed.

**Demo moment.** A perp on "ETH ETF approval by a given date" trades continuously. Show the funding rate, the probability, and the PnL as the probability moves. Then trigger a resolution and show settlement.

**Moat.** Liquidity, oracle relationships, regulatory strategy, and event coverage.

**Risks and kill criteria.** Event contracts are regulated in many jurisdictions. Oracle manipulation is easy if the underlying prediction market is thin. If legal or oracle risk is too high, pivot to crypto-native events: token unlocks, protocol upgrades, governance votes, or airdrops.

**Score.** Novelty 5, technical 4, demo 5, feasibility 4, judge 5, moat 4. Total 27.

### 3.3 Perpetual Insurance and Slashing Perp

**Thesis.** Insurance has expiry, capital lockup, and claims friction. A perp with a jump payoff provides continuous coverage, with funding as the premium. Restaking and slashing are the killer use case.

**Paper foundation.** Paper 1, Theorem 10. A binary or jump payoff has funding equal to the jump integral. The short side bears the jump risk and earns the funding premium. Theorem 12 describes replication with power options, but for a hackathon a capital-backed vault is sufficient.

**What appears never made.** Slashing insurance exists in various forms. A perpetual, perp-style slashing cover with model-free jump funding is rare. The same primitive generalizes to stablecoin depeg, bridge hacks, and protocol exploits.

**Why it could work.** Restaking is large and growing. Validators and restakers need continuous cover. A perp is simpler than a claims-based insurance contract, and the funding premium is observable and tradeable. It is also a perfect fit for Paper 1's jump model.

**Mechanism and oracle design.** Onchain slashing events are observable. Define a smooth approximation to the binary payoff, or use a jump process with an estimated intensity. The risk vault posts capital and earns funding. Use a cap on payout and a circuit breaker for correlated slashing. The oracle should be trust-minimized: beacon chain events, not a committee.

**Build architecture.** Perp contract, funding engine, risk vault, oracle adapter, UI, risk dashboard. For a hackathon, simulate slashing events on a testnet.

**Demo moment.** A validator buys perpetual slashing cover. The screen shows the funding premium, the vault capacity, and the payout under a simulated slashing event. Then show the same primitive for USDC depeg or a bridge hack.

**Moat.** Risk modeling, capital, validator relationships, and oracle integrations.

**Risks and kill criteria.** Adverse selection, moral hazard, correlated events, oracle manipulation, and insurance regulation. If the capital model cannot be made safe, pivot to a capped parametric cover. If insurance regulation is a blocker, position it as a risk-transfer primitive for professional restakers.

**Score.** Novelty 5, technical 4, demo 5, feasibility 4, judge 5, moat 4. Total 27.

### 3.4 LP Perp and impermanent-loss hedge

**Thesis.** Paper 1 proves that, ignoring fees, a perp with the same payoff as a CFMM LP position dominates the LP. Build a perp that replicates or hedges LP exposure.

**Paper foundation.** Paper 1, Example 9. For a geometric-mean CFMM, `phi = V_0 (S_1/S_1,0)^p (S_2/S_2,0)^q`. The discount rate is `D = p(p-1)/2 d<log S_1> + q(q-1)/2 d<log S_2> + p q d<log S_1, log S_2>`. The discounted payoff dominates the LP by `(p dx - q dy)^2`.

**What appears never made.** Impermanent-loss hedges exist using options and vaults. A perp on the LP payoff with model-free funding is rare.

**Why it could work.** LPs constantly ask for IL protection. A perp is capital-efficient and does not require an options market. The paper gives an exact benchmark and a clean dominance result.

**Mechanism and oracle design.** Compute the LP value from pool reserves or a subgraph. Use a TWAP to avoid manipulation. Add a fee-aware adjustment, because the paper's dominance result ignores fees. The perp's funding should account for the LP's realized covariance.

**Build architecture.** Oracle adapter for pool reserves, perp contract, funding engine, hedge vault, UI. For a hackathon, use a Uniswap v3 or v2 pool on a testnet.

**Demo moment.** Compare an ETH/USDC LP position with a perp hedge over historical data. Show the IL, the funding cost, and the net improvement. Then show the paper's dominance term `(p dx - q dy)^2` visually.

**Moat.** DEX integrations, fee-aware modeling, and distribution to LPs.

**Risks and kill criteria.** Fees, MEV, and oracle manipulation can erase the edge. If the fee-aware model shows no improvement, pivot to a pure analytics tool for LPs.

**Score.** Novelty 4, technical 4, demo 4, feasibility 4, judge 4, moat 4. Total 24.

### 3.5 Blob, gas, and blockspace perps

**Thesis.** Rollups and users need to hedge Ethereum data-availability and gas costs. A perp on blob fees or gas is a native Ethereum primitive.

**Paper foundation.** Paper 1, Theorem 2 and Theorem 7. Choose `phi` as the blob base fee or gas price. Funding is the gamma of the fee process minus financing. Paper 2 provides the anchoring logic if the perp trades against a spot fee index.

**What appears never made.** Blockspace markets exist (for example, Alkimiya). A dedicated blob-fee perp with model-free funding is rare. EIP-4844 created a new, volatile cost input for rollups, and there is no standard hedge.

**Why it could work.** Every rollup has blob-cost exposure. A perp lets an L2 treasury lock in a data-availability budget. It is Ethereum-native, timely, and easy for judges to understand.

**Mechanism and oracle design.** Use onchain blob base fee history from the beacon chain. For gas, use EIP-1559 base fee. Use a TWAP and a circuit breaker. The perp can be cash-settled against the index.

**Build architecture.** Oracle adapter, perp contract, funding engine, UI for rollups and DAOs. For a hackathon, use historical blob data and a testnet settlement token.

**Demo moment.** An L2 treasury hedges six months of blob costs. Show the funding cost, the budget certainty, and the payout under a blob-fee spike. Then show the same for gas.

**Moat.** Ethereum-native data, L2 partnerships, and a standard index.

**Risks and kill criteria.** Demand may be limited, and liquidity may be thin. If rollups do not engage, pivot to a "gas budget" product for DAOs and traders.

**Score.** Novelty 4, technical 3, demo 4, feasibility 4, judge 5, moat 4. Total 24.

### 3.6 Compute and DePIN perps

**Thesis.** AI compute is a new commodity. Perps let AI startups and GPU providers hedge price risk. Paper 1 gives the funding math for any compute-price index.

**Paper foundation.** Paper 1, Theorem 2 and Theorem 7. Choose `phi` as a GPU rental, inference, or storage price index. Funding is the gamma of the index minus financing. The jump extension prices supply shocks.

**What appears never made.** DePIN tokens give spot exposure. Perps on compute prices are rare. An index-based compute perp with model-free funding is unbuilt to our knowledge.

**Why it could work.** AI compute demand is booming and volatile. GPU providers want to hedge revenue; AI startups want to hedge costs. It is a timely AI x crypto narrative with a real hedging use case.

**Mechanism and oracle design.** Build a compute price index from marketplace data: Render, Akash, io.net, or cloud GPU spot prices. Use a robust median and a TWAP. Disclose the index methodology. Add a dispute mechanism.

**Build architecture.** Oracle adapter, perp contract, funding engine, UI for providers and consumers. For a hackathon, use a curated index and a mock settlement token.

**Demo moment.** An AI startup locks in GPU costs for six months; a GPU provider locks in revenue. Show the funding, the hedge ratio, and the payout under a compute-price shock.

**Moat.** Index methodology, data partnerships, and distribution.

**Risks and kill criteria.** Oracle quality and liquidity are the main risks. If the data is unreliable, pivot to a synthetic compute index with a transparent, curated methodology.

**Score.** Novelty 5, technical 4, demo 4, feasibility 3, judge 5, moat 4. Total 25.

### 3.7 Correlation and dispersion perps

**Thesis.** Crypto portfolios have correlation risk. A perp on correlation or dispersion is a new hedging primitive. Paper 1's multi-asset funding formula prices it.

**Paper foundation.** Paper 1, Theorem 2. Choose a payoff such as `phi = (S_1/S_1,0)^p (S_2/S_2,0)^q`, or a dispersion index. The funding includes the cross-gamma term `p q d<log S_1, log S_2>`.

**What appears never made.** Correlation swaps exist in OTC markets. Onchain correlation perps are rare.

**Why it could work.** Crypto portfolios are highly correlated, and correlation regime shifts are a major risk. A correlation perp lets funds hedge or speculate on it. It is a genuine quant primitive.

**Mechanism and oracle design.** Multi-asset price feeds, realized covariance, and a robust index methodology. The payoff must be bounded or collateralized to avoid pathological outcomes.

**Build architecture.** Perp contract, funding engine, covariance oracle, analytics UI. For a hackathon, use BTC/ETH and a simple dispersion index.

**Demo moment.** A portfolio manager hedges the correlation between BTC and ETH. Show the funding, the hedge ratio, and the PnL under a correlation regime shift.

**Moat.** Quant IP, data, and relationships with funds.

**Risks and kill criteria.** Complexity and liquidity. If users do not understand it, package it as "portfolio insurance" rather than a correlation perp.

**Score.** Novelty 5, technical 5, demo 3, feasibility 3, judge 3, moat 4. Total 23.

### 3.8 Jump and event perps

**Thesis.** Scheduled events create jump risk: token unlocks, ETF decisions, elections, protocol upgrades. A perp with jump-aware funding prices that risk explicitly.

**Paper foundation.** Paper 1, Theorem 10. The funding integral becomes an event premium. A smooth approximation to a binary payoff lets the perp trade continuously.

**What appears never made.** Event-driven options exist. A perp with jump-aware funding around scheduled events is rare.

**Why it could work.** Crypto is event-driven. Traders want continuous exposure to event outcomes without buying expiring options. A perp is capital-efficient and composable.

**Mechanism and oracle design.** Event calendar, jump-intensity estimator, event oracle, and a settlement rule. Use a capped payout and a circuit breaker around the event.

**Build architecture.** Event registry, perp contract, funding engine, oracle adapter, UI. For a hackathon, use a token unlock or a protocol upgrade.

**Demo moment.** A trader hedges a token unlock. Show the funding premium, the jump intensity, and the payout under different unlock scenarios.

**Moat.** Event data, oracle integrations, and risk models.

**Risks and kill criteria.** Oracle manipulation and event definition ambiguity. If the event oracle is unreliable, pivot to crypto-native deterministic events such as protocol upgrades or unlock schedules.

**Score.** Novelty 5, technical 4, demo 4, feasibility 4, judge 4, moat 4. Total 25.

### 3.9 Power perp factory and structured products

**Thesis.** Squeeth is one power perp. Paper 1 generalizes to any power `phi = S^gamma` and to arbitrary structured payoffs. Build a factory that launches and hedges these products.

**Paper foundation.** Paper 1, Example 8. `D = gamma(gamma-1)/2 d<log S> - (1-gamma) d log M`. The same formula covers LETFs and power perps.

**What appears never made.** Squeeth exists. A general power-perp factory with model-free funding and automatic hedging is rare.

**Why it could work.** Traders want leveraged convex exposure without rollover. A factory can launch 2x, 3x, inverse, and custom power perps. It is a natural extension of a proven product.

**Mechanism and oracle design.** Price and realized variance oracle, funding engine, hedge vault, and a risk dashboard. The hard part is liquidity and hedging, not the math.

**Build architecture.** Perp factory contract, funding engine, oracle, UI, and a market-maker vault. For a hackathon, launch one or two power perps on testnet.

**Demo moment.** Launch a 3x ETH perp and an inverse ETH perp from the same factory. Show the funding, the hedge, and the PnL.

**Moat.** Liquidity, hedging infrastructure, and integrations.

**Risks and kill criteria.** Liquidity and competition from Squeeth. If the market is saturated, pivot to the compiler and structured-product angle.

**Score.** Novelty 3, technical 4, demo 4, feasibility 4, judge 4, moat 3. Total 22.

### 3.10 Perp on funding rate or basis

**Thesis.** The funding rate and basis are themselves tradeable risk factors. A perp on funding or basis lets traders hedge basis risk directly.

**Paper foundation.** Paper 2, the deviation `rho` and the no-arbitrage band. Paper 1 for the funding mechanics of the meta-perp.

**What appears never made.** Some CEXs offer funding-rate futures. An onchain perp on a basis index is rare. The definitional challenge is recursive: the funding of the funding perp needs its own anchor.

**Why it could work.** Basis traders want to hedge the funding leg. A basis perp would complete the market. It is a pure quant product.

**Mechanism and oracle design.** Define a basis index (for example, the annualized `rho` across venues). Use a separate funding rule for the meta-perp, or make it a futures-style contract with a maturity. Be explicit about the recursion.

**Build architecture.** Basis index oracle, meta-perp contract, funding engine, UI. For a hackathon, use a simple index and a capped funding rule.

**Demo moment.** A basis trader hedges the funding leg of a carry trade. Show the meta-perp funding and the PnL under a funding regime change.

**Moat.** Index methodology and liquidity.

**Risks and kill criteria.** Recursion and liquidity. If the meta-perp is too confusing, turn it into a "funding curve" analytics product instead.

**Score.** Novelty 4, technical 4, demo 3, feasibility 3, judge 3, moat 3. Total 20.

## 4. Tier 2: Market structure and analytics from Paper 2

### 4.1 Basis Mesh

**Thesis.** Basis and funding are a cross-venue, cross-chain factor. The winning product is a Bloomberg-style terminal plus an onchain oracle plus an AI copilot.

**Paper foundation.** Paper 2, Proposition 1, Corollary 2, Proposition 3, and the empirical section. Compute `rho`, the clamp-aware band, the common-factor index, and the liquidation bound.

**What appears never made.** Funding dashboards exist. A clamp-aware fair-value oracle plus a basis factor index plus an agentic risk copilot is a rare combination.

**Why it could work.** Traders need context, not just numbers. A copilot that explains why a basis is dislocated and what the risk is adds real value. The oracle creates a moat.

**Mechanism and oracle design.** Ingest CEX and DEX data. Compute `rho` and the band. Publish the band and a confidence score onchain. Use multiple data sources and outlier detection.

**Build architecture.** Ingestion layer, data warehouse, signal engine, oracle contract, AI copilot, alerts, and an optional intent-based execution layer.

**Demo moment.** Live basis across three venues. The copilot flags a dislocation, explains the drivers, shows the band and liquidation risk, and publishes an onchain signal. A lending protocol consumes the oracle.

**Moat.** Data, oracle integrations, and AI UX.

**Risks and kill criteria.** Crowded dashboard market. If the UI is commoditized, pivot to the oracle and risk API.

**Score.** Novelty 3, technical 4, demo 5, feasibility 5, judge 5, moat 3. Total 25.

### 4.2 Basis Oracle and perp fair-value oracle

**Thesis.** Perp DEXs and lending protocols need a fair-value and basis oracle, not just a spot price. Publish the clamp-aware no-arbitrage band and `rho` onchain.

**Paper foundation.** Paper 2, Corollary 2 and Proposition 3. The oracle outputs the benchmark, the band, and a deviation score.

**What appears never made.** Price oracles are common. A perp-specific fair-value/basis oracle is rare.

**Why it could work.** It is a public good with clear consumers: perp DEXs, lending protocols, risk dashboards, and structured products. It is also a natural sponsor fit for oracle networks.

**Mechanism and oracle design.** Keeper network, multiple data sources, medianization, and a dispute window. Publish the inputs and the formula so consumers can verify.

**Build architecture.** Oracle contract, keeper, data adapters, consumer demo. For a hackathon, deploy on a testnet and integrate with a mock lending protocol.

**Demo moment.** A lending protocol uses the perp fair-value band to adjust collateral requirements. Show the oracle updating through a basis dislocation.

**Moat.** Oracle network effects and integrations.

**Risks and kill criteria.** Oracle manipulation and adoption. If no consumer integrates, pivot to a risk dashboard.

**Score.** Novelty 4, technical 4, demo 3, feasibility 4, judge 4, moat 5. Total 24.

### 4.3 Perp Sim: a flight simulator for perp DEX design

**Thesis.** Perp DEX design is risky. A simulator lets teams stress-test funding, margin, auto-deleveraging, insurance funds, and oracle attacks before launch.

**Paper foundation.** Paper 1 for funding and replication; Paper 2 for no-arbitrage, deviations, and liquidation bounds.

**What appears never made.** Internal risk simulators exist. A public, formula-faithful perp DEX flight simulator is rare.

**Why it could work.** Every perp DEX team needs this. It is a developer tool with a strong visual demo: stress-test a protocol and watch it break.

**Mechanism and oracle design.** Agent-based simulation with configurable funding rules, margin, fees, oracle noise, and adversarial traders. Scenario library: jump, depeg, funding spike, oracle manipulation, bank run.

**Build architecture.** Simulation engine, scenario library, web UI, report export. Use Python or Rust for the engine and a React frontend.

**Demo moment.** Stress-test a perp DEX under a 20% jump and a funding spike. Show liquidations, insurance fund depletion, and auto-deleveraging. Then change the funding rule and show the market survive.

**Moat.** Scenario library, integrations, and trust.

**Risks and kill criteria.** Fidelity and scope. If users want a monitor rather than a simulator, pivot to a live risk dashboard.

**Score.** Novelty 4, technical 5, demo 5, feasibility 3, judge 4, moat 4. Total 25.

### 4.4 Agentic basis copilot and perp CFO

**Thesis.** Traders and DAOs need an AI agent that monitors basis, funding, and liquidation risk and proposes hedges. Paper 2 provides the signals; Paper 1 provides the hedge ratios.

**Paper foundation.** Paper 2's `rho`, band, common factor, and liquidation bounds; Paper 1's `Delta = grad phi` and funding formulas.

**What appears never made.** Trading bots exist. A theory-grounded, explainable, guardrailed perp risk copilot is rare.

**Why it could work.** AI agents are a dominant 2026 narrative. A copilot that explains its reasoning and uses paper-derived signals is more trustworthy than a black-box bot.

**Mechanism and oracle design.** Deterministic signal engine plus an LLM for explanation and tool use. Human approval for execution. Multisig and spend limits. No autonomous fund custody in the hackathon demo.

**Build architecture.** Data ingestion, signal engine, LLM agent, tool layer, alerts, and an optional testnet execution module.

**Demo moment.** The agent detects a basis dislocation, explains the driver, proposes a hedge, shows the risk bounds, and executes on a testnet after human approval.

**Moat.** UX, guardrails, and data.

**Risks and kill criteria.** Hallucination, execution risk, and regulation. If the agent is unreliable, pivot to a deterministic alerting copilot with an LLM explanation layer only.

**Score.** Novelty 4, technical 4, demo 5, feasibility 4, judge 5, moat 3. Total 25.

### 4.5 Random-maturity arbitrage vault or DAO

**Thesis.** Turn Paper 2's strategy into a transparent onchain vault with risk bounds, liquidation protection, and proof of reserves.

**Paper foundation.** Paper 2's strategy, cost bands, and liquidation bounds.

**What appears never made.** Basis trading funds exist. A transparent, onchain, theory-anchored random-maturity arbitrage vault is rare.

**Why it could work.** It is a concrete yield product with a research edge. It can attract depositors and demonstrate the paper's economics.

**Mechanism and oracle design.** Off-chain execution on CEXs with onchain attestations, or onchain execution on perp DEXs. Use a multisig, spend limits, and real-time risk monitoring. Publish the strategy and the bounds.

**Build architecture.** Vault contract, strategy engine, risk monitor, attestation layer, UI.

**Demo moment.** Deposit testnet USDC, run a historical replay, and show the Sharpe ratio under different fee tiers. Show the liquidation bound and the drawdown.

**Moat.** Track record, capital, and risk management.

**Risks and kill criteria.** Regulatory, custody, and execution risk. If a real vault is not feasible, build a paper-trading vault and call it a research prototype.

**Score.** Novelty 3, technical 4, demo 4, feasibility 3, judge 4, moat 3. Total 21.

### 4.6 Funding Firewall: manipulation and mechanism-design simulator

**Thesis.** Funding rates are a manipulation surface. Build a tool that stress-tests perp DEX funding rules against oracle manipulation, wash trading, and impact-margin games.

**Paper foundation.** Paper 2's funding mechanics, clamp, impact margin notional, and no-arbitrage bands; Paper 1's funding formulas.

**What appears never made.** Security tools for perp funding mechanisms are rare. Most audits focus on code, not mechanism design.

**Why it could work.** Perp DEXs are high-value targets. A mechanism-design security tool is a strong infra/security narrative.

**Mechanism and oracle design.** Adversarial simulation of funding manipulation. Scenario library: oracle push, wash trading, impact-margin gaming, funding timestamp attack. Output a risk report and mitigation recommendations.

**Build architecture.** Simulation engine, attack library, report generator, UI.

**Demo moment.** Break a naive funding rule, then show a clamp-aware rule survive. Show the cost to manipulate and the mitigation.

**Moat.** Attack library and security reputation.

**Risks and kill criteria.** Demand and fidelity. If perp DEXs do not engage, pivot to an audit/report product.

**Score.** Novelty 4, technical 4, demo 4, feasibility 4, judge 3, moat 4. Total 23.

## 5. Tier 3: Moonshots and crazy ideas

These are high-variance ideas. Some are serious, some are for spectacle. Each is scored briefly.

### 5.1 Agent Perp: perps on AI agent performance

**Thesis.** Trade the performance of AI agents: trading agents, coding agents, research agents, or an agent swarm.

**Paper foundation.** Paper 1, any payoff `phi(performance)`. Paper 2 for the anchor if the performance index is tradeable.

**What appears never made.** AI agent tokens exist. Perps on measured agent performance are rare.

**Why it could be crazy-good.** It is a 2026-native narrative. It turns agent benchmarks into a financial market. It is memorable and demo-friendly.

**Why it could fail.** Performance is hard to oracle, easy to game, and legally ambiguous.

**Score.** Novelty 5, technical 3, demo 5, feasibility 3, judge 4, moat 2. Total 22.

### 5.2 Dark Perp: privacy-preserving perpetuals

**Thesis.** Perp DEXs leak positions and liquidation levels. A privacy-preserving perp hides size and collateral using ZK, FHE, or MPC.

**Paper foundation.** Paper 2's funding and risk bounds, computed over encrypted or committed data.

**What appears never made.** Privacy DEXs exist. Privacy perps with encrypted funding and margin are rare.

**Why it could be crazy-good.** Privacy is a real need for market makers and funds. It is a strong ZK/privacy sponsor play.

**Why it could fail.** Performance, complexity, and liquidity. It is a multi-month build, not a prototype.

**Score.** Novelty 5, technical 5, demo 3, feasibility 2, judge 3, moat 4. Total 22.

### 5.3 Perpetual Lottery: a jackpot perp

**Thesis.** A perp with a convex payoff that pays a large amount on a rare jump. Funding is the lottery premium.

**Paper foundation.** Paper 1, Theorem 10. The jump integral prices the jackpot.

**What appears never made.** Lotteries and options exist. A perpetual lottery with funding is rare.

**Why it could be crazy-good.** It is simple, viral, and visually fun. It could be a consumer crypto app.

**Why it could fail.** Gambling regulation, adverse selection, and capital.

**Score.** Novelty 4, technical 2, demo 5, feasibility 4, judge 3, moat 2. Total 20.

### 5.4 Private Company Perp

**Thesis.** Shiller-style perpetual claims on private company revenue or valuation.

**Paper foundation.** Paper 1, Theorem 7, with a revenue or valuation index.

**What appears never made.** Private company secondary markets exist. Perps on private revenue are rare.

**Why it could be crazy-good.** It opens a huge asset class and matches Shiller's original vision.

**Why it could fail.** Securities law, oracle credibility, and liquidity. Likely not hackathon-feasible.

**Score.** Novelty 5, technical 3, demo 3, feasibility 2, judge 3, moat 3. Total 19.

### 5.5 Weather, energy, and carbon perps

**Thesis.** Perpetual claims on weather, energy, or carbon indices.

**Paper foundation.** Paper 1, Theorem 2, with an RWA index.

**What appears never made.** OTC weather derivatives exist. Onchain weather perps are rare.

**Why it could be crazy-good.** Real-world impact and climate finance narrative.

**Why it could fail.** Oracle quality, regulation, and demand.

**Score.** Novelty 4, technical 3, demo 3, feasibility 2, judge 4, moat 4. Total 20.

### 5.6 Perp on a Perp: Perp squared

**Thesis.** A perp on the price of a perp protocol token, or a perp on a perp's funding rate.

**Paper foundation.** Paper 1 and Paper 2, applied recursively.

**What appears never made.** Likely never made as a serious product. It is a meme.

**Why it could be crazy-good.** It is a hackathon spectacle and a great joke for the demo.

**Why it could fail.** It is not a real product. Use it as a stretch goal or an easter egg.

**Score.** Novelty 3, technical 2, demo 4, feasibility 5, judge 2, moat 2. Total 18.

### 5.7 ZK-verified perp solvency

**Thesis.** Prove perp DEX solvency and funding correctness with ZK proofs, without revealing positions.

**Paper foundation.** Paper 2's funding and risk bounds; Paper 1's replication.

**What appears never made.** ZK proofs for exchange solvency exist. ZK-verified perp funding is rare.

**Why it could be crazy-good.** Trust and privacy in one primitive. Strong ZK sponsor fit.

**Why it could fail.** Proving complexity and performance.

**Score.** Novelty 4, technical 5, demo 4, feasibility 2, judge 4, moat 3. Total 22.

### 5.8 Attention and meme perps

**Thesis.** Perps on social attention, meme indices, or creator coins.

**Paper foundation.** Paper 1, with an attention index payoff.

**What appears never made.** Meme indices exist. Attention perps are rare.

**Why it could be crazy-good.** Consumer-friendly and viral.

**Why it could fail.** Oracle manipulation and demand.

**Score.** Novelty 4, technical 2, demo 5, feasibility 4, judge 3, moat 2. Total 20.

### 5.9 Hashrate, staking yield, and MEV perps

**Thesis.** Miners hedge hashrate; validators hedge staking yield; searchers hedge MEV revenue.

**Paper foundation.** Paper 1, Theorem 2 and Theorem 7; jump extension for slashing and MEV spikes.

**What appears never made.** Hashrate futures exist. Onchain perps on these cash flows are rare.

**Why it could be crazy-good.** Real hedging demand from professional operators.

**Why it could fail.** Niche and oracle-dependent.

**Score.** Novelty 4, technical 3, demo 4, feasibility 3, judge 4, moat 3. Total 21.

### 5.10 Protocol revenue and TVL perps

**Thesis.** Trade protocol fundamentals without the token. A perp on revenue or TVL.

**Paper foundation.** Paper 1, with an onchain fundamental index.

**What appears never made.** Fundamental perps on protocol revenue are rare.

**Why it could be crazy-good.** A new asset class for onchain fundamentals.

**Why it could fail.** Oracle manipulation and demand.

**Score.** Novelty 4, technical 3, demo 4, feasibility 3, judge 3, moat 3. Total 20.

## 6. Scoring matrix

| Idea | Paper | Novelty | Tech | Demo | Feasibility | Judge | Moat | Total |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| Perp Forge | 1 | 5 | 5 | 4 | 4 | 5 | 5 | 28 |
| Slashing Perp | 1 | 5 | 4 | 5 | 4 | 5 | 4 | 27 |
| Prediction Perp | 1+2 | 5 | 4 | 5 | 4 | 5 | 4 | 27 |
| Shiller Perps | 1 | 5 | 3 | 4 | 3 | 5 | 5 | 25 |
| Compute Perp | 1 | 5 | 4 | 4 | 3 | 5 | 4 | 25 |
| Jump/Event Perp | 1 | 5 | 4 | 4 | 4 | 4 | 4 | 25 |
| Basis Mesh | 2 | 3 | 4 | 5 | 5 | 5 | 3 | 25 |
| Perp Sim | 1+2 | 4 | 5 | 5 | 3 | 4 | 4 | 25 |
| Agentic Copilot | 2 | 4 | 4 | 5 | 4 | 5 | 3 | 25 |
| LP Perp | 1 | 4 | 4 | 4 | 4 | 4 | 4 | 24 |
| Blob Perp | 1 | 4 | 3 | 4 | 4 | 5 | 4 | 24 |
| Basis Oracle | 2 | 4 | 4 | 3 | 4 | 4 | 5 | 24 |
| Correlation Perp | 1 | 5 | 5 | 3 | 3 | 3 | 4 | 23 |
| Funding Firewall | 2 | 4 | 4 | 4 | 4 | 3 | 4 | 23 |
| Agent Perp | 1 | 5 | 3 | 5 | 3 | 4 | 2 | 22 |
| Dark Perp | 1+2 | 5 | 5 | 3 | 2 | 3 | 4 | 22 |
| ZK Perp | 1+2 | 4 | 5 | 4 | 2 | 4 | 3 | 22 |
| Power Perp Factory | 1 | 3 | 4 | 4 | 4 | 4 | 3 | 22 |
| Arbitrage Vault | 2 | 3 | 4 | 4 | 3 | 4 | 3 | 21 |
| Hashrate/Staking/MEV | 1 | 4 | 3 | 4 | 3 | 4 | 3 | 21 |
| Funding/Basis Perp | 1+2 | 4 | 4 | 3 | 3 | 3 | 3 | 20 |
| Perpetual Lottery | 1 | 4 | 2 | 5 | 4 | 3 | 2 | 20 |
| Weather/Energy | 1 | 4 | 3 | 3 | 2 | 4 | 4 | 20 |
| Attention Perp | 1 | 4 | 2 | 5 | 4 | 3 | 2 | 20 |
| Protocol Revenue Perp | 1 | 4 | 3 | 4 | 3 | 3 | 3 | 20 |
| Private Company Perp | 1 | 5 | 3 | 3 | 2 | 3 | 3 | 19 |
| Perp on a Perp | 1+2 | 3 | 2 | 4 | 5 | 2 | 2 | 18 |

## 7. Recommended build portfolio for 5 humans and 10 agents

The highest-expected-value strategy is not to build one idea in isolation. It is to build a platform and use it to launch one flagship instrument that has never existed.

### 7.1 The recommended combination

**Core:** Perp Forge, the compiler and launchpad.

**Flagship:** Slashing Perp or Prediction Perp, generated by Forge.

**Trust layer:** Basis Oracle, which publishes the fair-value band and risk score.

**Narrative:** "We built the compiler that turns any payoff into a perp, and we used it to launch a perp that has never existed: perpetual slashing insurance."

This combination:

- Uses Paper 1 for the compiler, funding, and jump math.
- Uses Paper 2 for the anchor, risk bounds, and basis signals.
- Has a platform story and a concrete product story.
- Gives judges a live "math to market" demo.
- Leaves room for a spectacular finale: trigger a simulated slashing event and show the perp pay out.

### 7.2 Why this beats a single product

- A single perp is easy to dismiss as "another derivative."
- A compiler is a new category. It is defensible and extensible.
- The flagship proves the compiler works.
- The oracle makes the whole system credible.
- The demo has a clear beginning, middle, and end.

### 7.3 Team split

**Human 1: Quant and math lead.**

- Owns the symbolic engine, funding formulas, jump integral, and risk model.
- Reviews every formula against the papers.
- Defines the test vectors.

**Human 2: Protocol lead.**

- Owns the Solidity contracts, margin engine, oracle integration, and deployment.
- Reviews generated code.
- Owns security and invariants.

**Human 3: Frontend and demo lead.**

- Owns the Forge UI, charts, deployment flow, and the three-act demo.
- Makes the math legible.

**Human 4: Data and oracle lead.**

- Owns price feeds, realized covariance, event oracles, and the Basis Oracle keeper.
- Builds the mock and live data adapters.

**Human 5: Product, narrative, and partnerships lead.**

- Owns the pitch, judge Q&A, sponsor conversations, and the flagship use case.
- Coordinates the agents and the demo script.

### 7.4 Agent assignment

| Agent | Responsibility |
|---|---|
| Agent 1 | Payoff DSL parser and validator |
| Agent 2 | Symbolic differentiator and funding engine tests |
| Agent 3 | Solidity code generation |
| Agent 4 | Fuzzing, invariant tests, and security review |
| Agent 5 | Oracle adapters and covariance computation |
| Agent 6 | Monte Carlo and historical simulator |
| Agent 7 | Frontend components and charts |
| Agent 8 | Documentation, research notes, and citations |
| Agent 9 | Integration and end-to-end tests |
| Agent 10 | Demo assets, slides, and video |

### 7.5 Build order and dependency graph

1. Payoff DSL and parser.
2. Symbolic differentiation and funding engine.
3. Test vectors from Paper 1: variance, power, CFMM, jump.
4. Solidity code generation for a simple perp.
5. Oracle adapters and a mock index.
6. Forge UI with live charts.
7. Flagship perp: slashing or prediction.
8. Basis Oracle and risk panel.
9. Simulator and stress test.
10. Demo polish and judge narrative.

### 7.6 Three-act demo

**Act 1: The problem.**

- Perps are the largest crypto derivative, but launching a new perp is bespoke and slow.
- Funding is widely misunderstood, and most models ignore the clamp.
- Exotic risks such as slashing and event jumps have no continuous hedge.

**Act 2: The compiler.**

- Type a payoff into Forge.
- Show the funding curve, hedge ratios, and jump premium.
- Show the oracle specification and the generated contract.
- Deploy to a testnet.

**Act 3: The flagship.**

- Launch a Slashing Perp or Prediction Perp from Forge.
- Show the funding premium and the risk vault.
- Trigger a simulated slashing or event jump.
- Show the payout and the Basis Oracle risk band.
- End with the platform vision: any payoff, any perp, in minutes.

## 8. Judge narrative and Q&A

### 8.1 The one-sentence pitch

"We built the compiler and risk layer that turns any payoff into a perpetual, and we used it to launch perpetual slashing insurance, a instrument that did not exist before this weekend."

### 8.2 Why now

- Perps are the largest crypto derivative market.
- Restaking and slashing are large and growing.
- AI compute and DePIN create new commodity exposures.
- RWA and prediction markets are maturing.
- Onchain data and oracles are good enough to support exotic payoffs.

### 8.3 Judge questions and answers

**"Isn't this just another perp DEX?"**

No. We are not launching a single market. We are building the compiler and risk layer that makes new perp markets launchable. The flagship is proof of the compiler, not the whole product.

**"Why would anyone use a compiler instead of forking an existing perp?"**

Forking gives you code, not economics. Forge gives you the funding formula, hedge ratios, oracle spec, and risk report for your specific payoff. That is the hard part.

**"How do you prevent the generated code from being unsafe?"**

A constrained DSL, symbolic checks, invariant tests, fuzzing, and a human review gate. The generated code is a starting point, not an unaudited deployment.

**"How do you price a slashing perp?"**

Paper 1's jump funding formula. The funding integral is the event premium. The risk vault earns it and bears the jump risk. The payout is capped and collateralized.

**"What if the event oracle is wrong?"**

Use onchain slashing events where possible, optimistic dispute windows, multiple data sources, and circuit breakers. For ambiguous events, use a committee or a prediction-market oracle.

**"Is this legal?"**

Testnet prototype and research. Real-world derivatives and insurance require jurisdiction-specific compliance. We are not launching a live financial product at the hackathon.

**"What is the moat?"**

The payoff library, the symbolic engine, the oracle integrations, and the risk models. The compiler gets better with every new payoff.

**"What is the business model?"**

Protocol fees on launched perps, a SaaS/API for the compiler, and a risk/oracle subscription for protocols.

## 9. Kill criteria and red flags

- If the symbolic engine cannot handle a non-trivial payoff safely, drop code generation and focus on the design and simulator.
- If the flagship perp's oracle is unreliable, switch to a crypto-native event: token unlock, protocol upgrade, or LP payoff.
- If the scope is too large, cut the simulator and the Basis Oracle. Keep Forge plus one flagship.
- If legal concerns dominate, keep everything on testnet and frame it as research infrastructure.
- Avoid claiming risk-free yield, guaranteed profit, or production readiness.
- Avoid building a generic dashboard with no protocol primitive.

## 10. Alternative portfolios

### Portfolio A: Maximum novelty

- Perp Forge plus Shiller Perps.
- Why: the most "never made" narrative, strong RWA and impact angle.
- Risk: oracle and regulatory complexity.

### Portfolio B: Maximum demo

- Basis Mesh plus Agentic Copilot plus Basis Oracle.
- Why: live data, clear UI, strong AI narrative.
- Risk: crowded dashboard market; must lead with the oracle.

### Portfolio C: Maximum Ethereum-native

- Perp Forge plus Slashing Perp plus Blob Perp.
- Why: restaking and L2 data costs are Ethereum-native and sponsor-friendly.
- Risk: niche audiences; needs a strong demo.

### Portfolio D: Maximum spectacle

- Prediction Perp plus Agent Perp plus Perpetual Lottery.
- Why: memorable, consumer-friendly, viral.
- Risk: regulatory and oracle concerns; less technical depth.

### Portfolio E: Maximum technical depth

- Perp Forge plus Correlation Perp plus Perp Sim plus ZK proofs.
- Why: a quant and cryptography showcase.
- Risk: hard to explain to judges; weak demo.

## 11. Open research questions worth exploring during the build

- How do you compute realized covariance robustly onchain without a centralized oracle?
- How do you incorporate the funding cap into the no-arbitrage anchor?
- How do you price jumps when the jump distribution is unknown or adversarial?
- How do you prevent manipulation of event and RWA oracles?
- How do you design margin and liquidation for exotic payoffs?
- How do you handle adverse selection in perpetual insurance?
- How do you make generated contracts formally verifiable?
- How do you measure the capacity and decay of basis arbitrage?
- How do you design a basis factor index that is robust across venues?
- How do you prevent an AI copilot from hallucinating a trade?

## 12. Final recommendation

Build **Perp Forge plus one flagship perp**, with **Basis Oracle** as the trust layer. Choose the flagship based on the team's appetite:

- **Slashing Perp** if the team wants an Ethereum-native, high-narrative product with a spectacular jump-event demo.
- **Prediction Perp** if the team wants a consumer-friendly, viral product with continuous probability trading.
- **Shiller Perp** if the team wants the most conceptually novel, RWA-focused story.

The compiler is the platform. The flagship is the proof. The oracle is the moat. That is the combination most likely to be remembered, and the one that uses both papers at full depth.

## 13. Wave 2: liquidation and funding-feedback primitives

Papers 3 and 4 change the thesis. Papers 1 and 2 answer "what payoff and what price?" Papers 3 and 4 answer "does the hedge survive, and does the funding rule actually work?" The strongest 2026 ETHGlobal project is no longer just a compiler plus a flagship perp. It is a **perp risk stack**: design the payoff, anchor the price, keep the hedge alive, and calibrate the funding feedback.

### 13.1 LiquidMesh: liquidation-aware hedge autopilot and margin oracle

**Thesis.** Every bitcoin holder, DAO treasury, miner, restaker, and perp DEX needs to know not only the hedge ratio but the probability that the hedge is liquidated before it pays off. LiquidMesh computes both and publishes the survival score onchain.

**Paper foundation.** Paper 3, Theorem 3.2 and Section 4. Nonlinear optimal hedge `theta* = omega theta_0`, liquidation probability `P(m, theta)`, implied leverage, and the direct-versus-inverse venue choice.

**What appears never made.** Liquidation dashboards show price levels. A forward-looking survival-probability oracle plus a hedge optimiser plus a venue router is rare. Most DeFi risk tools are reactive; this is prescriptive.

**Why it could work.** Liquidation cascades are the defining risk of perps. A product that explicitly targets a liquidation-probability budget is easy to explain and easy to demo. It also has clear consumers: treasuries, vaults, lending protocols, and perp DEXs.

**Mechanism and oracle design.** Ingest spot exposure, venue, contract type, collateral, maintenance-margin tier, horizon, and loss aversion. Estimate GEV tails on rolling windows with cross-venue proxies. Solve the root equation for `theta_0`. Publish `theta*`, `P`, implied leverage, and a confidence band. Use signed reports, multiple data sources, and a dispute window.

**Build architecture.** Off-chain tail and optimiser engine; `LiquidationOracle.sol`; `HedgeVault.sol`; `MarginPolicy.sol`; `RiskRegistry.sol`; scenario lab; React dashboard; consumer adapter for a mock lending protocol.

**Demo moment.** A 100 BTC treasury runs a naive 1:1 hedge at 20x and gets liquidated in a 20% crash. LiquidMesh recommends a smaller inverse hedge with `m = 20%`, `gamma = 20`, shows 93% HE and sub-1% liquidation probability, and the treasury survives the same crash. The autopilot tops up margin when the survival score crosses a threshold.

**Moat.** Calibrated tails, venue-specific margin rules, the survival oracle integrations, and the execution track record.

**Risks.** Data quality, exchange rule changes, model risk, custody, and regulation. Keep execution advisory-first and capped.

**Score.** Novelty 4, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 28.

### 13.2 Funding DNA: effective-feedback oracle and funding-rule compiler

**Thesis.** A funding template is a controller; the market is the plant. Funding DNA measures the effective feedback actually delivered by a contract and compiles a rule that the market can absorb.

**Paper foundation.** Paper 4: nominal versus effective funding, capital-dependent stability band, weak/intermediate/strong feedback, liquidation-reduced corrective capacity, and flat/capped/interval-averaged regions.

**What appears never made.** Funding dashboards show rates. An oracle that publishes an effective-feedback vector, a stability band, and a cap-risk score is rare. A compiler that turns a target convergence half-life into rule parameters is rarer still.

**Why it could work.** Every perp DEX copies a funding template. The paper's core claim is that copying does not work. A calibration product has a clear buyer: any perp DEX that wants its basis to converge without exploding.

**Mechanism and oracle design.** Parse contract specs. Reconstruct delivered funding. Estimate the basis response to funding with capital and leverage controls. Classify the regime. Publish `FundingDNA` and `StabilityScore`. The compiler outputs a piecewise rule and a simulated stress report.

**Build architecture.** Data ingestion; template parser; effective-funding engine; feedback estimator; stability classifier; liquidation-capacity monitor; rule compiler; `FundingDNAOracle.sol`; `RuleRegistry.sol`; dashboard; simulator.

**Demo moment.** Two contracts with the same nominal rule diverge after a shock. Funding DNA shows one is in the flat band and the other is at the cap. The compiler produces a new rule; the same shock now mean-reverts without expanding reversals. Publish the new DNA onchain.

**Moat.** The cross-contract dataset, the estimator, the stability calibration, and the compiler. The oracle improves with every contract and regime.

**Risks.** Measurement error, endogeneity, data access, and overfitting. Show uncertainty and avoid causal overclaiming.

**Score.** Novelty 5, technical 5, demo 4, feasibility 4, judge 5, moat 5. Total 28.

### 13.3 Collateral Autopilot: margin top-up and deleverage engine

**Thesis.** The optimal hedge is only optimal if the margin account stays alive. A Collateral Autopilot monitors the survival score and recommends or executes top-ups, partial deleveraging, or venue rotation before liquidation.

**Paper foundation.** Paper 3, Sections 4.4 and 4.5. Liquidation probability and implied leverage are monotone in collateral and position size; the hedger can trade off top-up versus deleverage.

**What appears never made.** Stop-loss and liquidation bots exist. A theory-grounded policy that targets a liquidation-probability budget and chooses between top-up, deleverage, and venue rotation is rare.

**Why it could work.** It is the natural execution layer for LiquidMesh. It is also a strong risk/security narrative: preventing cascades rather than reacting to them.

**Mechanism and oracle design.** Consume LiquidMesh's survival score. Define a policy: if `P > P_max`, first top up to restore the buffer; if top-up is unavailable, reduce `theta`; if the venue is degraded, rotate. Enforce spend limits, cooldowns, and a multisig for large actions.

**Build architecture.** Policy engine; `CollateralVault.sol`; `SpendLimit.sol`; oracle consumer; alerting; testnet execution adapter.

**Demo moment.** A crash starts. The survival score falls. The autopilot tops up margin, then partially deleverages when the top-up limit is reached. The hedge survives; a naive bot is liquidated.

**Moat.** Policy library, integrations, and execution evidence.

**Risks.** Oracle failure, execution risk, and custody. Keep autonomous execution capped and auditable.

**Score.** Novelty 4, technical 4, demo 5, feasibility 4, judge 5, moat 4. Total 26.

### 13.4 Cascade Sim: liquidation and funding-feedback stress lab

**Thesis.** Perp risk is a coupled system: basis gaps cause liquidations, liquidations reduce arbitrage capital, weaker capital makes funding less effective, and weaker funding widens the gap. Cascade Sim lets teams stress-test this loop before launch.

**Paper foundation.** Paper 3 for margin, liquidation, and hedge survival; Paper 4 for effective funding, capital-dependent stability, and liquidation-reduced corrective capacity.

**What appears never made.** Perp simulators exist in isolation. A coupled liquidation-funding simulator with effective feedback and agent heterogeneity is rare.

**Why it could work.** Every perp DEX, vault, and risk team needs this. It is a developer tool with a dramatic demo: watch a market break, then fix the rule and watch it survive.

**Mechanism and oracle design.** Agent-based simulation with extrapolative traders, capital-constrained arbitrageurs, liquidity suppliers, and a piecewise funding rule. Scenario library: 20% crash, funding spike, cap hit, exchange freeze, oracle lag, pre-settlement attack.

**Build architecture.** Rust or Python engine; scenario library; web UI; report export; optional onchain attestation of stress results.

**Demo moment.** Stress a naive perp DEX. Liquidations spike, arbitrage capital collapses, the basis diverges, and the insurance fund drains. Change the funding rule and margin policy; rerun; the market survives.

**Moat.** Scenario library, calibration data, and trust.

**Risks.** Fidelity and scope. Label it a research simulator, not a validator of production safety.

**Score.** Novelty 4, technical 5, demo 5, feasibility 3, judge 5, moat 4. Total 26.

### 13.5 Basis Survival Vault: a basis trade that budgets for liquidation

**Thesis.** Basis arbitrage is usually presented as a yield strategy. It is also a survival problem. A vault that runs a basis trade with an explicit liquidation-probability budget and dynamic collateral is a better product than a naive carry vault.

**Paper foundation.** Paper 2 for the no-arbitrage band and deviation; Paper 3 for liquidation-aware sizing; Paper 4 for effective funding and cap risk.

**What appears never made.** Basis vaults exist. A vault that targets a survival probability, adapts to effective funding regimes, and routes across direct/inverse venues is rare.

**Why it could work.** Yield products are easy to understand; survival-aware yield is a differentiator. It also creates a real consumer for the oracles.

**Mechanism and oracle design.** Allocate across venues and contract types. Size each leg with LiquidMesh. Monitor Funding DNA and cap risk. Publish proof of reserves and a live survival score. Use a multisig and spend limits.

**Build architecture.** Vault contract; strategy engine; risk monitor; oracle adapters; attestation layer; UI.

**Demo moment.** Deposit testnet USDC. Run a historical replay through a liquidation cascade. Show the survival-aware vault preserving capital while a naive carry vault is liquidated. Show the funding DNA of each venue.

**Moat.** Track record, risk management, and oracle integrations.

**Risks.** Custody, regulation, execution, and oracle failure. Keep it a research prototype.

**Score.** Novelty 4, technical 5, demo 4, feasibility 4, judge 5, moat 4. Total 26.

### 13.6 Restaking Treasury Hedger

**Thesis.** Restakers and validators have ETH-denominated revenue and slashing risk. A liquidation-aware hedge with a survival budget is a concrete institutional product.

**Paper foundation.** Paper 3 for the hedge and liquidation model; Paper 1 for slashing jump funding; Paper 4 for funding feedback if the hedge uses perps.

**What appears never made.** Restaking insurance and hedge vaults exist. A combined slashing-cover plus liquidation-aware ETH hedge with an onchain survival score is rare.

**Why it could work.** Restaking is a large, Ethereum-native market. The narrative is strong and the user is identifiable.

**Mechanism and oracle design.** Hedge ETH price risk with inverse perpetuals; add slashing cover via a jump perp or a capped vault; size both with a liquidation budget; publish the combined survival score.

**Build architecture.** Hedge vault; slashing-cover vault; LiquidMesh oracle; beacon-chain event adapter; dashboard.

**Demo moment.** A restaker hedges ETH and buys slashing cover. A simulated price crash and a slashing event occur. The hedge survives; the cover pays; the treasury remains solvent.

**Moat.** Restaking relationships, risk models, and oracle integrations.

**Risks.** Correlated slashing, oracle ambiguity, and regulation. Cap payouts and stress-test correlated events.

**Score.** Novelty 5, technical 4, demo 5, feasibility 4, judge 5, moat 4. Total 27.

### 13.7 Funding Template A/B Lab

**Thesis.** Exchanges copy funding templates. A lab that A/B tests templates on historical and simulated data would let perp DEXs choose a rule with evidence instead of fashion.

**Paper foundation.** Paper 4's effective feedback and stability band; Paper 2's no-arbitrage anchor.

**What appears never made.** Backtesters exist. A public, cross-contract funding-template A/B lab with stability bands and cap-risk scores is rare.

**Why it could work.** It is a developer tool with a clear buyer and a strong visual: two rules, one shock, different outcomes.

**Mechanism and oracle design.** Ingest contract specs and market data. Simulate candidate rules. Score convergence half-life, cap frequency, liquidation impact, and manipulation cost. Publish a leaderboard and a report.

**Build architecture.** Simulation engine; template library; scoring engine; web UI; report export.

**Demo moment.** Run three templates against the same shock. One drifts, one converges, one oscillates. Show the stability band and the cap-risk score. Recommend the middle rule.

**Moat.** Template library, calibration data, and benchmark reputation.

**Risks.** Overfitting and data quality. Use out-of-sample tests and uncertainty bands.

**Score.** Novelty 4, technical 4, demo 4, feasibility 5, judge 4, moat 4. Total 25.

### 13.8 Perp Forge 2.0: the full risk stack

**Thesis.** Combine Papers 1-4 into one platform: design the payoff, anchor the price, keep the hedge alive, and calibrate the funding feedback. (Wave 3 extends this to Paper 5 under section 18.8.)

**Paper foundation.** All four.

**What appears never made.** A compiler that emits not only funding and hedge ratios but also a survival policy, a funding-DNA calibration, and a stress report is rare. It is a platform, not a single perp.

**Why it could work.** It is the natural evolution of Perp Forge and it gives the demo a complete arc: from a typed payoff to a live, risk-managed market.

**Mechanism and oracle design.** Forge emits:
1. Payoff and replication spec.
2. Funding and discount formulas.
3. Hedge ratios.
4. Liquidation-aware position sizing (LiquidMesh).
5. Effective-feedback calibration and rule parameters (Funding DNA).
6. Oracle spec and stress report.
7. Generated contracts and tests.

**Build architecture.** Payoff DSL; symbolic engine; funding compiler; risk compiler; funding-DNA compiler; code generator; simulator; oracle adapters; UI; testnet deployment.

**Demo moment.** Type a payoff. Forge emits the funding, hedge, survival policy, and funding rule. Launch the perp. Shock it. The basis converges, the hedge survives, and the oracle publishes the risk scores. End with the platform vision.

**Moat.** The compiler, the payoff library, the risk models, the oracles, and the integrations.

**Risks.** Scope and safety. Constrain the DSL, generate auditable code, and keep a human review gate.

**Score.** Novelty 5, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 29.

## 14. Wave 2 scoring matrix

| Idea | Papers | Novelty | Tech | Demo | Feasibility | Judge | Moat | Total |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| Perp Forge 2.0 | 1+2+3+4 | 5 | 5 | 5 | 4 | 5 | 5 | 29 |
| LiquidMesh | 3 | 4 | 5 | 5 | 4 | 5 | 5 | 28 |
| Funding DNA | 4 | 5 | 5 | 4 | 4 | 5 | 5 | 28 |
| Restaking Treasury Hedger | 1+3+4 | 5 | 4 | 5 | 4 | 5 | 4 | 27 |
| Collateral Autopilot | 3 | 4 | 4 | 5 | 4 | 5 | 4 | 26 |
| Cascade Sim | 3+4 | 4 | 5 | 5 | 3 | 5 | 4 | 26 |
| Basis Survival Vault | 2+3+4 | 4 | 5 | 4 | 4 | 5 | 4 | 26 |
| Funding Template A/B Lab | 2+4 | 4 | 4 | 4 | 5 | 4 | 4 | 25 |

## 15. Recommended build portfolio 2.0 (supersedes section 12)

### 15.1 The stack

**Core platform:** Perp Forge 2.0.

**Flagship instrument:** a perp that could not exist without the new risk layer. The strongest choice is a **Slashing Perp with a liquidation-aware risk vault**, or a **Prediction Perp with a survival-budget market maker**.

**Risk layer:** LiquidMesh.

**Calibration layer:** Funding DNA.

**Trust layer:** Basis Mesh / Basis Oracle.

**Narrative:** "Perps are not just derivatives. They are control systems. We built the compiler that designs the payoff, the oracle that anchors the price, the risk layer that keeps the hedge alive, and the calibration layer that makes the funding rule actually work."

### 15.2 Why this beats the Paper 1+2 portfolio

- It answers the two questions judges always ask about a perp demo: "What happens in a crash?" and "Does the funding actually work?"
- It turns a dashboard into a control system with measurable outputs: survival probability, effective feedback, stability band, and cap risk.
- It gives the demo a dramatic middle: a liquidation cascade and a funding failure, followed by a fix.
- It creates three consumable oracles, not one, which is a stronger protocol story.
- It uses all four papers in the 2.0 stack at full depth without feeling like a literature review.

### 15.3 Team split for 5 humans and 10 agents

**Human 1: Quant and math lead.** Owns the payoff funding formulas (Paper 1), the no-arbitrage anchor (Paper 2), the liquidation-aware hedge (Paper 3), and the effective-feedback estimator (Paper 4). Defines test vectors and validates every number.

**Human 2: Protocol lead.** Owns the Solidity contracts: perp engine, margin engine, oracles, vaults, and generated code. Owns security, invariants, and deployment.

**Human 3: Frontend and demo lead.** Owns the Forge UI, the LiquidMesh heatmaps, the Funding DNA comparison, charts, and the three-act demo.

**Human 4: Data and oracle lead.** Owns minute-level CEX/DEX data, liquidation feeds, GEV estimation, funding-DNA estimation, keeper infrastructure, and oracle publication.

**Human 5: Product, narrative, and partnerships lead.** Owns the pitch, the flagship use case, judge Q&A, sponsor conversations, and coordination of the agents.

**Agent 1:** payoff DSL parser and validator.

**Agent 2:** symbolic differentiator and funding engine tests.

**Agent 3:** Solidity code generation and contract templates.

**Agent 4:** fuzzing, invariants, and security review.

**Agent 5:** oracle adapters and covariance/tail estimation.

**Agent 6:** Monte Carlo and historical simulator.

**Agent 7:** frontend components and charts.

**Agent 8:** documentation, citations, and research notes.

**Agent 9:** integration and end-to-end tests.

**Agent 10:** demo assets, slides, and video.

### 15.4 Build order and dependency graph

1. Payoff DSL, parser, and test harness.
2. Symbolic differentiation and funding engine (Paper 1).
3. No-arbitrage band and basis index (Paper 2).
4. GEV tail engine and liquidation model (Paper 3).
5. LiquidMesh optimiser and survival oracle.
6. Effective-funding parser and Funding DNA estimator (Paper 4).
7. Funding-rule compiler.
8. Solidity code generation and perp engine.
9. Flagship perp plus risk vault.
10. Cascade Sim and stress reports.
11. Basis Mesh and consumer integrations.
12. Demo polish, judge narrative, and video.

### 15.5 Three-act demo 2.0

**Act 1: The illusion.** Perps look simple: post margin, short the perp, collect carry. Then a 20% crash liquidates the hedge, the funding rule hits its cap, and the basis diverges. The treasury is naked and the market is unstable. The judge sees the two failure modes the papers identify.

**Act 2: The stack.** Type a payoff into Forge. Forge emits the funding formula, hedge ratios, liquidation-aware sizing, the funding-DNA calibration, the oracle spec, and the generated contract. LiquidMesh shows the survival score. Funding DNA shows the effective feedback and stability band. Deploy to testnet.

**Act 3: The fix.** Shock the market again. The basis converges instead of diverging. The hedge survives the crash. The autopilot tops up margin or deleverages within its budget. The oracles update. End with the platform vision: any payoff, any market, calibrated and survival-aware.

### 15.6 Judge Q&A for the unified stack

**"Why these four papers?"** Each answers a different failure mode: what to build (Paper 1), how to price it (Paper 2), how to survive (Paper 3), and how to make the funding work (Paper 4). The product is the integration, not a summary. Wave 3 adds Paper 5 for execution.

**"Isn't this too much for a hackathon?"** We scope it as a working compiler, one flagship perp, and three oracles, with the simulator as a stretch goal. The modules are separable; the demo shows the full loop.

**"What is the one primitive?"** The **perp risk stack**: a compiler plus three oracles (survival, funding DNA, basis). The flagship is the proof.

**"What is the moat?"** The calibrated models, the cross-contract dataset, the oracle integrations, and the generated-contract library. The compiler gets better with every payoff and every market.

**"How do you avoid overclaiming?"** Every output has a confidence band and a model-risk disclosure. The hackathon version is testnet-only and advisory-first.

**"What if the oracle is wrong?"** Multiple data sources, signed reports, dispute windows, circuit breakers, and a human review gate for generated contracts.

### 15.7 Kill criteria

- If the DSL cannot safely handle a non-trivial payoff, cut code generation and keep the compiler as a design and simulation tool.
- If the flagship oracle is unreliable, use a crypto-native event: a token unlock, a protocol upgrade, or an LP payoff.
- If the scope is too large, keep Forge plus LiquidMesh plus one flagship; cut Funding DNA and Cascade Sim.
- If funding-DNA estimation is too noisy, ship it as a research dashboard and keep the oracle advisory.
- If legal concerns dominate, keep everything on testnet and frame it as research infrastructure.
- Never claim risk-free yield, guaranteed survival, or production readiness.

## 16. Why this portfolio can win ETHGlobal

1. **It has a thesis, not a feature list.** "Perps are control systems; we build the compiler and the control layer" is a memorable frame.
2. **It uses the math visibly.** The demo shows the funding formula, the survival probability, the stability band, and the generated contract. Judges can see the papers in the product.
3. **It has a crisis.** The liquidation cascade and the funding failure give the demo tension and a clear before/after.
4. **It has three moats.** The compiler, the oracles, and the calibrated dataset. Any one of them is a plausible startup; together they are a platform.
5. **It fits multiple sponsor tracks.** Derivatives, oracles, risk/security, AI agents, Ethereum restaking, and data analytics.
6. **It is honest about risk.** Confidence bands, model-risk disclosures, testnet-only claims, and a human review gate make the project credible to technical judges.
7. **It is extensible.** Every new payoff, venue, or funding rule improves the compiler and the oracles. The hackathon is the beginning, not the whole product.

## 17. Open research questions after Papers 3 and 4

- How do you estimate a liquidation-probability oracle robustly when exchange liquidation data is incomplete or manipulated?
- How do you propagate GEV parameter uncertainty into the optimal hedge and the survival score?
- How do you model auto-deleveraging, insurance funds, and socialised losses in the hedge problem?
- How do you identify effective funding feedback causally, rather than as a correlation?
- How do you measure arbitrage capital and leverage constraints onchain or from public data?
- How do you design a funding rule that is stable across regimes without overfitting?
- How do you price the interaction between funding caps and liquidation cascades?
- How do you make a rule compiler safe when the generated rule controls real money?
- How do you aggregate survival, funding, and basis signals into one risk score without hiding model risk?
- How do you prevent an AI copilot from hallucinating a hedge or a rule change?

## 18. Wave 3: cross-venue execution and the two-tier market

Paper 5 adds the missing layer: even a correctly designed, priced, calibrated, and survival-aware perp trade can fail because the venue structure prevents execution. Funding-rate markets are two-tiered: CEX leads, DEX follows, and the CEX-DEX boundary is where most large spreads live. The paper also delivers the most useful negative result in the stack: **a visible spread is not an executable profit**. The winning product is therefore not a scanner. It is an execution and risk system that decides which spreads are real, which are survivable, and which should be rejected.

### 18.1 TierBridge: execution-aware CEX-DEX funding arbitrage router

**Thesis.** Detect CEX-led funding dislocations, price the full cost of crossing to a DEX, predict the reversal hazard, and route only trades whose expected net value survives costs and forced exits.

**Paper foundation.** Paper 5, Sections 3.4-3.5 and 4.7. Gross spread vs net P&L, 8h normalization, CEX-to-DEX lead-lag, duration, reversal patterns, forced exits, and transaction-cost thresholds.

**What appears never made.** Funding scanners and basis dashboards exist. A router that combines lead-lag, executable cost, duration, and reversal hazard into a single decision is rare. Most products show the spread and stop there.

**Why it could work.** The paper's numbers are a ready-made demo: 17% of observations look like opportunities, but only 8 of 20 top trades are profitable after costs, and the average Sharpe is -7.40. A product that fixes that gap has an obvious before/after.

**Mechanism and oracle design.** Normalize funding; estimate CEX-to-DEX transmission by symbol; compute break-even time from fees, slippage, gas, and latency; estimate reversal hazard; rank routes by expected net value; enforce exposure and venue limits; publish the executable net spread and hazard score onchain.

**Build architecture.** Data ingestion; normalizer; lead-lag engine; cost engine; duration model; reversal-hazard model; router; exit engine; `FundingTierOracle.sol`; `RouteRegistry.sol`; `RiskBudget.sol`; CEX sandbox and DEX testnet adapters; React dashboard.

**Demo moment.** A naive bot enters a 200 bps spread and loses after fees and a reversal. TierBridge waits for the CEX lead, computes a 40-minute expected duration and a 60 bps net edge, enters, and exits before the spread flips. Show the rejected route next to the accepted route.

**Moat.** The cross-venue dataset, the lead-lag and hazard models, the cost model, and the execution integrations.

**Risks.** CEX API restrictions, custody, latency, data quality, regulatory compliance, and model risk. Hackathon version: simulator plus oracle, sandbox execution only.

**Score.** Novelty 5, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 29.

### 18.2 Tier Oracle: a two-tier market-structure oracle

**Thesis.** Publish the structure of the funding market itself: CEX-DEX integration, lead-lag scores, executable spreads, and reversal hazard. Other protocols can consume the market-structure signal instead of rebuilding it.

**Paper foundation.** Paper 5, Sections 4.5-4.6. CEX-CEX, CEX-DEX, and DEX-DEX correlations; Granger causality; 61% integration gap.

**What appears never made.** Price oracles are common; funding oracles are emerging; a market-structure oracle that publishes which tier leads and how strong the transmission is rare.

**Why it could work.** It is a public good with clear consumers: perp DEXs setting funding, basis vaults choosing venues, risk desks monitoring fragmentation, and researchers tracking integration.

**Mechanism and oracle design.** Ingest funding rates, compute normalized spreads, estimate rolling correlations and lead-lag, publish a `TierScore`, `LeadLagScore`, and `ExecutableSpread`. Use multiple data sources, outlier detection, and a dispute window.

**Build architecture.** Keeper network; `TierOracle.sol`; data adapters; methodology registry; consumer demo with a mock perp DEX.

**Demo moment.** A perp DEX reads the Tier Oracle and adjusts its funding update frequency when the CEX lead-lag score says it is a price taker. Show the oracle updating through a CEX funding shock.

**Moat.** Data, methodology, and integrations.

**Risks.** Oracle manipulation and adoption. Keep the signal advisory and publish the inputs.

**Score.** Novelty 4, technical 4, demo 3, feasibility 5, judge 4, moat 5. Total 25.

### 18.3 Reversal Hazard Engine: predict the forced exit before it happens

**Thesis.** The paper's central risk is not the spread level; it is the reversal. A model that predicts the probability and timing of a spread sign flip is the difference between a profitable router and a naive carry trade.

**Paper foundation.** Paper 5, Section 4.7: 95% of opportunities show reversal patterns; forced exits occur for 12 of 20; exit threshold choice dominates profitability.

**What appears never made.** Stop-loss bots exist. A funding-specific reversal-hazard model that uses lead-lag, duration, spread shape, liquidation, and venue state is rare.

**Why it could work.** It is the core IP of TierBridge and a standalone oracle. It also creates a measurable claim: hazard score versus realised reversal.

**Mechanism and oracle design.** Features: spread level, spread slope, time above threshold, CEX-DEX lead-lag, funding interval, cap proximity, liquidation flow, open interest, venue outage, and volatility. Model: survival analysis or gradient-boosted hazard. Output: probability of reversal within the next 15/30/60 minutes and an expected exit time.

**Build architecture.** Feature store; hazard model; backtest with time-stamped data; `ReversalHazardOracle.sol`; dashboard; API.

**Demo moment.** The hazard score rises before a spread flips. The naive bot stays in and loses; the router exits and keeps the funding. Show the score's calibration curve.

**Moat.** The labelled dataset, the feature set, and the calibration track record.

**Risks.** Overfitting and regime change. Use walk-forward validation and publish uncertainty.

**Score.** Novelty 4, technical 5, demo 4, feasibility 4, judge 5, moat 5. Total 27.

### 18.4 ArbOS: the agentic cross-venue arbitrage operating system

**Thesis.** An agent that scans the two-tier market, explains why a spread exists, computes the cost and hazard, proposes a route, and executes only after human approval. It is a research analyst and an execution assistant in one.

**Paper foundation.** Paper 5's two-tier structure, cost model, and reversal risk; Papers 2-4 for the anchor, survival, and funding calibration.

**What appears never made.** Trading bots exist. A theory-grounded, explainable, guardrailed agent that reasons about venue structure and refuses uneconomic trades is rare.

**Why it could work.** AI agents are a dominant narrative, and the paper gives the agent a real edge: it can distinguish a CEX-led dislocation from a DEX-led noise move and reject trades that fail the duration test.

**Mechanism and oracle design.** Deterministic signal engine plus an LLM for explanation and tool use. Tools: spread scanner, cost calculator, hazard model, route simulator, risk limits. Human approval for execution. No autonomous custody in the hackathon.

**Build architecture.** Agent framework; tool layer; data warehouse; risk guardrails; execution sandbox; audit log.

**Demo moment.** The agent finds a 300 bps spread, explains that it is DEX-led and likely noise, rejects it, then finds a CEX-led spread with a high hazard-adjusted edge and proposes a route. The human approves; the sandbox executes.

**Moat.** The tooling, guardrails, and audit trail.

**Risks.** Hallucination and over-trust. Keep execution human-approved and deterministic.

**Score.** Novelty 5, technical 4, demo 5, feasibility 4, judge 5, moat 4. Total 27.

### 18.5 Cross-venue delta-neutral basis vault

**Thesis.** A vault that runs only executable CEX-DEX funding trades, budgets for forced exits, and publishes a live survival and hazard score.

**Paper foundation.** Paper 5's portfolio results and cost model; Paper 3's liquidation-aware sizing; Paper 4's effective funding.

**What appears never made.** Basis vaults exist. A vault that rejects most "opportunities" because they fail the cost/duration test, and publishes the rejection log, is rare.

**Why it could work.** It is a concrete yield product with a transparent edge and a strong risk narrative. It also demonstrates the paper's central point: discipline beats spread-chasing.

**Mechanism and oracle design.** Route through TierBridge; size each leg with LiquidMesh; require a minimum net edge and maximum hazard; cap exposure per venue; exit on hazard or negative spread; publish proof of reserves and realised versus expected P&L.

**Build architecture.** Vault contract; router integration; risk monitor; attestation; UI.

**Demo moment.** A naive vault chases a 200 bps spread and loses. The disciplined vault rejects it, takes a smaller CEX-led trade, and exits before the reversal. Show the rejection log and the net P&L.

**Moat.** Track record, risk management, and integrations.

**Risks.** Custody, regulation, execution, and counterparty risk. Research prototype only.

**Score.** Novelty 4, technical 5, demo 4, feasibility 4, judge 5, moat 4. Total 26.

### 18.6 Two-Tier Explorer: a live market-structure terminal

**Thesis.** A Bloomberg-style terminal for the CEX-DEX funding market: integration heatmaps, lead-lag networks, executable spreads, hazard scores, and venue risk.

**Paper foundation.** Paper 5's correlation and Granger results; Paper 2's basis band; Paper 4's effective funding.

**What appears never made.** Funding dashboards exist. A terminal that explicitly models the two-tier structure, lead-lag, and executable net spread is rare.

**Why it could work.** It is the demo layer for TierBridge and the Tier Oracle. It is also a standalone research product for funds and venues.

**Mechanism and oracle design.** Ingest data; compute normalized spreads, correlations, lead-lag, cost-adjusted spreads, and hazard; visualise the CEX and DEX tiers and the boundary.

**Build architecture.** Data pipeline; analytics engine; React dashboard; alerts; export.

**Demo moment.** The heatmap shows the CEX block and the fragmented DEX block. A shock hits Binance; the lead-lag arrows light up toward Hyperliquid; the executable spread panel shows which routes survive costs.

**Moat.** Data, visualisation, and integrations.

**Risks.** Crowded dashboard market. Lead with the oracle and router, not the charts.

**Score.** Novelty 3, technical 4, demo 5, feasibility 5, judge 4, moat 3. Total 24.

### 18.7 CEX-DEX market-structure simulator

**Thesis.** Simulate how venue design, latency, fees, and funding rules affect integration and arbitrage. Let a DEX test whether faster oracles, lower fees, or deeper liquidity would change its tier.

**Paper foundation.** Paper 5's two-tier results; Paper 4's funding feedback; Paper 3's liquidation.

**What appears never made.** Agent-based market simulators exist. A CEX-DEX funding simulator with lead-lag, costs, and reversal dynamics is rare.

**Why it could work.** It is a tool for DEX designers and a strong visual demo: change the latency and watch the integration gap close.

**Mechanism and oracle design.** Agents: CEX market makers, DEX liquidity providers, arbitrageurs, extrapolative traders. Frictions: latency, fees, gas, bridge time, capital. Outputs: integration, spread persistence, arbitrage P&L, forced exits.

**Build architecture.** Simulation engine; scenario library; web UI; report export.

**Demo moment.** Start with a slow DEX; the integration gap is large and spreads persist. Cut latency and fees; the gap narrows and the naive arbitrage profits disappear. Show the DEX becoming a price leader in one scenario.

**Moat.** Scenario library and calibration.

**Risks.** Fidelity. Label it a research tool.

**Score.** Novelty 4, technical 5, demo 5, feasibility 3, judge 5, moat 4. Total 26.

### 18.8 Perp Forge 3.0: design, price, calibrate, survive, execute

**Thesis.** The full stack. Forge designs the payoff; Basis Mesh anchors the price; Funding DNA calibrates the funding; LiquidMesh keeps the hedge alive; TierBridge executes and exits. The platform covers the entire lifecycle of a perpetual market.

**Paper foundation.** All five papers.

**What appears never made.** A compiler that emits a complete market specification plus risk and execution oracles is rare. It is the natural end state of the research program.

**Why it could work.** It is a platform story with five live modules and a flagship instrument. Each module is independently demoable, and together they tell a complete story.

**Mechanism and oracle design.** Forge emits:
1. Payoff and replication.
2. Funding and discount formulas.
3. No-arbitrage band and basis index.
4. Effective-feedback calibration.
5. Liquidation-aware sizing and survival score.
6. Cross-venue route and exit policy.
7. Oracle spec, stress report, generated contracts, and tests.

**Build architecture.** Payoff DSL; symbolic engine; funding compiler; basis oracle; funding-DNA engine; liquid-mesh engine; tier-bridge router; code generator; simulator; UI; testnet deployment.

**Demo moment.** Type a payoff. Forge emits the full market spec. Launch the perp. A shock hits. The funding converges, the hedge survives, and the router exits the cross-venue leg before the reversal. End with the platform vision.

**Moat.** The compiler, the five oracles, the dataset, and the integrations.

**Risks.** Scope and safety. Constrain the DSL, generate auditable code, keep a human review gate.

**Score.** Novelty 5, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 29 (30 if the execution demo lands).

## 19. Wave 3 scoring matrix

| Idea | Papers | Novelty | Tech | Demo | Feasibility | Judge | Moat | Total |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| Perp Forge 3.0 | 1+2+3+4+5 | 5 | 5 | 5 | 4 | 5 | 5 | 29-30 |
| TierBridge | 5 | 5 | 5 | 5 | 4 | 5 | 5 | 29 |
| ArbOS | 2+3+4+5 | 5 | 4 | 5 | 4 | 5 | 4 | 27 |
| Reversal Hazard Engine | 5 | 4 | 5 | 4 | 4 | 5 | 5 | 27 |
| Cross-venue Basis Vault | 3+4+5 | 4 | 5 | 4 | 4 | 5 | 4 | 26 |
| CEX-DEX Simulator | 3+4+5 | 4 | 5 | 5 | 3 | 5 | 4 | 26 |
| Tier Oracle | 5 | 4 | 4 | 3 | 5 | 4 | 5 | 25 |
| Two-Tier Explorer | 2+4+5 | 3 | 4 | 5 | 5 | 4 | 3 | 24 |

## 20. Recommended build portfolio 3.0 (supersedes section 15)

### 20.1 The stack

**Core platform:** Perp Forge 3.0.

**Flagship instrument:** a Slashing Perp or Prediction Perp generated by Forge, launched with a liquidation-aware risk vault.

**Risk layer:** LiquidMesh.

**Calibration layer:** Funding DNA.

**Execution layer:** TierBridge.

**Trust layer:** Basis Mesh / Tier Oracle.

**Narrative:** "We built the full lifecycle for a perpetual market: design the payoff, anchor the price, calibrate the funding, keep the hedge alive, and execute the cross-venue trade. Five papers, one stack."

### 20.2 Why this beats the 2.0 portfolio

- It closes the last gap in the demo: the trade actually executes and exits.
- It turns the paper's negative result into a product feature: TierBridge rejects most opportunities, which is exactly what a risk-aware judge wants to see.
- It adds a second crisis to the demo: not only a liquidation cascade, but a fake arbitrage that looks profitable and is not.
- It makes the oracle suite complete: basis, funding, survival, hazard, and market structure.
- It positions the project as infrastructure for the CEX-DEX boundary, which is the most active and least tooled part of the market.

### 20.3 Team split update for 5 humans and 10 agents

The 2.0 split still holds. Add these responsibilities:

**Human 4 (data/oracle lead):** owns cross-venue data, 8h normalization, CEX-DEX lead-lag, cost tables, and the Tier Oracle.

**Human 5 (product/narrative):** owns the two-tier story and the CEX-DEX execution demo.

**Agent 5:** cross-venue adapters and cost model.

**Agent 6:** simulator plus reversal-hazard backtest.

**Agent 10:** demo assets, including the naive-versus-router side-by-side.

If the team can support one more agent, add **Agent 11: execution sandbox and CEX API simulator**. If not, Human 2 owns it.

### 20.4 Build order update

1. Payoff DSL and funding engine (Paper 1).
2. Basis band and basis index (Paper 2).
3. GEV tail and liquidation model (Paper 3).
4. LiquidMesh optimiser and survival oracle (Paper 3).
5. Funding DNA estimator and rule compiler (Paper 4).
6. Cross-venue normalizer and cost engine (Paper 5).
7. Lead-lag and reversal-hazard models (Paper 5).
8. TierBridge router and exit engine (Paper 5).
9. Solidity oracles and perp engine.
10. Flagship perp plus risk vault.
11. Cascade and CEX-DEX simulators.
12. Demo polish and judge narrative.

### 20.5 Three-act demo 3.0

**Act 1: Two failures.** A treasury hedge is liquidated in a crash. A naive funding arbitrage enters a 200 bps CEX-DEX spread and loses after costs and a reversal. The judge sees the two ways perp trading fails: the hedge dies, and the "free money" is not free.

**Act 2: The stack.** Forge designs the perp. Basis Mesh anchors it. Funding DNA calibrates the funding. LiquidMesh sizes the hedge. TierBridge finds a CEX-led dislocation, computes the cost and hazard, and rejects three routes before accepting one. Deploy to testnet.

**Act 3: The execution.** Shock the market. The funding converges, the hedge survives, and TierBridge exits the cross-venue leg before the spread flips. The oracles update. End with the platform vision: the full lifecycle, from payoff to execution.

### 20.6 Judge Q&A additions

**"Why add cross-venue execution to a perp compiler?"** Because a perp is not just a payoff; it is a market. The market fails if the hedge is liquidated or if the arbitrage that anchors it is uneconomic. TierBridge closes the loop.

**"Isn't the paper's result that arbitrage is unprofitable?"** Yes, for naive strategies. TierBridge is built on that result: it rejects most spreads and only routes the rare high-spread, long-duration, CEX-led trades.

**"How do you know the spread is executable?"** We price fees, slippage, gas, transfer latency, and expected duration, then require a positive net edge after a hazard haircut.

**"What if the CEX leg is not accessible?"** The demo uses a sandbox. The product can run as an oracle and simulator for venues that cannot custody funds.

**"What is the one-sentence pitch?"** "We built the full lifecycle for perpetual markets, from payoff design to survival-aware hedging to executable CEX-DEX arbitrage."

### 20.7 Updated kill criteria

- If cross-venue execution is too hard, keep TierBridge as a simulator and oracle; cut live execution.
- If the hazard model is too noisy, use a simple duration threshold and publish it as a heuristic.
- If the flagship perp oracle is unreliable, use a crypto-native event.
- If the scope is too large, keep Forge + LiquidMesh + TierBridge and cut Funding DNA / Cascade Sim.
- Never claim risk-free arbitrage, guaranteed survival, or production readiness.

## 21. Why the 3.0 stack can win ETHGlobal

1. **It has a complete lifecycle story.** Design, price, calibrate, survive, execute. No other team will tell all five in one demo.
2. **It has two crises.** A liquidation and a fake arbitrage. The judge sees the product solve both.
3. **It has five oracles.** Basis, funding, survival, hazard, and market structure. That is a protocol story, not a dashboard.
4. **It is built on a negative result.** TierBridge's willingness to reject trades is a credibility signal.
5. **It fits every relevant track.** Derivatives, oracles, risk, AI agents, data, Ethereum/L2, and market microstructure.
6. **It is honest.** The paper's own inconsistencies and limitations are documented, and the product does not overclaim.
7. **It is extensible.** Every new venue, symbol, or funding rule improves the compiler and the oracles.

## 22. Open questions after Paper 5

- How do you predict spread reversals out of sample without overfitting?
- How do you measure executable spread when the top of book is not available in the required size?
- How do you model CEX-DEX capital transfer and bridge latency in real time?
- How do you handle CEX API restrictions, custody, and compliance in a live product?
- How do you distinguish CEX funding leadership from underlying spot price discovery?
- How do you reconcile the paper's zero-reverse-causality claim with its own Table 6?
- How do you design a DEX funding mechanism that reduces its lag without creating instability?
- How do you aggregate hazard, survival, funding, and basis signals into one risk score?
- How do you prevent an agent from over-trusting a high gross spread?
- How do you make the router robust to venue outages and liquidation cascades?
