# A Shared Template Without Shared Feedback: Funding Rates in Cryptocurrency Perpetual Futures

## Metadata

- **Authors:** Sicheng He, Shirui Wang, Tianyang Zhang (contact author, Zhongnan University of Economics and Law)
- **Working paper:** SSRN Electronic Journal
- **DOI:** 10.2139/ssrn.6185958
- **SSRN abstract ID:** 6185958
- **Posted:** 25 February 2026
- **Last revised:** 5 September 2026
- **Date written:** 30 August 2026
- **Length:** 59 pages
- **JEL classification:** G12, G13, G14, D47
- **Keywords:** Cryptocurrency, perpetual futures, funding rate, futures basis, limits to arbitrage
- **Type:** Theory plus empirical asset pricing / market microstructure. Structural model with extrapolative traders, capital-constrained arbitrageurs, liquidity suppliers, and a piecewise funding rule; panel evidence from 200 Binance USDT-margined perpetuals.
- **Read status:** The SSRN abstract page, Crossref metadata, and the full 25-item reference list were retrieved. The full 59-page PDF could not be retrieved because SSRN's content-protection layer blocked every direct, archived, and proxied download attempt. This file therefore separates **(a) verified claims from the abstract and metadata** from **(b) an analytical reconstruction** that is clearly marked as ours, not the authors'. No equation or numerical result below is attributed to the paper unless it is explicitly marked as verified.

> **Access note.** The user-supplied signed SSRN URL expired before it could be fetched, and SSRN returned a bot-protection page with the message "Content Blocked ... automated script or search engine". The paper is not on arXiv, OpenAlex has no alternate PDF, Semantic Scholar has no record, and the Wayback Machine archived the abstract page but not the PDF. This is a genuine access limitation, not a choice. The verified findings are still detailed and the reconstruction is designed to be useful for ideation, but it should not be cited as a verbatim summary of the authors' sections.

## One-paragraph summary

Perpetual futures have no maturity, so exchanges use a funding rate to pull the perpetual price toward spot. The paper's central claim is that a common funding template does not produce a common feedback response. The authors build a model with extrapolative traders, capital-constrained arbitrageurs, liquidity suppliers, and a piecewise funding rule. The model generates a capital-dependent stability band. Weak funding feedback leaves price gaps persistent; intermediate feedback stabilises the basis, with or without the perpetual price crossing spot; feedback that is too strong generates expanding reversals. Losses and liquidations reduce arbitrageurs' future corrective capacity, so stabilisation can become infeasible. A binding leverage constraint can also prevent stronger funding from expanding corrective positions. Empirically, using 200 Binance USDT-margined perpetuals, the paper reports that the nominal funding rule does not by itself determine effective feedback, basis reversals are associated with directional liquidation, and rate caps accompany persistent price gaps. The title captures the punchline: exchanges copy the same funding template, but the effective feedback delivered at settlement depends on the asset, the trader mix, liquidity, capital, and the path of the basis.

## Verified findings from the abstract and metadata

These points are directly supported by the SSRN abstract and the Crossref/SSRN metadata.

### The motivation

- Perpetual futures lack maturity.
- Exchanges use funding payments to keep perpetual prices close to spot.
- The funding rule is a template with a piecewise structure, including flat and capped regions, and it is applied over settlement intervals.
- The same template is widely copied across contracts and venues.
- The paper asks whether the template actually delivers the same corrective feedback everywhere.

### The model ingredients

- **Extrapolative traders:** traders whose demand chases past price or basis movements.
- **Capital-constrained arbitrageurs:** arbitrageurs who can correct the basis only up to their capital and leverage limits.
- **Liquidity suppliers:** agents who provide liquidity and whose behaviour depends on the state of the market.
- **A piecewise funding rule:** the rule has regions, including a flat region and a capped region, and funding is applied at settlement intervals.

### The core theoretical result

- The model yields a **capital-dependent stability band**.
- **Weak feedback:** price gaps remain persistent.
- **Intermediate feedback:** gaps stabilise, with or without the perpetual price crossing spot.
- **Strong feedback:** the system generates expanding reversals.
- **Losses and liquidation** reduce future corrective capacity and can make stabilisation infeasible.
- **A binding leverage constraint** can prevent stronger funding from expanding corrective positions.

