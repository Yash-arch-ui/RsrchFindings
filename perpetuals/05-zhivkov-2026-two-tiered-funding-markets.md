# The Two-Tiered Structure of Cryptocurrency Funding Rate Markets

## Metadata

- **Author:** Petar Zhivkov (Institute of Information and Communication Technologies, Bulgarian Academy of Sciences; "Angel Kanchev" University of Ruse; Centre of Excellence in Informatics and ICT)
- **Journal:** Mathematics (MDPI), 2026, vol. 14, issue 2, article 346
- **DOI:** 10.3390/math14020346
- **History:** Received 1 December 2025; revised 11 January 2026; accepted 12 January 2026; published 20 January 2026
- **License:** CC BY 4.0
- **Length:** 20 pages, 9 tables, 1 figure, 27 references
- **Type:** Empirical market microstructure / statistical arbitrage. High-frequency panel data, time-series econometrics, correlation analysis, Granger causality, delta-neutral portfolio simulation.
- **Data:** 35.7 million one-minute observations; 26 exchanges (11 CEX, 15 DEX); 749 symbols; 8 consecutive days, 8-15 November 2025; 3,883,727 symbol-timestamp pairs analyzed after filters. Funding rates normalized to 8-hour equivalents.
- **Read status:** Full paper read end to end, including all tables, the Granger causality section, portfolio simulation, discussion, conclusions, and references. Numerical findings below are taken directly from the paper. Internal inconsistencies are flagged explicitly in the limitations section.

## One-paragraph summary

The paper builds a one-minute panel of funding rates across 26 centralized and decentralized crypto derivatives venues and asks whether funding-rate markets are integrated. It finds a two-tiered structure. Centralized exchanges (CEX) are more integrated with each other, lead funding-rate innovations, and transmit information to decentralized exchanges (DEX), with no significant reverse flow in the paper's headline tests. Roughly 17% of one-minute observations contain a cross-venue funding spread of at least 20 basis points, and 84% of these significant spreads are CEX-DEX pairs. Yet the apparent arbitrage is mostly not profitable. Under a conservative rule that exits when the spread turns negative, only 8 of the top 20 opportunities make money after fees, slippage, and gas; the average net P&L is about USD 22 on USD 10,000 per side, and the average Sharpe ratio is -7.40. The paper reconciles the coexistence of large visible spreads and market efficiency through limits to arbitrage: transaction costs, capped duration before spread reversals, and structural CEX-DEX frictions prevent arbitrage from eliminating the mispricing. The practical message is that funding-rate arbitrage is an execution and risk-management problem, not a spread-scanning problem.

## Section-by-section findings

### Abstract (p. 1)

- Perpetual futures are about 93% of crypto futures volume, with over USD 90 trillion cumulative volume.
- The paper studies funding-rate dynamics across fragmented markets.
- Dataset: 35.7 million one-minute observations, 26 exchanges (11 CEX, 15 DEX), 749 symbols, 8 days.
- Methods: time-series econometrics, correlation analysis, Granger causality.
- Main results:
  - Two-tiered market structure.
  - CEX integration is 61% higher than DEX integration.
  - All significant information flow runs CEX-to-DEX with zero reverse causality (headline claim; contradicted by Table 6, see below).
  - 17% of observations have economically significant arbitrage spreads (>=20 bps).
  - Only 40% of top opportunities generate positive returns after transaction costs and spread reversals.
  - Delta-neutral simulations: successful arbitrage requires both high spreads and sufficient duration before reversals; forced exits occur in 95% of opportunities (headline claim; disagrees with the 12/20 forced-exit count in Section 4.7).
- Interpretation: persistent two-tiered structure; CEX dominates price discovery; costs and reversal risk prevent arbitrage from eliminating large mispricings. This resolves the apparent paradox of fragmentation plus efficiency.

### Section 1: Introduction (pp. 1-2)

- Perpetual futures dominate crypto derivatives: about 93% of futures volume, over USD 90 trillion cumulative.
- Perps have no expiry, so funding payments anchor the perp price to spot. How well this works varies across markets.
- Market structure is fragmented:
  - CEX: Binance, OKX, etc.; order books, centralized custody.
  - DEX: Hyperliquid, Drift, etc.; on-chain settlement, permissionless access.
- Three research questions:
  1. What are the time-series properties of funding-rate spreads across exchanges? Mean reversion, volatility clustering, persistence?
  2. What are the risk-adjusted returns of delta-neutral arbitrage portfolios exploiting funding differentials, and how do convergence patterns affect optimal holding periods?
  3. Which exchanges lead funding-rate price discovery, and how does integration differ between CEX and DEX?
- Contributions:
  1. A statistical framework for funding dynamics with transaction-cost accounting.
  2. A normalization procedure for comparing funding rates across heterogeneous platforms.
  3. Risk-managed multi-venue arbitrage portfolio construction.
  4. Practical guidance on exchange selection, position sizing, and risk management.
- The author is explicitly cautious about novelty, noting it depends on how the literature evolves.

### Section 2: Literature review (pp. 2-4)

- Theoretical lineage: Shiller (1993) perpetual claims; Ackerer, Hugonnier, and Jermann (2024) no-arbitrage prices for linear, inverse, and quanto perps; He, Manela, Ross, and von Wachter (2024) no-arbitrage prices and bounds with trading costs.
- The paper argues the theory abstracts from institutional heterogeneity across venues.
- Cross-sectional crypto asset pricing: Liu, Tsyvinski, and Wu (2022) common factors.
- DeFi and DEX arbitrage: AMM design affects arbitrage profitability; searchers extracted about USD 233.8 million from over 7.2 million Ethereum arbitrages between August 2023 and March 2025 (Neuder et al.).
- Volatility: GARCH captures Bitcoin volatility; asymmetric volatility; long-volatility strategies.
- Portfolio optimization: mean-variance crypto portfolios; rebalancing and transaction costs; unstable Sharpe ratios.
- Market efficiency and price discovery: time-varying Bitcoin efficiency; persistent cross-exchange price deviations (Makarov and Schoar); futures contribute to price discovery (Alexander et al.).
- Research gap: prior funding-rate studies focus on single exchanges or aggregate data; no systematic CEX vs DEX comparison at high frequency.

### Section 3: Methodology (pp. 4-9)

#### 3.1 Data

- Source: exchange APIs plus a software tool that aggregates and stores real-time perpetual futures data.
- Period: 8-15 November 2025, eight consecutive days.
- Observations: 35.7 million one-minute observations, 749 symbols, minimum two exchanges per symbol.
- Fields: funding rate in bps, funding interval (1h, 4h, 8h), open interest rank as liquidity proxy, millisecond timestamp.
- Venues:
  - 11 CEX: Binance, Bybit, OKX, KuCoin, MEXC, Gate.io, Bitget, BingX, Phemex, Crypto.com, HTX.
  - 15 DEX: Hyperliquid, Drift, Paradex, Kuma, Vest, Bluefin, Pacifica, Lighter, Extended, EdgeX, Hibachi, Ethereal, Variational, Aster, WOO.
- Missing data: <0.01%; forward-fill gaps <=5 minutes; longer gaps treated as structural missing.
- Funding rates reported at discrete intervals are carried forward until the next update. The paper acknowledges this introduces autocorrelation by construction for infrequent-update venues.

#### 3.1.2 Normalization

```
r_8h = r_h * (8 / h)
```

where `r_h` is the funding rate over an `h`-hour interval. Multipliers are 8 for 1h, 2 for 4h, and 1 for 8h. The choice of 8h is industry convention and is acknowledged as somewhat arbitrary.

Annualized percentage yield:

```
APY = r_8h * 3 * 365 / 100
```

where `r_8h` is in basis points. A 20 bps spread per 8h is about 219% APY.

#### 3.1.3 Sample filters

- Multi-exchange filter: symbols traded on at least two exchanges; sample falls from 1,417 to 731 symbols with arbitrage potential.
- Major crypto subsample: top 10 by open interest for detailed time-series work.
- Portfolio analysis: top 20 opportunities by spread magnitude, not OI.
- Outlier filter: funding rates beyond +/-5,000 bps (about +/-54,750% APY) are removed; 47 observations (0.0001%). The sample maximum is 2,401 bps, so the threshold is well beyond the 99.9th percentile.

#### 3.2 Time-series methods

- ADF tests for unit roots; negative significant coefficient implies mean reversion.
- KPSS tests as a robustness check.
- Half-life:

```
Half-life = ln(2) / lambda
```

where `lambda` is estimated from an exponential decay regression.
- ACF and PACF up to 48 lags; Ljung-Box Q tests; AR(p) models.
- ARCH-LM tests; GARCH(1,1):

```
r_{s,t} = mu_s + eps_{s,t},   eps_{s,t} ~ N(0, sigma^2_{s,t})
sigma^2_{s,t} = omega + alpha eps^2_{s,t-1} + beta sigma^2_{s,t-1}
```

with `alpha + beta < 1` for stationarity.
- Granger causality:
  - Bivariate VARs for five exchange pairs: Binance-Hyperliquid, Binance-Bybit, Drift-Hyperliquid, Binance-OKX, KuCoin-MEXC.
  - AIC lag selection, maximum 10 lags; most pairs select 1-3 lags.
  - F-tests on VAR coefficients; orthogonalized impulse responses.
  - Robustness at 5-minute and 15-minute sampling.

#### 3.3 Cross-sectional analysis

- Mean, median, standard deviation, IQR by exchange type.
- Time-averaged funding per exchange-symbol:

```
rbar_{s,j} = (1/T) sum_{t=1}^T r_{s,t,8h}
```

with `T = 10,080` minutes (7 days). Note the paper describes an 8-day sample but uses a 7-day average window.
- Two-sample t-tests, Levene tests, Mann-Whitney U tests.
- Pairwise Pearson correlations across exchanges for the same symbol, grouped into CEX-CEX, DEX-DEX, CEX-DEX.

#### 3.4 Arbitrage identification

```
Spread_{s,t} = max_{e in E_s} r_{e,s,t} - min_{e in E_s} r_{e,s,t}
```

- Economically significant if `Spread >= 20 bps` (about 219% APY).
- Strategy: long notional `N` on the lowest-funding exchange; short `N` on the highest-funding exchange.
- Profit per period:

```
Profit = N * Spread / 10,000
```

- The paper acknowledges execution, liquidity, margin, exchange, and timing risks.
- Duration: cumulative minutes with spread >=20 bps, not necessarily contiguous; percentage of total observation time.
- Portfolio holding duration is different: continuous time from entry until the spread turns negative, with no re-entry after a forced exit.

#### 3.5 Portfolio construction and costs

- Equal position size: USD 10,000 per arbitrage opportunity, long one venue and short another.
- Taker fees: 0.01-0.05%, conservatively 0.05%.
- Slippage: 0.1% for liquid pairs (OI rank <50), 0.5% for illiquid pairs (OI rank >=50).
- Gas: negligible for the DEXs in the sample (Solana, Arbitrum, Optimism), <USD 0.01 per trade.
- Round-trip cost:
  - Liquid: USD 60 (2 sides x USD 10,000 x 0.15% x 2).
  - Illiquid: USD 220 (2 sides x USD 10,000 x 0.55% x 2).
- Funding accrual: 8h rate divided by 480 minutes, summed over the holding period.
- Convergence metrics: time to fall below 20 bps, half-life, time to 10 bps.
- Forced exit: close immediately when the spread turns negative (<0 bps). The paper calls this conservative and notes that alternative thresholds (-5, -10 bps) could improve profitability but are out of scope.
- Performance: Sharpe ratio and maximum drawdown.

### Section 4.1: Data overview (p. 9)