### The empirical claims

- Evidence from **200 Binance USDT-margined perpetuals**.
- The **nominal funding rule does not by itself determine effective feedback**.
- **Basis reversals are associated with directional liquidation**.
- **Rate caps accompany persistent price gaps**.
- The results help explain why a shared funding template does not guarantee shared feedback.

### The design implications

- **Pre-settlement trading**, **interval averaging**, and the rule's **flat and capped regions** separate the nominal template from the funding response actually delivered at settlement.
- A funding rule should be judged by its **effective feedback**, not by its printed formula.
- Two contracts with the same template can have different stability properties because their capital, liquidity, trader composition, and basis paths differ.

## What the paper is arguing, in plain language

A funding rate is often described as a spring that pulls the perpetual price back to spot. The paper says the spring has a maximum force, a dead zone, and a delay:

1. **Dead zone.** If the basis is inside the flat region, funding is zero or tiny. The spring does nothing. A small persistent gap can survive indefinitely.
2. **Delay.** Funding is computed over an interval and paid at settlement. Traders can position ahead of settlement, and the average premium may differ from the premium at the moment of payment. The spring acts on a lagged, averaged signal.
3. **Maximum force.** The cap limits how much funding can be charged. In a large dislocation, the spring cannot pull harder. The gap can persist even though the formula looks aggressive.
4. **Weak or absent arbitrage capital.** The spring only works if arbitrageurs are willing and able to take the other side. If they have lost capital, been liquidated, or are leverage-constrained, the spring has nothing to pull against.
5. **Too much force.** If funding is very sensitive to the basis, it can overshoot. The perpetual price crosses spot, the basis reverses, extrapolative traders pile in, and the correction itself becomes destabilising.

The paper's contribution is to put these pieces into one model and show that the **feedback strength** is an equilibrium object, not a parameter chosen by the exchange. The exchange chooses the template; the market determines how much correction is actually delivered.

## Analytical reconstruction (ours, not the authors')

The following is a minimal formalisation that reproduces the qualitative regimes described in the abstract. It is **not** the authors' model and should not be cited as such. It is useful for building a simulator, an oracle, or a hackathon demo.

### State variables

- `S_t`: spot price.
- `F_t`: perpetual price.
- `b_t = log(F_t / S_t)`: log basis. Positive means the perp trades at a premium.
- `K_t`: arbitrage capital, measured in units of risk capacity.
- `L_t`: leverage limit or margin capacity.
- `phi_t`: funding rate for the interval ending at `t`.
- `D_t^E`: extrapolative demand.
- `D_t^A`: arbitrage demand.
- `D_t^L`: liquidity-supplier demand.

### Demand block

A simple linear approximation:

```
D_t^E = e * (b_t - b_{t-1}) + f * r_{t-1}
D_t^A = -a * K_t * tanh(g * phi_t)
D_t^L = -l * b_t
```

where `e, f, a, g, l > 0`. Extrapolative demand chases recent basis or return momentum. Arbitrage demand responds to funding but is capped by capital `K_t`. Liquidity suppliers lean against the basis.

The leverage constraint enters as:

```
|D_t^A| <= a * L_t
```

so a stronger funding sensitivity `g` cannot generate an arbitrarily large corrective position if `L_t` binds.

### Funding rule

A piecewise template:

```
phi_t = 0                                   if |b_t| <= b_flat
phi_t = k * (b_t - sign(b_t) * b_flat)     if b_flat < |b_t| < b_cap
phi_t = sign(b_t) * phi_max                 if |b_t| >= b_cap
```

The effective funding at settlement can use an interval average:

```
phi_t^eff = (1 / n) * sum_{j=1}^{n} phi_{t-j}
```

and may be adjusted for pre-settlement trading. The nominal rule is `phi_t`; the delivered rule is `phi_t^eff`.

### Price impact and basis dynamics