- 35,674,300 total observations.
- 749 unique symbols; 26 exchanges (11 CEX, 15 DEX).
- 8 days, 1-minute intervals.
- Missing data <0.01%.
- 3,883,727 symbol-timestamp pairs analyzed.
- Significant spreads: 665,251 observations (17.1%).
- Mean spread: 14.3 bps; median 7.1 bps; max 2,401 bps.
- Top 10 symbols by OI: BTC, ETH, SOL, BNB, XRP, ZEC, DOGE, HYPE, ASTER, SUI. Each has 24-26 exchange coverage.
- The paper says "roughly 17% of observations exceed this threshold" and "mean spread 14.3 bps" in the same paragraph; the 14.3 bps is the unconditional mean, while 51.6 bps is the conditional mean for significant spreads (Section 4.3).

### Section 4.2: CEX vs DEX descriptive statistics (pp. 9-10)

| Metric (bps) | CEX | DEX | Difference |
|---|---:|---:|---:|
| Observations | 27.9M (78%) | 7.8M (22%) | - |
| Mean | -1.91 | -1.74 | 0.18 |
| Median | 1.00 | 0.80 | -0.20 |
| Std Dev | 22.82 | 17.24 | -5.58 |
| Min | -2,400 | -1,847 | 553 |
| Max | 1,600 | 224 | -1,376 |
| IQR | 1.20 | 1.60 | 0.40 |

- All differences are statistically significant at p < 0.001 because the sample is huge, but the central-tendency differences are economically negligible.
- CEX has higher dispersion.
- Most rates cluster near zero.
- Interpretation: frictions show up in cross-platform spreads, not in average funding levels.

### Section 4.3: Arbitrage opportunities and fragmentation (pp. 10-11)

- 665,251 observations (17%) exceed 20 bps.
- Conditional mean spread: 51.6 bps; max 2,401 bps.
- Pair composition:
  - 84% CEX-DEX; average 52.4 bps; present 91% of observation time.
  - 16% CEX-CEX; average 47.2 bps; present 92% of time.
  - Essentially no DEX-DEX; average 30.3 bps; present 62% of time.
- Top 10 opportunities:

| Rank | Symbol | Avg spread (bps) | Max spread (bps) | Avg APY (%) | Duration / % time |
|---:|---|---:|---:|---:|---:|
| 1 | INTC | 472.4 | 600.5 | 5,173 | 1h 12m / 0.7% |
| 2 | ELX | 307.3 | 1,508.2 | 3,365 | 7h 7m / 4.2% |
| 3 | CPOOL | 283.0 | 2,401.0 | 3,099 | 1h 26m / 0.8% |
| 4 | AIA | 260.2 | 1,623.8 | 2,850 | 3d 13h 20m / 50.2% |
| 5 | LSK | 259.1 | 2,400.1 | 2,837 | 2d 16h 34m / 38.0% |
| 6 | ASML | 192.1 | 618.5 | 2,103 | 4h 37m / 2.7% |
| 7 | MERL | 173.7 | 1,440.2 | 1,903 | 1d 12h 14m / 21.3% |
| 8 | 0G | 166.5 | 1,597.4 | 1,823 | 3d 15h 48m / 51.6% |
| 9 | LA | 162.2 | 1,601.0 | 1,776 | 1d 22h 40m / 27.4% |
| 10 | PARTI | 159.0 | 1,200.0 | 1,741 | 1d 17h 54m / 24.6% |

- The top table ranks by average spread, but the portfolio section ranks by spread magnitude and then simulates. The two rankings are not identical.
- High average spread alone is not enough: INTC has the highest average spread but only 0.7% duration; AIA and 0G persist for about half the sample.

### Section 4.4: Time-series properties (pp. 10-11)

**Stationarity.**

| Symbol | OI rank | ADF stat | ADF p | KPSS stat | KPSS p | Conclusion |
|---|---:|---:|---:|---:|---:|---|
| BTC | 1 | -6.01 | 0.000 | 0.81 | 0.01 | Mixed |
| ETH | 2 | -7.20 | 0.000 | 0.30 | 0.10 | Stationary |
| SOL | 3 | -5.04 | 0.000 | 5.86 | 0.01 | Mixed |
| BNB | 4 | -1.86 | 0.352 | 4.88 | 0.01 | Non-stationary |
| XRP | 5 | -4.16 | 0.001 | 5.54 | 0.01 | Mixed |
| ZEC | 6 | -2.44 | 0.132 | 0.64 | 0.02 | Non-stationary |
| DOGE | 7 | -4.16 | 0.001 | 1.12 | 0.01 | Mixed |
| HYPE | 8 | -4.52 | 0.000 | 5.44 | 0.01 | Mixed |
| ASTER | 9 | -2.57 | 0.099 | 4.36 | 0.01 | Non-stationary |
| SUI | 10 | -6.50 | 0.000 | 6.49 | 0.01 | Mixed |

- Only ETH is clearly stationary by both tests.
- BNB, ZEC, and ASTER appear non-stationary.
- Six symbols are mixed: ADF rejects a unit root but KPSS rejects stationarity. This is the classic near-unit-root / slow-reversion pattern.

**Autocorrelation and persistence.**

- First-order autocorrelation: 0.966-0.998.
- At lag 8, autocorrelation remains above 0.80 for all symbols.
- Ljung-Box Q statistics: 39,970-51,736, p < 0.001.
- Current spreads strongly predict next-period spreads.

**Mean reversion.**

- ETH half-life about 20 minutes; AR(1) coefficient 0.966; R^2 = 0.93.
- Other symbols have AR(1) above 0.96 and half-lives of hours or days.
- Interpretation: ETH attracts sophisticated arbitrage; less liquid symbols correct slowly.

**Volatility.**

| Symbol | omega | alpha | beta | alpha+beta |
|---|---:|---:|---:|---:|
| BTC | 434.81 | 0.777 | 0.223 | 1.000 |
| ETH | 481.59 | 0.926 | 0.074 | 1.000 |
| SOL | 747.82 | 0.202 | 0.798 | 1.000 |

- All three are integrated GARCH (IGARCH): persistence approximately 1.
- BTC is shock-dominated; SOL is past-volatility-dominated; ETH is in between.
- IGARCH implies shocks may be permanent; the paper cautions that 8 days may not distinguish true IGARCH from near-integrated dynamics.

**Implications.**

- Extreme persistence, limited stationarity, and IGARCH imply slow-moving spreads and permanent volatility shocks.
- ETH's 20-minute half-life supports short-horizon arbitrage.
- Other symbols require long holding periods or tolerance of persistent adverse spreads.
- IGARCH requires continuous risk monitoring; volatility may not decline to bail out a position.

### Section 4.5: Information flow and price discovery (pp. 11-13)

- 92 bidirectional tests across 5 exchange pairs and 10 symbols.
- 57 significant (62%) at 1-minute (Table 7 reports 56/92, 60.9%; see limitations).
- CEX-DEX: 14 significant relationships, all CEX-to-DEX, zero DEX-to-CEX by the paper's count.
- Binance causes Hyperliquid for 7 of 9 tested symbols; F-statistics 4.23-127.83, p < 0.05.
- CEX-CEX: 35 significant bidirectional relationships. Binance and Bybit cause each other for 9 of 10 symbols.
- DEX-DEX: 8 significant relationships (Table 7 says 7 at 1-minute), concentrated between Drift and Hyperliquid. Hyperliquid causes Drift for 5 symbols.
- Interpretation: CEX leads funding-rate discovery; DEX are price takers.

**Table 6 selected results.** The table includes:

| Symbol | Direction | F-stat | p | Result |
|---|---|---:|---:|---|
| ETH | Binance -> Hyperliquid | 4.23 | 0.040 | Significant |
| ETH | Hyperliquid -> Binance | 58.27 | 0.000 | Significant |
| SOL | Binance -> Hyperliquid | 5.33 | 0.021 | Significant |
| ZEC | Binance -> Hyperliquid | 127.83 | 0.000 | Significant |
| BNB | Binance -> Hyperliquid | 4.54 | 0.004 | Significant |
| BNB | Hyperliquid -> Binance | 337.60 | 0.000 | Significant |
| BTC | Binance -> Bybit | 5.24 | 0.005 | Significant |
| BTC | Bybit -> Binance | 86.54 | 0.000 | Significant |

**Critical inconsistency.** The table reports significant Hyperliquid-to-Binance causality for ETH and BNB, yet the text and abstract say "zero DEX-to-CEX causality." This is not a rounding issue; it is a direct contradiction. The likely explanation is that the counts refer to a subset of pairs/symbols or a different specification, but the paper does not reconcile it. Treat the "zero reverse causality" claim as unsupported until clarified.

**Robustness across frequencies.**

| Direction | 1-min | 5-min | 15-min |
|---|---:|---:|---:|
| Total significant | 56/92 (60.9%) | 44/92 (47.8%) | 27/92 (29.3%) |
| CEX -> DEX | 14/14 | 7/7 | 6/6 |
| DEX -> CEX | 0/14 | 0/7 | 0/6 |
| CEX <-> CEX | 35 | 23 | 12 |
| DEX <-> DEX | 7 | 4 | 3 |

- The directional asymmetry is robust in the paper's counts: CEX-to-DEX is always significant when tested; DEX-to-CEX is never significant when tested.
- But the number of tested pairs falls with coarser sampling, and the table still conflicts with Table 6's DEX-to-CEX results.

### Section 4.6: Market integration and correlations (pp. 12-14)

- 2,194 unique pairwise correlations across the top 10 symbols and 26 exchanges.
- CEX-CEX: 576 pairs, mean 0.246, median 0.203, SD 0.284.
- CEX-DEX: 1,141 pairs, mean 0.175, median 0.131, SD 0.247.
- DEX-DEX: 477 pairs, mean 0.153, median 0.124, SD 0.234.
- CEX integration is 61% higher:

```
(0.246 - 0.153) / 0.153 = 0.608
```

- Statistical tests:
  - CEX-CEX vs DEX-DEX: t = 5.77, p < 0.001.
  - CEX-CEX vs CEX-DEX: t = 5.39, p < 0.001.
  - DEX-DEX vs CEX-DEX: t = -1.67, p = 0.096 (not significant).
- Bitcoin heatmap: CEX platforms (Binance, Bybit, OKX) correlate above 0.4 with each other; DEX platforms (Hyperliquid, Drift, Paradex) correlate 0.1-0.3 with CEX; some DEX-DEX pairs are near zero or negative.
- Interpretation: a highly connected CEX tier and a fragmented DEX tier; partial CEX-DEX integration creates persistent cross-tier opportunities.

### Section 4.7: Delta-neutral portfolio performance (pp. 14-15)

- Top 20 opportunities by spread magnitude; USD 10,000 per side.
- Reversal patterns: 19 of 20 (95%) exhibit reversal patterns where exchanges swap funding-rate positions.
- Forced exits: 12 of 20 when the spread turns negative.
- Profitable: 8 of 20 (AIA, ASML, MERL, 0G, PARTI, KAVA, GODS, LAYER).
- Average net P&L: USD 22.
- Best: AIA, USD 1,432.78 net over 2 days.
- Costs: about USD 220 per round trip for illiquid markets; USD 60 for liquid.
- Funding accrual at 20 bps: USD 0.042 per minute on USD 10,000 per side; requires 5,263 minutes (3.7 days) to cover costs.
- Risk-adjusted:
  - Average Sharpe: -7.40.
  - Best Sharpe: -2.73 (AIA).
  - Average Sortino: -1.93.
  - Average max drawdown: -USD 117 (-1.17% of position size).

**Table 9 (selected).**

| Symbol | Duration | Avg spread (bps) | Gross P&L | Net P&L | Pattern |
|---|---|---:|---:|---:|---|
| AIA | 2d 0h 27m | 732.53 | 1,652.78 | 1,432.78 | Reversal |
| PARTI | 4d 0h 21m | 88.01 | 724.62 | 504.62 | Reversal |
| KAVA | 2d 5h 24m | 170.77 | 626.88 | 406.88 | Reversal |
| MERL | 1d 3h 45m | 373.76 | 460.20 | 240.20 | Reversal |
| ASML | 6d 6h 0m | 18.81 | 311.00 | 91.00 | Chaotic |
| 0G | 0d 22h 28m | 487.27 | 278.15 | 58.15 | Reversal |
| GODS | 2d 6h 35m | 96.13 | 268.97 | 48.97 | Reversal |
| LAYER | 6d 6h 0m | 13.84 | 257.91 | 37.91 | Reversal |
| INTC | 6d 6h 0m | 6.57 | 180.17 | -39.83 | Reversal |
| LSK | 0d 1h 14m | 1,133.94 | 174.82 | -45.18 | Reversal |
| PYR | 0d 0h 2m | 164.80 | 1.03 | -218.97 | Reversal |