```
b_{t+1} = b_t + lambda * (D_t^E + D_t^A + D_t^L) - m * b_t + noise_t
```

where `lambda` is price impact and `m` is exogenous mean reversion (for example, spot arbitrage or demand for the underlying).

### Feedback strength

Define the local corrective response:

```
kappa_t = a * lambda * g * K_t * (1 - tanh^2(g * phi_t))
```

This is the derivative of the arbitrage correction with respect to the basis, scaled by impact. It is state-dependent:

- Inside the flat region, `phi_t = 0` and the derivative of the funding rule is zero. The spring is off.
- At the cap, the derivative is zero again. The spring is saturated.
- In the linear region, `kappa_t` rises with capital `K_t` and sensitivity `g`.
- After a loss, `K_t` falls, so `kappa_t` falls even if the template is unchanged.

### Stability band (reconstruction)

Linearising the basis dynamics around a steady state gives a scalar autoregression:

```
b_{t+1} = (1 - m + lambda * e - kappa_t) * b_t + noise_t
```

Stability requires:

```
|1 - m + lambda * e - kappa_t| < 1
```

which yields a band:

```
0 < kappa_t < 2 + lambda * e - m
```

The lower bound is the weak-feedback region; the upper bound is the strong-feedback region. The exact band in the paper is capital-dependent and derived from the full model; this scalar version only illustrates the mechanism.

### The three regimes

1. **Weak feedback (`kappa` small).** The autoregressive coefficient is close to 1. A shock to `b_t` decays slowly or not at all. Persistent gaps.
2. **Intermediate feedback.** The coefficient is inside the unit circle. The basis mean-reverts. Depending on the impact and extrapolation parameters, the path may cross spot (overshoot) or approach spot monotonically.
3. **Strong feedback (`kappa` large).** The coefficient is negative and greater than 1 in absolute value. The basis oscillates with expanding amplitude. Expanding reversals.

### Capital and liquidation dynamics

A simple capital law:

```
K_{t+1} = K_t + pnl_t - liquidation_loss_t - financing_cost_t
```

After a large adverse move, `K_t` falls. If `K_t` falls below a threshold `K_min`, the arbitrage demand cannot cover the extrapolative demand, and the basis can diverge even with a strong nominal funding rule. This is the "stabilisation infeasible" result in reduced form.

### Why the nominal template is not the effective rule

The effective feedback depends on:

- **Where the basis sits relative to the flat band.** If the basis never leaves the dead zone, funding is zero.
- **Whether the basis hits the cap.** If it does, the funding response saturates.
- **The interval average.** The average premium can be much smaller than the peak premium.
- **Pre-settlement trading.** Traders can push the premium at the settlement snapshot or trade against it before settlement.
- **Capital `K_t`.** The same funding rate produces a smaller corrective position when arbitrage capital is depleted.
- **Leverage `L_t`.** A binding constraint caps the corrective position even if funding is large.
- **Trader composition.** More extrapolative traders require stronger feedback for the same stability.
- **Liquidity.** Lower liquidity means a larger price impact for the same order flow.

This is the paper's core insight in mechanism form: **the template is a policy instrument, but the transmission channel is the market's capital and liquidity.**

## Empirical design (inferred, not verified)

Because the full text was not accessible, the following is an inference from the abstract and the reference list. It is a plausible design, not a claim about the authors' exact specification.

### Sample

- 200 Binance USDT-margined perpetual contracts.
- Panel data across contracts and time.
- Likely variables: funding rate, premium index, mark price, spot/index price, basis, open interest, volume, long/short liquidations, leverage proxies, and contract specifications (funding interval, cap, floor, flat band).

### Tests implied by the abstract