- The paper explains that portfolio durations can be much shorter than cumulative time above threshold (Table 3) because of the no-re-entry rule. PYR had spreads above 20 bps for 4d 20h cumulatively, but the portfolio exited after 2 minutes when the initial spread reversed.

### Section 5: Discussion (pp. 15-18)

- Two-tiered structure:
  1. **Liquidity depth:** CEX account for about 75-80% of perpetual volume. The DEX-to-CEX ratio rose from 3.7% to 11.7% over 2025 but remains small.
  2. **Execution speed:** CEX matching engines are microsecond-level; DEX settlement is sub-second to seconds, still orders of magnitude slower.
  3. **Arbitrage infrastructure:** sophisticated arbitrageurs operate primarily on CEX because of liquidity, lower friction, and speed. Information propagates CEX-to-DEX; reverse flow is limited.
- The integration gap explains why 84% of significant spreads are CEX-DEX.
- Practical implication: successful arbitrage must navigate cross-platform frictions, not just scan spreads.
- Reconciling perception and reality:
  - Visible spreads can be equilibrium mispricings under binding frictions.
  - Trading costs create pricing bounds; 20-50 bps spreads may not cover costs before reversal.
  - Only opportunities with both high spread and sufficient duration are profitable.
- AIA vs INTC:
  - AIA: 732.5 bps average, 2 days, USD 0.57/minute funding accrual, USD 1,653 gross, USD 1,433 net, about 12,953% APY.
  - INTC: 6.57 bps average, 6d 6h, USD 180 gross, -USD 40 net. Long duration alone is not enough.
- Dynamic risk management: 95% reversal prevalence means passive delta-neutral positions become loss-makers. Need real-time spread tracking, immediate exit on negative spread, and realistic profit expectations.
- The paper positions the results as limits-to-arbitrage evidence: mispricings persist because costs and reversal risk filter most opportunities.

### Section 6: Conclusions (pp. 18-19)

- First systematic cross-platform examination of crypto funding rates across CEX and DEX.
- Two-tiered structure: CEX leads price discovery; DEX are price takers; CEX-CEX correlations 61% higher.
- Most significant spreads are at the CEX-DEX boundary.
- 17% of observations have high spreads, but most are unprofitable under the conservative exit protocol.
- The paradox is resolved by equilibrium mispricing under binding frictions.
- Practical requirements: high spread plus sufficient duration; focus on CEX-DEX; immediate exit on negative spread; realistic expectations.
- Future work:
  - Longer sample across bull, bear, and high-volatility regimes.
  - Predictive models for spread reversals using machine learning and order-flow imbalance.
  - Alternative exit thresholds (-5, -10 bps).
  - Better understanding of the evolving CEX-DEX boundary.

### References (pp. 19-20)

Key references: Shiller (1993); Ackerer, Hugonnier, Jermann (2024); He, Manela, Ross, von Wachter (2024); Liu, Tsyvinski, Wu (2022); He, Yang, Zhou (2024); Neuder et al. (2024); Katsiampa (2017); Baur and Dimpfl (2018); Conrad, Custovic, Ghysels (2018); Brauneis and Mestel (2019); Platanakis and Urquhart (2020); Grobys, Ahmed, Sapkota (2021); Nadarajah and Chu (2017); Makarov and Schoar (2020); Alexander, Choi, Park, Sohn (2020); Dickey and Fuller (1979); Kwiatkowski et al. (1992); Ljung and Box (1978); Engle (1982); Bollerslev (1986); Granger (1969); Akaike (1974); Cameron, Gelbach, Miller (2011); Jorion (2007); Sharpe (1994); CoinGecko (2025).

## Equation ledger

| # | Object | Expression | Role |
|---|---|---|---|
| 1 | Funding normalization | `r_8h = r_h * (8/h)` | Makes 1h/4h/8h rates comparable |
| 2 | Annualized yield | `APY = r_8h * 3 * 365 / 100` | Economic interpretation |
| 3 | Half-life | `Half-life = ln(2)/lambda` | Mean-reversion speed |
| 4 | GARCH mean | `r_{s,t} = mu_s + eps_{s,t}`, `eps ~ N(0, sigma^2_{s,t})` | Volatility model |
| 5 | GARCH variance | `sigma^2_{s,t} = omega + alpha eps^2_{s,t-1} + beta sigma^2_{s,t-1}` | Conditional volatility |
| 6 | Time-averaged funding | `rbar_{s,j} = (1/T) sum_t r_{s,t,8h}`, `T = 10,080` min | Cross-sectional comparison |
| 7 | Cross-venue spread | `Spread_{s,t} = max_e r_{e,s,t} - min_e r_{e,s,t}` | Opportunity definition |
| 8 | Significance threshold | `Spread_{s,t} >= 20 bps` | Economic filter |
| 9 | Arbitrage P&L | `Profit = N * Spread / 10,000` | Gross funding profit |
| 10 | Round-trip cost | Liquid USD 60; illiquid USD 220 | Net profitability |
| 11 | Funding accrual | `r_8h / 480` per minute | Continuous approximation |
| 12 | CEX integration gap | `(0.246 - 0.153)/0.153 = 0.608` | 61% higher CEX correlation |

## Paper-specific findings

### The central empirical fact

Funding-rate markets are **not one market**. They have a two-tier structure:

- A relatively integrated CEX tier with fast information transmission.
- A fragmented DEX tier that follows CEX.
- A partially integrated boundary where most large spreads occur.