1. **Nominal versus effective funding.** Regress the realised funding payment or the next funding rate on the basis/premium and contract characteristics. Test whether the slope (effective feedback) is homogeneous across contracts with the same template.
2. **Stability band.** Estimate regime-dependent mean reversion of the basis. Test whether weak-feedback contracts show unit-root-like persistence, intermediate contracts mean-revert, and strong-feedback contracts oscillate.
3. **Basis reversals and liquidation.** Event studies around basis reversals; test whether directional liquidations (long versus short) predict or accompany reversals.
4. **Caps and persistent gaps.** Test whether contracts that hit the funding cap have larger and more persistent absolute basis.
5. **Flat region and deadband.** Test whether the basis spends more time inside the flat band and whether small gaps persist.
6. **Pre-settlement and interval averaging.** Compare funding at the settlement snapshot with the interval average; test for pre-settlement price pressure.
7. **Capital and leverage constraints.** Interact funding sensitivity with proxies for arbitrage capital, open interest, and leverage; test whether stronger funding fails to correct when capital is low or leverage binds.

### What the empirical results would need to show

- The same nominal funding rule maps to different effective feedback across contracts.
- The cross-section of basis persistence is explained by capital, liquidity, and rule regions.
- Liquidation events are not just consequences of basis moves; they feed back into weaker correction.
- Caps are associated with persistent gaps, consistent with the spring saturating.

## Equation ledger

### Verified definitions (from the abstract, not the paper's notation)

| Object | Definition | Status |
|---|---|---|
| Log basis | `b_t = log(F_t / S_t)` | Our notation; the paper uses "funding rate" and "futures basis" without exposing notation in the abstract |
| Nominal funding rule | `phi_t` | Generic; the paper's rule is piecewise, with flat and capped regions |
| Effective funding | `phi_t^eff` | Our shorthand for the funding response delivered at settlement |
| Feedback strength | `kappa` | Our shorthand for the sensitivity of corrective demand to the basis |
| Stability band | capital-dependent interval of `kappa` | Verified as a result; the exact formula is in the paper |
| Regimes | weak / intermediate / strong | Verified qualitatively |

### Reconstruction equations (ours)

| # | Object | Expression |
|---|---|---|
| R1 | Extrapolative demand | `D^E = e (b_t - b_{t-1}) + f r_{t-1}` |
| R2 | Arbitrage demand | `D^A = -a K_t tanh(g phi_t)` |
| R3 | Liquidity demand | `D^L = -l b_t` |
| R4 | Leverage constraint | `|D^A| <= a L_t` |
| R5 | Piecewise funding | flat if `|b| <= b_flat`; linear if `b_flat < |b| < b_cap`; capped if `|b| >= b_cap` |
| R6 | Interval average | `phi^eff = (1/n) sum_{j=1}^n phi_{t-j}` |
| R7 | Basis dynamics | `b_{t+1} = b_t + lambda(D^E + D^A + D^L) - m b_t + noise` |
| R8 | Feedback strength | `kappa = a lambda g K_t (1 - tanh^2(g phi_t))` |
| R9 | Linearised stability | `|1 - m + lambda e - kappa| < 1` |
| R10 | Capital law | `K_{t+1} = K_t + pnl_t - liquidation_loss_t - financing_cost_t` |

Do not present R1-R10 as the authors' equations. They are a scaffold for a simulator and for hackathon ideation.

## Paper-specific findings

### The big idea

**Funding is a control system, not a formula.** The exchange sets a controller; the market determines the plant gain. A controller that is stable for one asset can be unstable for another because the plant gain (capital, liquidity, extrapolation, leverage) differs. This is a control-theoretic reframing of a mechanism-design problem.

### Why a shared template can fail

1. **Same rule, different basis path.** The flat band and cap are nonlinear. Two assets can have the same template but different time-in-band and time-at-cap.
2. **Same rule, different capital.** A deep, well-capitalised market can arbitrage a gap; a thin market cannot. The same funding rate produces different corrective flow.
3. **Same rule, different trader mix.** Extrapolative demand differs across assets and regimes.
4. **Same rule, different settlement mechanics.** Interval averaging and pre-settlement trading change the effective signal.
5. **Same rule, different leverage.** A binding leverage constraint caps the correction.

### Why stronger funding can be worse

If the funding response is too strong, the basis overshoots. Extrapolative traders may interpret the reversal as a new trend, and the system can enter expanding oscillations. The paper's "expanding reversals" result is a warning against naive "just raise the funding coefficient" fixes.

### Why liquidation matters for stabilisation

Liquidation is not only a consequence of a gap; it is a shock to the arbitrage capacity that would close the gap. After a liquidation cascade, the remaining capital is smaller, leverage constraints bind harder, and the funding rule has less force. This is a **corrective-capacity feedback loop**: gap → liquidation → less capital → weaker correction → larger gap. It is the mechanism behind "stabilisation infeasible".

### The policy implication

Exchanges and perp DEXs should calibrate funding to the **effective feedback** they want, not copy a template. That means:

- Measure the basis path and the delivered funding, not just the formula.
- Estimate the stability band for each contract.
- Account for capital, open interest, and leverage.
- Design the flat band and cap jointly with the linear region.
- Stress-test the rule under liquidation cascades and pre-settlement pressure.
- Monitor regime shifts; a rule that is stable in calm markets can be unstable in stress.

## Limitations and caveats

### Access limitations

1. **Full text unavailable.** The 59-page PDF was blocked by SSRN. The verified findings are limited to the abstract, metadata, and references. The reconstruction is ours.
2. **No exact equations.** The paper's formal model, proofs, and regression specifications are not in this file. Any equation labelled "reconstruction" is a scaffold.
3. **No exact sample period.** The abstract says 200 Binance USDT-margined perpetuals but does not state the date range in the abstract. The reference list includes 2026 papers, so the sample likely runs through 2025 or early 2026, but this is not verified.
4. **No numerical coefficients.** The abstract reports qualitative results, not effect sizes. Any numbers in a pitch must be generated by our own replication, not attributed to the paper.

### Substantive limitations (even with full text)

1. **Single venue.** Binance USDT-margined perpetuals only. The results may not generalise to coin-margined contracts, other CEXs, or onchain perp DEXs.
2. **USDT-margined contracts.** The collateral and settlement asset is USDT. A USDT depeg or a change in the USDT/USD basis could affect the results.
3. **Endogeneity.** Funding, basis, liquidation, and open interest are jointly determined. Identifying causal feedback requires careful instruments or event studies.
4. **Liquidation data quality.** Exchange-reported liquidations can be incomplete, delayed, or manipulated, as documented in Paper 3 for Binance in May 2021.
5. **Rule heterogeneity.** Contracts may differ in funding interval, cap, floor, premium index, and mark price methodology. The "shared template" is not literally identical across all 200 contracts.
6. **Regime dependence.** The stability band is local. A rule that is stable in one volatility regime may be unstable in another.
7. **Model scope.** Extrapolative traders, capital-constrained arbitrageurs, and liquidity suppliers are a stylised set. Other agents (market makers, hedgers, treasuries) may matter.
8. **No DeFi frictions.** Gas, MEV, oracle latency, and smart-contract risk are absent.
9. **No welfare analysis.** The paper explains stabilisation, not whether a stable basis is socially optimal or who bears the cost.
10. **No regulatory analysis.** Funding caps and leverage limits may be imposed by regulators, not chosen by exchanges.
11. **General-equilibrium effects.** If all venues adopt the same stronger rule, the aggregate effect may differ from the partial-equilibrium effect.
12. **The "effective feedback" measure is not directly observable.** It must be estimated from funding, basis, and flow data; measurement error is likely.

## Hackathon prep for this paper

### What this paper supports building

1. **A Funding DNA oracle.** For each perp, publish a vector of effective-feedback characteristics: linear-region sensitivity, flat-band width, cap frequency, interval-average ratio, pre-settlement pressure, capital proxy, and stability-band estimate.
2. **A funding-rule compiler.** Given an asset's liquidity, trader mix, leverage, and desired convergence speed, output a piecewise funding rule with a target effective feedback and a stress-tested stability band. This is a natural extension of Perp Forge.
3. **A basis-stability dashboard.** Show nominal versus effective funding across 200+ contracts; classify each into weak, intermediate, or strong feedback; flag caps and persistent gaps.
4. **A liquidation-capacity monitor.** Track how liquidation cascades reduce corrective capacity; warn when stabilisation is at risk.
5. **A pre-settlement attack simulator.** Model how traders can push the premium around the settlement snapshot and how interval averaging changes the delivered funding.
6. **A funding-rule A/B lab.** Compare templates on historical data: flat band, cap, interval, and sensitivity. Show which rule would have stabilised a given contract.
7. **A perp DEX risk oracle.** Publish a stability score and a cap-risk score that perp DEXs, lenders, and vaults can consume.