This is not just a correlation statement. The paper shows:
- CEX-CEX funding correlations are 61% higher than DEX-DEX.
- CEX funding innovations lead DEX innovations in the paper's headline tests.
- 84% of significant spreads are CEX-DEX pairs.
- DEX-DEX arbitrage is rare.

### The central economic fact

**Visible spread is not executable profit.** The paper's most useful contribution is the gap between gross opportunity and net P&L:

- 17% of observations have spreads >=20 bps.
- 665,251 observations qualify.
- Yet only 8 of the top 20 portfolios are profitable.
- Average net P&L is USD 22 on USD 10,000 per side.
- Average Sharpe is -7.40.
- 95% of opportunities show reversal patterns.
- Costs are USD 60-220 per round trip; a 20 bps spread needs about 3.7 days to cover costs.

The binding constraints are:
1. **Transaction costs** (fees, slippage, gas).
2. **Duration** before the spread reverses.
3. **Reversal risk** (the sign flips; the trade becomes a loser).
4. **Execution and capital friction** across venues.

### The persistence result

- First-order autocorrelation 0.966-0.998.
- Lag-8 autocorrelation above 0.80.
- Only ETH is clearly stationary.
- ETH half-life about 20 minutes; other majors are much slower.
- GARCH persistence is approximately 1 for BTC, ETH, and SOL.

This says funding spreads are highly predictable in the short run but slow to converge. Predictability is not the same as profitability: a spread can be predictable and still too small or too short-lived to cover costs.

### The information-flow result

- CEX leads DEX in the paper's headline tests.
- Binance is the dominant CEX in the tested pairs.
- Hyperliquid is the dominant DEX; it leads Drift.
- CEX-CEX relationships are bidirectional and pervasive.
- DEX-DEX relationships are limited.

But the paper's own Table 6 shows significant Hyperliquid-to-Binance causality for ETH and BNB, contradicting the "zero reverse causality" claim. The safe conclusion is: **CEX leadership is the dominant pattern in the paper's counts, but the evidence is not clean enough to claim literally zero reverse causality.**

### The portfolio result

- The best trade (AIA) made USD 1,432.78 net over 2 days because it combined a very high average spread (732.53 bps) with sufficient duration.
- The worst trade (PYR) lost USD 218.97 because the spread reversed after 2 minutes.
- Long duration alone fails (INTC: 6 days, 6.57 bps, -USD 39.83).
- High spread alone fails if duration is too short (LSK: 1,133.94 bps, 1h 14m, -USD 45.18).
- The winning combination is **high spread x sufficient duration x fast exit**.

### What the paper implies for market design

- CEX-DEX integration is the frontier. Intra-CEX arbitrage is competitive; intra-DEX is fragmented but small; cross-tier is where the opportunity is.
- DEX funding rates are followers. A DEX that wants better price discovery needs faster oracle updates, deeper liquidity, or tighter cross-chain execution.
- The two-tier structure is self-reinforcing: liquidity and speed attract arbitrageurs to CEX, which keeps DEX in a follower role.
- Funding-rate arbitrage is a **latency and cost** business, not a **signal** business. The signal is easy; the execution is hard.

## Critical review and limitations

### Internal inconsistencies to flag

1. **"Zero DEX-to-CEX causality" vs Table 6.** Table 6 reports significant Hyperliquid-to-Binance causality for ETH (F=58.27, p=0.000) and BNB (F=337.60, p=0.000). The abstract, text, and Table 7 say zero. This is a direct contradiction and undermines the strongest form of the headline claim.
2. **"Forced exits in 95% of opportunities" vs 12/20.** The abstract says forced exits occur in 95% of opportunities. Section 4.7 says 19/20 (95%) exhibit reversal patterns, but forced exits occurred for 12/20 (60%). Reversal pattern and forced exit are different events; the abstract conflates them.
3. **57 vs 56 significant tests.** Section 4.5 says 57 of 92 (62%); Table 7 says 56/92 (60.9%).
4. **8 vs 7 DEX-DEX significant relationships.** Section 4.5 text says 8; Table 7 says 7 at 1-minute.
5. **8-day sample vs 7-day average.** Section 3.1 says 8 days (8-15 November); Equation 6 uses `T = 10,080` minutes, which is 7 days. The time-averaged funding calculation may use a 7-day window.
6. **Top-10 ranking vs portfolio ranking.** Table 3 ranks by average spread; Section 4.7 says the portfolio analysis ranks by spread magnitude. The sets are not identical, so the top-10 table and the portfolio table should not be read as the same ranking.
7. **"Essentially no DEX-DEX opportunities" vs 30.3 bps average and 62% duration.** The paper means few relative to CEX-DEX (16% vs 84%), not literally none.

### Methodological limitations

1. **Short sample.** Eight days in November 2025, one market regime. The paper acknowledges this and calls for longer samples across bull, bear, and high-volatility regimes.
2. **Forward-fill induces autocorrelation.** Funding rates reported at 1h, 4h, or 8h are carried forward to one-minute frequency. This mechanically creates persistence. ADF, ACF, and GARCH results are partly artifacts of the interpolation. The paper acknowledges this but does not correct for it.
3. **Linear normalization.** Converting a 1h rate to an 8h equivalent by multiplying by 8 assumes the rate is constant over the interval. If funding is charged hourly and the rate varies, the 8h equivalent is an approximation. Cross-venue comparisons are affected.
4. **Max-min spread across 26 venues.** The spread is the difference between the highest and lowest rate across all venues. With 26 venues, the extreme order statistics are mechanically large. This overstates the executable opportunity for any single trader, especially after liquidity and capital constraints.
5. **Selection bias.** The portfolio analysis uses the top 20 opportunities by spread magnitude, selected ex post. This is an in-sample backtest, not an out-of-sample strategy. The average net P&L of USD 22 and the negative Sharpe are likely optimistic relative to a live strategy, not pessimistic.
6. **No depth or size impact.** The simulation uses USD 10,000 per side and fixed slippage. It does not model order-book depth, market impact, or the possibility that the quoted extreme rate is not available in the required size.
7. **No margin, liquidation, or counterparty risk.** The strategy is delta-neutral in notional but not in collateral. A price move, a margin call, an exchange outage, or a counterparty failure can force an exit independent of the funding spread. Paper 3 shows how large this risk is.
8. **No capital transfer or bridge latency.** CEX-DEX arbitrage requires moving capital or maintaining inventory on both venues. The paper acknowledges execution risk but does not model transfer times, withdrawal limits, or bridge risk.
9. **No shorting/borrow constraints.** The strategy assumes both legs can be opened at the quoted funding rate and size.
10. **Funding accrual approximation.** The paper divides the 8h rate by 480 and accrues per minute. Actual funding is paid at discrete intervals. The approximation is reasonable for long holds but can misstate P&L around settlement and for short holds.
11. **Non-stationarity and spurious correlation.** Correlations are computed on funding-rate levels, which are highly persistent. Correlation on levels can be spurious; first differences would be safer. The paper does not report differenced correlations.
12. **Granger causality is not economic causality.** Granger causality tests predictive content, not structural causation. Funding-rate leadership is not the same as underlying price discovery. The paper sometimes uses "price discovery" loosely.
13. **Small Granger sample.** Five exchange pairs and ten symbols; 92 tests. Multiple testing is not adjusted. The 62% significance rate is not corrected for false discovery.
14. **DEX heterogeneity.** The 15 DEXs use different funding mechanisms, oracles, collateral, and settlement. Treating them as one "DEX" tier hides substantial variation.
15. **CEX data quality.** Wash trading, fee rebates, and API reporting differences can distort funding and volume. The paper does not audit venue data quality beyond missingness.
16. **No regulatory or tax treatment.** Cross-venue arbitrage may trigger regulatory, KYC, and tax complications that are not modeled.
17. **No risk-free rate.** Sharpe ratios are computed without specifying the risk-free rate; with a negative average return, the exact rate does not change the sign but affects the magnitude.
18. **IGARCH interpretation.** With 8 days of data, IGARCH may reflect the sampling/interpolation scheme rather than true unit-root volatility. The paper flags this.
19. **"CEX integration 61% higher" is a correlation difference, not a structural integration measure.** It is a useful summary but not a formal test of market integration.
20. **Generalizability.** The sample is eight days in a specific period; the DEX/CEX ratio and funding regimes change quickly.

### What the paper does not claim

- It does not claim that all CEX-DEX spreads are arbitrageable.
- It does not claim that the strategy is profitable on average; the average is slightly positive but the Sharpe is negative.
- It does not claim that CEX causality is universal or that DEX never leads.
- It does not claim that the results hold in other regimes.
- It does not claim that the exit rule is optimal; it explicitly says alternative thresholds may improve results.
- It does not claim that the two-tier structure is permanent.

## Hackathon prep for this paper

### What this paper supports building

1. **A cross-venue funding and basis oracle.** Normalize rates, publish CEX-DEX spreads, and tag each opportunity with executable cost and reversal hazard.
2. **A lead-lag signal engine.** Use CEX funding innovations to forecast DEX funding changes, with a confidence score and a lag estimate.
3. **An execution-aware arbitrage router.** Compare gross spread, fees, slippage, gas, transfer latency, and expected duration; route only when net expected value is positive.
4. **A forced-exit / reversal-hazard model.** Predict the probability that the spread turns negative before the trade covers costs. Publish a survival score.
5. **A delta-neutral portfolio simulator.** Backtest cross-venue strategies with realistic costs, capital constraints, margin, and exit rules.
6. **A two-tier dashboard.** Show CEX-CEX, CEX-DEX, and DEX-DEX integration in real time; highlight the boundary as the opportunity set.
7. **A CEX-DEX basis vault.** A yield product that runs only executable spreads and budgets for forced exits.
8. **A market-structure research agent.** An AI copilot that explains why a spread exists, whether it is CEX-led or DEX-led, and what would make it disappear.

### What this paper does not support building directly

- A general payoff compiler. That is Paper 1.
- A no-arbitrage pricing model with funding clamps. That is Paper 2.
- A liquidation-optimal hedge. That is Paper 3.
- A funding-template calibration. That is Paper 4.
- A live cross-venue trading system without custody, API, and compliance work. The paper is an empirical study, not an execution blueprint.
- A claim that CEX-DEX arbitrage is risk-free. The paper shows the opposite.

### Data and tech needed

- One-minute funding rates, mark prices, index prices, and open interest across CEX and DEX.
- Contract specs: funding interval, cap, floor, settlement time, margin rules.
- Fee schedules, slippage curves, and gas costs.
- Withdrawal/deposit times and bridge latency for CEX-DEX routes.
- Liquidation and outage data.
- A time-series engine for ADF/KPSS, ACF/PACF, GARCH, VAR/Granger.
- A cost and execution simulator.
- An onchain oracle for normalized spreads, lead-lag scores, and reversal hazard.
- A frontend for cross-venue comparison and route simulation.

### Demo script (3 minutes)

**Act 1: The tempting spread.** Show a 200 bps CEX-DEX funding spread. A naive bot enters delta-neutral, long the low-funding DEX and short the high-funding CEX. Show the gross funding accrual. Then show the fees, slippage, and the spread reversing after 40 minutes. The trade loses.

**Act 2: The two-tier signal.** Show the CEX lead-lag score. The CEX funding rate moved first; the DEX followed. The router waits for the CEX signal, estimates the DEX lag, and computes the executable spread after costs. It sizes the trade only if the expected duration exceeds the break-even time.

**Act 3: The exit.** The reversal hazard crosses a threshold. The router exits before the spread turns negative. Show the net P&L, the survival score, and the onchain oracle update. Compare the naive bot and the router side by side.

### Judge Q&A prep

**"Isn't this just a funding-rate scanner?"** No. The scanner finds gross spreads. The product models execution costs, CEX-to-DEX lead-lag, duration, and reversal hazard, and only routes trades with positive expected net value.

**"Why does the two-tier structure exist?"** Liquidity, speed, and arbitrage infrastructure concentrate on CEX. DEX settlement is slower and more fragmented, so DEX funding follows CEX.