### What this paper does not support building directly

- A general payoff compiler for arbitrary `phi(S)`. That is Paper 1.
- A liquidation-optimal hedge ratio. That is Paper 3.
- A no-arbitrage fair-value band for a fixed funding rule. That is Paper 2.
- A production trading strategy. The paper is an empirical asset-pricing study, and the full text is not available to us.
- A claim that a particular funding rule is optimal. The paper shows that templates are not uniformly effective; it does not give a universal design.

### Data and tech needed

- Funding rate history, premium index, mark price, index price, and spot price for many contracts.
- Contract specifications: funding interval, cap, floor, flat band, and settlement snapshot rules.
- Open interest, volume, and liquidation data (long/short).
- Leverage proxies: margin tiers, initial/maintenance margin, and open interest relative to capital.
- A panel regression / event-study engine.
- A regime-switching or threshold model for the stability band.
- A simulator for the piecewise rule and pre-settlement trading.
- An onchain oracle for the Funding DNA vector and stability score.
- A frontend for cross-contract comparison and what-if analysis.

### Demo script (3 minutes)

**Act 1: Same template, different outcomes.** Show two perpetuals with the same nominal funding rule. One mean-reverts after a shock; the other drifts for days. The dashboard shows nominal funding is similar, but effective feedback is not. The weak one spends most of its time in the flat band; the strong one hits the cap.

**Act 2: The feedback loop.** Trigger a liquidation cascade on the weak contract. Arbitrage capital falls, the leverage constraint binds, and the basis gap widens even though the funding formula is unchanged. Show the stability band shrinking.

**Act 3: The compiler.** Input the asset's liquidity, capital, and desired convergence half-life. Funding Forge outputs a new piecewise rule, simulates it against the same shock, and shows the basis converging without expanding reversals. Publish the Funding DNA score onchain. A perp DEX consumes it.

### Judge Q&A prep

**"Isn't funding just a formula? Why does the template matter?"** The formula is the controller; the market is the plant. The same controller can be stable or unstable depending on capital, liquidity, and trader behaviour. The paper shows the effective feedback is endogenous.

**"What is the novelty?"** Most work prices a given funding rule. This paper explains why the same rule produces different convergence across assets, and why stronger funding can destabilise. The primitive is an effective-feedback oracle and a rule compiler.

**"How do you measure effective feedback?"** Estimate the response of the basis and corrective flow to funding, allowing for the flat band, cap, interval averaging, and capital constraints. Use panel data and event studies.

**"Why Binance only?"** That is the paper's sample. Our product can extend to other venues, but we should not claim the paper's results generalise.

**"Can you show a number?"** Only if we replicate it ourselves. The abstract does not report effect sizes, and we do not have the full text. In the demo, show our own backtest and label it as our replication, not the paper's.

**"Is this just a dashboard?"** No. The oracle and compiler are the product. The dashboard is the demo.

### Claims to avoid

- "The paper proves that funding caps cause persistent gaps" — the abstract says caps are **associated with** persistent gaps.
- "Stronger funding always destabilises" — the paper says feedback that is **too strong** generates expanding reversals; intermediate feedback stabilises.
- "The paper covers all exchanges" — it covers 200 Binance USDT-margined perpetuals.
- "We have the full paper" — we do not; be transparent.
- "Our reconstruction is the authors' model" — it is not.
- "This is investment advice."

### Track and sponsor fit

- **DeFi/derivatives track:** funding-rule compiler, perp DEX risk oracle.
- **Oracle track:** Funding DNA oracle, stability-band oracle.
- **Data/analytics track:** cross-contract effective-funding dashboard.
- **AI track:** an agent that explains why a contract's basis is persistently dislocated and proposes a rule change.
- **Risk/security track:** pre-settlement manipulation simulator, cap-risk monitor.
- **Ethereum track:** onchain perp DEX design; funding as a public good.