**"Why are only 8 of 20 top opportunities profitable?"** Because gross spread is not net profit. Costs are USD 60-220 per round trip, and 95% of spreads reverse. A 20 bps spread needs about 3.7 days to cover costs; most do not last.

**"Is the strategy profitable?"** On average, barely: USD 22 net on USD 10,000 per side, with a negative Sharpe. The point is that the naive version is not profitable; the execution-aware version can select the rare trades that are.

**"What is the moat?"** The cross-venue data, the lead-lag and reversal-hazard models, the cost model, and the execution integrations. The signal is commoditized; the execution layer is not.

**"How do you handle CEX custody and regulation?"** The hackathon version is a simulator plus an onchain oracle. Live execution requires regulated custody, API keys, and compliance. We do not custody funds in the demo.

**"Why should a DEX care?"** DEXs are price takers. A DEX that understands its lag and its funding feedback can improve oracle design, liquidity incentives, and listing decisions.

### Claims to avoid

- "Risk-free arbitrage."
- "17% of the market is free money."
- "CEX always leads DEX" — the paper's own Table 6 contradicts the zero-reverse claim.
- "95% of trades are forced exits" — the paper says 19/20 show reversal patterns and 12/20 are forced exits.
- "The strategy has a positive Sharpe" — the average Sharpe is -7.40.
- "The results generalise to all regimes" — eight days, one regime.
- "Gross spread equals profit" — costs and reversal risk dominate.

### Track and sponsor fit

- **DeFi/derivatives track:** CEX-DEX funding arbitrage router, delta-neutral vault.
- **Oracle track:** cross-venue funding/basis oracle, lead-lag score, reversal hazard.
- **Data/analytics track:** two-tier market dashboard.
- **AI/agent track:** research and execution copilot.
- **Risk/security track:** forced-exit and reversal-hazard model, venue-risk scoring.
- **Ethereum/L2 track:** DEX funding and oracle design; cross-chain execution.
- **Market microstructure track:** CEX-DEX integration and price discovery.

## Hackathon-level idea for this paper

### TierBridge: the execution-aware CEX-DEX funding arbitrage router

**One sentence.** TierBridge turns the paper's two-tier structure into an execution product: it detects CEX-led funding dislocations, prices the real cost of crossing to a DEX, predicts the reversal hazard, and routes only trades that survive.

**Why it is stronger than a scanner.** A scanner shows a spread. TierBridge shows the **net executable spread**, the **expected duration**, the **probability of forced exit**, and the **best route**. It is a decision engine, not a table.

**Core modules.**

1. **Normalizer.** Convert 1h/4h/8h funding to a common 8h equivalent; tag the original interval and the settlement schedule.
2. **Two-tier signal.** Estimate CEX-to-DEX lead-lag by symbol and venue; publish a confidence score.
3. **Cost engine.** Fees, slippage, gas, withdrawal/deposit latency, bridge cost, and capital carry.
4. **Duration model.** Estimate how long the spread is likely to persist; compute break-even time.
5. **Reversal hazard.** Predict the probability that the spread turns negative before break-even.
6. **Router.** Rank routes by expected net value; enforce exposure, venue, and counterparty limits.
7. **Exit engine.** Dynamic thresholds (−0, −5, −10 bps) with a stop-loss and a time stop.
8. **Oracle.** Publish normalized spreads, lead-lag scores, reversal hazard, and executable net spread onchain.

**Onchain architecture.**

- `FundingTierOracle.sol`: stores normalized rates, CEX-DEX lead-lag scores, and reversal hazard.
- `RouteRegistry.sol`: stores venue, symbol, fee, and latency metadata.
- `ExecutableSpread.sol`: computes net spread from oracle inputs and cost parameters.
- `RiskBudget.sol`: enforces position, venue, and counterparty limits.
- Off-chain keeper: ingests CEX/DEX data, estimates lead-lag and hazard, signs reports.
- Execution adapters: CEX API sandbox and DEX testnet; no live custody in the hackathon.
- Consumer adapters: basis vault, perp DEX, lending protocol, risk dashboard.

**Demo moment.** Show a 200 bps CEX-DEX spread. The naive bot enters and loses after fees and a reversal. TierBridge waits for the CEX lead signal, computes a 40-minute expected duration, requires a 60 bps net edge, enters, and exits before the reversal. Show the net P&L, the hazard score, and the onchain oracle update. Then show a second route that TierBridge rejects because the DEX leg is too illiquid.

**Moat.** The cross-venue dataset, the lead-lag and reversal-hazard models, the cost model, and the execution integrations. The router improves with every trade and every venue.

**Risks.** Data quality, CEX API restrictions, custody, latency, regulatory compliance, and model risk. Keep the hackathon version to a simulator plus an oracle, with execution only in a sandbox.

**Integration with the existing portfolio.** TierBridge is the execution layer of the Perp Forge stack:
- **Perp Forge** designs the instrument.
- **Basis Mesh** provides the no-arbitrage band.
- **Funding DNA** calibrates the funding feedback.
- **LiquidMesh** keeps the hedge alive.
- **TierBridge** executes the cross-venue trade and manages the exit.

**Score.** Novelty 5, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 29.

## Connection to the other papers

- **Paper 1 (Angeris et al.):** gives the general funding and replication language. TierBridge is an execution layer for linear funding-rate differentials, not a new payoff.
- **Paper 2 (He et al.):** gives the no-arbitrage band and basis deviation. TierBridge turns the band into an executable, cost-aware route.
- **Paper 3 (Alexander et al.):** gives liquidation-aware sizing. A cross-venue delta-neutral trade has two margin accounts and two liquidation risks; LiquidMesh should size both legs.
- **Paper 4 (Zhang):** gives effective funding and stability. TierBridge should use Funding DNA to avoid routes where the nominal spread is not delivered.
- **Synthesis:** Paper 1 designs; Paper 2 anchors; Paper 3 survives; Paper 4 calibrates; Paper 5 executes.

**Full ideation:** See [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md), especially the new Wave 3 sections on TierBridge, the Tier Oracle, reversal hazard, and the Perp Forge 3.0 stack.