## Hackathon-level idea for this paper

### Funding DNA: the effective-feedback oracle and funding-rule compiler

**One sentence.** Funding DNA measures what a perp's funding rule actually does, not what it says, and compiles a rule that delivers the feedback a given market can absorb.

**Why it is stronger than a dashboard.** A dashboard shows funding rates. Funding DNA publishes a **causal-ish feedback vector** and a **stability band** that other protocols can consume. It turns a copied template into a calibrated control system.

**Core modules.**

1. **Template parser.** Ingest contract specs: funding interval, premium index, cap, floor, flat band, settlement snapshot, and mark price method.
2. **Effective-funding engine.** Reconstruct the delivered funding path: interval averaging, pre-settlement trading, cap hits, and flat-band time.
3. **Feedback estimator.** Estimate the basis response to funding, allowing for capital and leverage constraints. Output `kappa_eff` and a confidence interval.
4. **Stability classifier.** Classify each contract as weak, intermediate, or strong feedback. Publish the estimated stability band.
5. **Liquidation-capacity monitor.** Track how long/short liquidations reduce corrective capacity. Flag "stabilisation at risk".
6. **Rule compiler.** Given target convergence half-life, liquidity, capital, and leverage, output a piecewise rule and simulate it.
7. **Oracle.** Publish `FundingDNA` and `StabilityScore` onchain, with inputs and methodology hashed.

**Onchain architecture.**

- `FundingDNAOracle.sol`: stores per-contract DNA vectors, stability scores, and timestamps.
- `RuleRegistry.sol`: stores funding-rule specs and compiler outputs.
- `StabilityScore.sol`: consumes DNA plus market data to publish a live score.
- Off-chain keeper: ingests CEX/DEX data, estimates feedback, signs reports.
- Consumer adapters: perp DEX, lending protocol, basis vault, risk dashboard.
- Optional `FundingRuleExecutor.sol`: a perp DEX can read the compiler output and update parameters through governance.

**Demo moment.** Two contracts, same template. A shock hits. Contract A's basis mean-reverts; contract B's drifts and hits the cap. Funding DNA shows the weak feedback and the depleted arbitrage capital. The compiler proposes a new flat band and cap. Re-run the shock: the basis converges without expanding reversals. Publish the new DNA onchain.

**Moat.** The cross-contract dataset, the effective-feedback estimator, the stability-band calibration, and the compiler. The oracle gets better with every contract and every regime.

**Risks.** Measurement error, endogeneity, exchange rule changes, data access, and the risk of overfitting the stability band. Keep the hackathon version to a research prototype and show uncertainty.

**Integration with the existing portfolio.** Funding DNA is the parameter-calibration layer for Perp Forge. Forge designs the payoff and funding; Funding DNA calibrates the feedback; LiquidMesh ensures the hedge survives; Basis Mesh provides the market anchor. Together they are a complete perp stack.

**Score.** Novelty 5, technical 5, demo 4, feasibility 4, judge 5, moat 5. Total 28.

## Connection to the other papers

- **Paper 1 (Angeris et al.):** gives the general funding formula for arbitrary payoffs. Funding DNA asks whether that formula delivers the intended feedback once frictions, caps, and capital constraints are added.
- **Paper 2 (He et al.):** gives the no-arbitrage anchor and basis deviation. Funding DNA explains why the same anchor can produce different convergence across contracts.
- **Paper 3 (Alexander et al.):** gives the liquidation-aware hedge. Funding DNA adds the funding-feedback channel that can weaken or amplify the hedge's survival.
- **Synthesis:** Paper 1 designs the contract; Paper 2 prices the basis; Paper 3 keeps the hedge alive; Paper 4 calibrates the control system that connects them.

**Full ideation:** See [06-hackathon-idea-lab.md](06-hackathon-idea-lab.md), especially the new Wave 2 sections on Funding DNA, LiquidMesh, and the unified Perp Forge risk stack.
