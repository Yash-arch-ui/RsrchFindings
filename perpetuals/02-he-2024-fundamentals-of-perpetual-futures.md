# Fundamentals of Perpetual Futures

## Metadata

- **Authors:** Songrun He (Washington University in St. Louis), Asaf Manela (Washington University in St. Louis and Reichman University), Omri Ross (University of Copenhagen and eToro Group), Victor von Wachter (University of Copenhagen)
- **Date:** First draft December 2022; this draft December 2024.
- **Identifier:** SSRN abstract ID 4301150 (paper previously circulated as arXiv:2212.06888)
- **Length:** 73 pages including appendices
- **Type:** Theory plus empirical asset pricing. Uses Binance data, Aave rates, and a trading-strategy backtest.
- **Read status:** Full paper read, including main text, appendices A-E, all tables, and proofs.

## One-paragraph summary

The paper asks what the fundamental value of a perpetual futures contract is and how large deviations from that value are in crypto markets. Because perps never expire, the usual futures-spot convergence argument does not apply. The authors introduce **random-maturity arbitrage**, a weaker notion of arbitrage with a bounded but random unwinding time. Under this notion, the no-arbitrage futures price is a constant multiple of the spot price, where the multiple depends on the exchange's funding-rate function. For Binance's clamped funding rule, they derive a closed-form benchmark and testable bounds with trading costs. Empirically, deviations are 52-90% per year in absolute value, comove across coins, and shrink over time. A theory-motivated convergence strategy earns high Sharpe ratios even under high trading costs. Past returns explain a significant part of the time-series variation in deviations.

## Section-by-section findings

### Abstract (p. 1)

- Perpetual futures are the most popular crypto derivatives.
- They offer leveraged exposure without rollover or direct ownership.
- Unlike fixed-maturity futures, they are not guaranteed to converge to spot.
- Funding payments are proportional to the futures-spot gap.
- The paper derives no-arbitrage prices in frictionless markets and bounds with trading costs.
- Empirically, crypto deviations are larger than in traditional currency markets, comove across currencies, and diminish over time.
- An implied arbitrage strategy yields high Sharpe ratios.

### Section 1: Introduction (pp. 2-7)

- Daily perp volume exceeds $100 billion.
- Alameda Research was a major counterparty before FTX collapsed.
- Perps are a single contract per underlying, traded 24/7, with no rollover.
- Funding is typically paid every 8 hours and approximately equals the average futures-spot spread over the prior 8 hours.
- **Funding-rate arbitrage is not riskless** even ignoring margin and trading costs, because there is no predetermined expiry at which the trade is unwound at a profit.
- The paper derives no-arbitrage prices and bounds.
- The theoretical price is proportional to spot, with the constant determined by interest rates and the exchange's funding mechanism.
- The **clamp** is a key feature: near parity, funding is fixed at a small positive value; when prices diverge, funding increases linearly with the gap.
- The futures-spot spread is larger when the cash borrowing rate is large relative to the funding rate.
- **Random-maturity arbitrage** is introduced: unlike riskless arbitrage, the strategy's time-to-maturity is random.
- Riskless no-arbitrage pricing is described as a useful fiction; real arbitrageurs must stay liquid longer than the market stays irrational.
- Empirical results:
  - Mean deviation from the benchmark is modest and statistically insignificant.
  - Mean absolute deviation is about 52% to 90% per year across cryptocurrencies.
  - This is considerably larger than deviations in traditional currency markets documented by Du, Tepper, and Verdelhan (2018).
  - Deviations comove strongly across cryptocurrencies.
  - Deviations decline over time, consistent with more arbitrage capital and competition.
  - The narrowing gap is linked to the downfall of Alameda Research and Three Arrows Capital.
- Strategy results:
  - Bitcoin perpetuals: Sharpe ratio 3.35 under high retail trading costs, up to 11.65 for market makers who pay no fees.
  - Performance is better for Ether and other cryptocurrencies.
  - The strategy delivers significant alphas relative to the Liu, Tsyvinski, and Wu (2022) 3-factor model and the Cong, Karolyi, Tang, and Zhao (2022) 5-factor model.
- Explanations for deviations:
  - Liquidity in crypto markets may be insufficient for arbitrageurs.
  - Past return momentum significantly explains the futures-spot gap, with a time-series regression R-squared above 15%.
  - When past returns are high, futures trade at a higher price relative to spot, consistent with positive feedback or momentum trading.
- Literature:
  - Empirical perp literature: Alexander et al. (2020), De Blasis and Webb (2022), Ferko et al. (2022), Streltsov and Ruan (2022).
  - Christin, Routledge, Soska, and Zetlin-Jones (2022): longing spot and shorting perps; profits attributed to differences of opinion and leverage constraints.
  - Angeris, Chitra, Evans, and Lorig (2022): theoretical no-arbitrage analysis; assumes the payoff is a fixed function of spot and focuses on security design; cannot analyze deviations.
  - Ackerer et al. (2023): no-arbitrage prices for perpetual contracts under an equivalent martingale measure.
- Three claimed contributions:
  1. No-arbitrage prices that incorporate realistic funding specifications, including the clamp.
  2. Explicit arbitrage bounds and trading strategies with transaction costs.
  3. Empirical evidence that the benchmark improves arbitrage strategy performance relative to existing approaches.
- Also relates to fixed-maturity crypto futures literature: Schmeling, Schrimpf, and Todorov (2022); Cong, He, and Tang (2022); Makarov and Schoar (2020); Duffie (2010); Gromb and Vayanos (2018); Lustig, Roussanov, and Verdelhan (2011).

### Section 2: An introduction to perpetual futures (pp. 7-11)

- Shiller (1993) introduced perpetual futures for illiquid assets such as housing, to enable price discovery.
- Crypto perps differ:
  - Crypto has no inherent dividend or cash flow.
  - Crypto spot prices are observable on active exchanges and DEXs.
  - The main role is leveraged hedging and speculation, making the market more complete.
  - Perps also serve as a tax-optimization tool.
- Crypto perps were introduced by BitMEX in 2016.
- Volume:
  - Median daily volume: $17.8 billion in 2020, $132.0 billion in 2021, $101.9 billion in 2022.
  - Perp volume is about 2x to 3.5x spot volume in 2022.
- Wash trading:
  - Cong, Li, Tang, and Yang (2022) estimate over 70% of crypto trading volume is not real.
  - Amiram, Lyandres, and Rabetti (2021) confirm.
  - The paper excludes exchanges known to misrepresent data.
- Funding mechanics:
  - Paid every 8 hours.
  - Approximately a weighted average of the prior 8 hours' futures-spot gap.
  - Positive funding: longs pay shorts; negative funding: shorts pay longs.
  - Perps cannot be replicated by rolling over 8-hour futures because the latter converge every 8 hours while perps do not.
- Figure 2: Bitcoin annualized funding rates across exchanges.
  - Funding is positive when the futures-spot spread is positive.
  - Funding rates are similar across exchanges due to cross-exchange arbitrage, but diverge in extreme liquidity episodes.
  - March 2020: funding turned substantially negative as COVID liquidity evaporated.
  - Early 2021: highly positive funding during the bull run.
  - FTX collapse: Bitcoin futures were substantially higher than spot on FTX, but the opposite on solvent exchanges, consistent with rapid liquidation of short futures positions.

### Section 3: Arbitrage in perpetual futures (pp. 11-22)

**Definition 1 (Perpetual futures):**

- A swap agreement with no cost to open.
- Both sides can terminate at any time.
- The long and short exchange a funding payment `Phi(F_s - S_s / F_s) ds`.
- `Phi` is a univariate function of the premium index `(F - S) / F`.
- If `Phi > 0`, long pays short; otherwise short pays long.
- Both sides post mark-to-market margin.

**Definition 2 (Riskless arbitrage):**

- A payoff `x` at a certain future time `tau` with price `p(x)`.
- Conditions: `x >= 0` almost surely; `x > 0` with positive probability; `p(x) <= 0`.

**Definition 3 (Random-maturity arbitrage):**

- A random payoff `x` at a bounded future random time `tau_tilde < T_bar`.
- Conditions: `x >= 0` almost surely; `x > 0` with positive probability; `p(x) <= 0`.

**Corollary 1:** In perps, a strategy with zero cost at time 0 and a bounded unwinding time at which the discounted payoff is positive is a random-maturity arbitrage.

**Assumptions:**

1. The risk-free short rate `r` is constant.
2. The gap between the futures price and its fundamental price is bounded: `sup_t |F_t - lambda S_t| < infinity`.
3. The funding-rate function `Phi` is a univariate, weakly increasing function of the premium index, with `sup{x : Phi(x) = r} < 1`.

**Proposition 1 (No-arbitrage price, general case):**

If `Phi^{-1}(r)` is a single point, under Assumptions 1-3 there is no random-maturity arbitrage if and only if:
```
F_t = (1 - Phi^{-1}(r))^{-1} S_t
```

**Intuition:**

- Conjecture `F_t = lambda S_t`.
- Both arbitrage strategies in Table 1 must have zero payoff.
- This requires `Phi = r` in equilibrium.
- The premium index is pinned at `Phi^{-1}(r)`.
- Rearranging gives the pricing formula.
- If the price deviates, the funding payments accumulate and eventually overcome any finite deviation, provided the deviation does not diverge.
- Two strategies in Table 1:
  - Long `lambda` spot, short 1 future.
  - Long 1 future, short `lambda` spot.
- Payoff decomposition in equation (8):
  - Initial spread.
  - Present value of cumulated funding payments.
  - Spread at unwinding, discounted.

**Proof logic (pp. 13-17):**

- If `F_0 > lambda S_0`, the first strategy is a random-maturity arbitrage.
  - The payoff is `-e^{-rt} u_t + u_0 + integral e^{-rs}(Phi - r) F_s ds`.
  - If the deviation returns to zero at a finite `t*`, the payoff is positive.
  - If `t* -> infinity`, the deviation would have to grow without bound to prevent profit, violating Assumption 2.
- If `F_0 < lambda S_0`, the second strategy is a random-maturity arbitrage.
  - The payoff is `e^{-rt} u_t - u_0 - integral e^{-rs}(Phi - r) F_s ds`.
  - Similar logic applies.

**Stochastic rates discussion:** The paper abstracts from stochastic short rates. Empirically, short-rate volatility is much smaller than deviation volatility, and rate changes have offsetting effects across cash, funding, margin, and reinvestment.

**Proposition 2 (Edge case):**

If `Phi^{-1}(r)` is an interval, no random-maturity arbitrage implies:
```
F_t in [ (1 - inf Phi^{-1}(r))^{-1} S_t,
         (1 - sup Phi^{-1}(r))^{-1} S_t ]
```

**Binance funding rule (equation 12):**
```
Phi(x) = kappa x + clamp(iota - kappa x, -gamma, gamma)
```
with:
- `kappa = 1`
- `gamma = 0.05%`
- `iota = 0.01%`

**Corollary 2 (No-arbitrage price for Binance):**

When `r != iota`:
```
F_t = kappa / (kappa + sgn(iota - r) gamma - r) * S_t
```

When `r = iota`:
```
F_t in [ kappa / (kappa + gamma - r) S_t,
         kappa / (kappa - gamma - r) S_t ]
```

**Proposition 3 (No-arbitrage bounds with trading costs):**

With a round-trip trading cost `C`, when `r != iota`:
```
F_t in [ benchmark - C, benchmark + C ]
```
where `benchmark = kappa / (kappa + sgn(iota - r) gamma - r) * S_t`.

- The bounds widen with trading costs.
- Deviations larger than `C` create a strong incentive to trade toward the benchmark.

### Relation to other theory papers (pp. 20-22)

- **Angeris et al. (2022):**
  - Inverse problem: assumes no arbitrage and that the perp price is a function of spot; derives the funding rate.
  - Security-design perspective.
  - In practice, funding mechanisms are predetermined and fixed for traders.
  - Because of limits to arbitrage, futures prices can deviate from the fixed function.
  - The paper's approach prescribes a strategy to exploit deviations.
- **Ackerer et al. (2023):**
  - Derives no-arbitrage prices for perpetual contracts under different assumptions.
  - The original December 2022 draft of this paper considered linear funding and derived:
    ```
    |F_t - (1 + r/kappa) S_t| <= C
    ```
    and, without costs:
    ```
    F_t = (1 + r/kappa) S_t
    ```
  - Ackerer et al. pointed out an inconsistency in the cash-flow specification of the original derivation.
  - After correction, the price is:
    ```
    F_t = kappa / (kappa - r) S_t
    ```
  - The authors credit Ackerer et al. for deriving the correct formula first.
  - Ackerer et al. do not allow for the clamp feature.
  - Han (2024): exchanges with the clamp feature account for more than 75% of Bitcoin perp open interest.
  - The paper's advantages: explicit arbitrage strategy, bounds with trading costs, and analysis of the clamped funding rate.

### Section 4: Data and empirical analysis (pp. 22-41)

**Data:**

- Five largest cryptocurrencies excluding stablecoins: BTC, ETH, BNB, DOGE, ADA.
- Total market cap $1.83 trillion, 73.2% of crypto market share by March 2024.
- Source: Binance.
- Frequency: 1-hour futures and spot prices.
- Funding payments at 8:00, 16:00, and 0:00 GMT.
- Sample: January 8, 2020 to March 11, 2024.
- Table 2 sample sizes:
  - BTC: 36,578
  - ETH: 36,578
  - BNB: 35,777
  - DOGE: 32,152
  - ADA: 36,017

**Trading costs (Table 3, maker fees):**

| Tier | Spot | Futures | Lower bound rho_l | Upper bound rho_u |
|---|---:|---:|---:|---:|
| No | 0.0000% | 0.0000% | 0.0% | 0.0% |
| Low | 0.0225% | 0.0018% | -53.2% | 53.2% |
| Medium | 0.0450% | 0.0072% | -114.3% | 114.3% |
| High | 0.0675% | 0.0144% | -179.4% | 179.4% |

- High fees: 30-day spot volume above $1 million and futures volume above $15 million (individual trader).
- Medium fees: spot above $150 million and futures above $1 billion (small funds).
- Low fees: spot above $2 billion and futures above $12.5 billion (large funds).
- No-fee tier: negotiated, e.g., market makers.
- Indirect costs:
  - Kaiko one-month order-book snapshots every 30 seconds.
  - Average bid-ask spread for perps: less than 0.05 bps.
  - Price impact for a $500,000 trade: less than 0.3 bps.
  - Combined indirect costs: less than 0.35 bps.
  - Direct fees dominate.

**Risk-free rate:**

- Aave stablecoin supply and borrowing rates.
- Three stablecoins: USDT, USDC, DAI.
- Average of the three rates.
- Robustness: T-bill rates from Kenneth French's data library and Binance margin borrowing rates.

**Deviation definition (equation 20):**
```
F = kappa / (kappa + sgn(iota - r) gamma - (r + rho)) * S
```

**Rearranged (equation 21):**
```
rho = kappa ((F - S) / F) + sgn(iota - r) gamma - r
```

- `rho` is the annualized interest-rate spread that would rationalize the observed futures-spot spread.
- An estimated `rho` of 1 percentage point means borrowing costs faced by arbitrageurs would have to be 1 percentage point higher than the prevailing rate for the gap to be arbitrage-free.
- Similar in spirit to covered interest parity deviations in Du, Tepper, and Verdelhan (2018).

**Threshold mapping (equations 22-23):**
```
F - kappa / (kappa + sgn(iota - r) gamma - r) S = c F
rho = (kappa + sgn(iota - r) gamma - r) c ~= kappa c
```
- Trading threshold is approximately `+/- kappa c`.

**Empirical results (Table 4, Figure 4):**

Panel A, full sample:

| Asset | Mean rho | Median rho | Std rho | Mean abs rho | Median abs rho | Std abs rho |
|---|---:|---:|---:|---:|---:|---:|
| BTC | 0.42 | 0.19 | 0.84 | 0.52 | 0.25 | 0.78 |
| ETH | 0.52 | 0.26 | 0.88 | 0.63 | 0.33 | 0.81 |
| BNB | 0.38 | 0.31 | 1.23 | 0.90 | 0.68 | 0.92 |
| DOGE | 0.50 | 0.23 | 1.90 | 0.86 | 0.49 | 1.77 |
| ADA | 0.51 | 0.27 | 1.27 | 0.84 | 0.53 | 1.08 |

Panel B, before and after 2022:

| Asset | Mean rho before 2022 | Mean rho after 2022 |
|---|---:|---:|
| BTC | 0.69 | 0.17 |
| ETH | 0.92 | 0.16 |
| BNB | 0.69 | 0.11 |
| DOGE | 0.99 | 0.18 |
| ADA | 0.99 | 0.09 |

- Deviations are predominantly positive: perps are more expensive than the no-arbitrage price on average.
- Standard deviation exceeds twice the mean for most tokens.
- Mean absolute deviation is about 52% to 90% per year.
- After 2022, average deviation falls by almost 80%; volatility shrinks by more than 50% for most tokens.
- Figure 4 shows strong comovement across the five cryptocurrencies.
- Table 5 correlation of arbitrage deviations:
  - Pairwise correlations range roughly from 0.44 to 0.88.
  - Deviations are highly correlated with funding rates.
  - Deviations have only modest correlation with spot returns, even though spot returns are highly correlated with each other.
- Interpretation:
  - Common factor in deviations, consistent with time-varying funding constraints of arbitrageurs and/or common sentiment/relative demand.
  - The market becomes more efficient over time.

### Section 4.3: Random-maturity arbitrage strategy (pp. 31-38)

**Trading rule:**

- Open when `rho` exits the annualized round-trip trading-cost band.
- Close when `rho` first returns to 0.
- When `rho` is above the band: long spot, short perp.
- When `rho` is below the band: short spot, long perp.

**Figure 5:** Illustration for Bitcoin; blue dots are hourly deviations; red line is the zero benchmark.

**Table 6: Performance over time under high trading costs.**

The table reports Sharpe ratios, annualized returns, volatility, maximum drawdowns, open-to-close time, and observations. The reported Sharpe ratios match the no-fee tier in Table 8, so treat the "high trading costs" caption as a likely labeling inconsistency and cross-check Table 8 for the fee-tier results.

Representative no-fee/high-cost values from the paper:

- BTC: Sharpe 11.65 no-fee; 3.35 high-fee.
- ETH: Sharpe 12.77 no-fee; 4.56 high-fee.
- BNB: Sharpe 17.66 no-fee; 6.93 high-fee.
- DOGE: Sharpe 14.90 no-fee; 4.97 high-fee.
- ADA: Sharpe 19.76 no-fee; 4.14 high-fee.

**Table 7: Strategy comparisons.**

| Strategy | BTC Sharpe | ETH Sharpe | BNB Sharpe | DOGE Sharpe | ADA Sharpe |
|---|---:|---:|---:|---:|---:|
| Correct (this paper) | 11.65 | 12.77 | 17.66 | 14.90 | 19.76 |
| No clamp | 6.06 | 8.29 | 14.71 | 12.68 | 15.78 |
| Long-only | 8.20 | 8.64 | 11.28 | 9.29 | 12.13 |
| Carry (Christin et al.) | 2.24 | 2.66 | -0.66 | 1.01 | 1.64 |

- Ignoring the clamp roughly halves the BTC Sharpe ratio.
- The long-only restriction (no shorting spot) still produces high Sharpe ratios.
- The dynamic strategy significantly outperforms the static carry strategy.

**Table 8: Performance across trading-cost tiers.**

- As trading costs increase, the strategy trades less often and open-to-close duration increases.
- Even at the high-cost tier, Sharpe ratios are significant and large.
- Examples:
  - BTC: 11.65 (no fee), 4.30 (low), 3.38 (medium), 3.35 (high).
  - ETH: 12.77, 5.83, 4.69, 4.56.
  - BNB: 17.66, 11.41, 8.35, 6.93.
  - DOGE: 14.90, 9.22, 6.27, 4.97.
  - ADA: 19.76, 10.72, 6.03, 4.14.

**Table 9: Return decomposition.**

- Decomposes returns into price convergence vs funding-rate payments.
- Price convergence dominates.
- BTC all-sample: price 37.61 vs funding 14.94.
- ETH all-sample: price 45.05 vs funding 17.78.
- BNB all-sample: price 111.75 vs funding 23.16.
- DOGE all-sample: price 164.26 vs funding 16.70.
- ADA all-sample: price 135.50 vs funding 19.62.
- Funding becomes a smaller share over time.

**Annualization:**

- For threshold strategies, the paper follows Lucca and Moench (2015):
  ```
  SR = (mu / sigma) * sqrt(N_a)
  ```
  where `N_a` is the average number of active hours in a year.
- Annualized return: `mu_ann = mu * N_a`.
- Annualized volatility: `sigma_ann = sigma * sqrt(N_a)`.

### Section 4.4: Liquidation risk (pp. 38-41)

- Arbitrage capital is `M`; spot and futures margin accounts are linked.
- Scenario 1: `F_0 > lambda S_0`.
  - No early liquidation requires the payoff to exceed `-M`.
  - The upper bound process grows exponentially: `u_bar_t = e^{(kappa - gamma) t} (u_0 + M)`.
  - Funding accumulation makes the bound less binding over time.
  - If `u_t <= u_0 + M`, the arbitrageur never faces early liquidation.
- Scenario 2: `F_0 < lambda S_0`.
  - In the clamp region, the lower bound process is `u_t = u_0 - M + integral e^{-rs}(iota - r) F_s ds`.
  - If `u_t >= u_0 - M`, the arbitrageur avoids early liquidation.
- Empirically, maximum drawdowns are small:
  - BTC: above -4% across specifications.
  - Other coins similar.
- **Finding:** Liquidation risk can be avoided with a modest amount of capital in random-maturity arbitrage.

### Section 5: Explaining futures-spot deviations (pp. 41-45)

Two hypotheses:

1. Time-varying funding constraints of arbitrageurs.
2. Time-varying relative demand from end-users for perps vs spot.

Regression models (equations 26-27):
```
rho_i,t = beta_0,i + beta_1 (t / 365) + beta_2 R_i,t-120:t-1 + beta_3 sigma_i,t-120:t-1 + epsilon_i,t
|rho_i,t| = beta_0,i + beta_1 (t / 365) + beta_2 R_i,t-120:t-1 + beta_3 sigma_i,t-120:t-1 + epsilon_i,t
```

- `t` is days since the start of the sample.
- `R` is the annualized spot return over the past 120 days.
- `sigma` is the annualized standard deviation of spot returns over the past 120 days.
- Currency fixed effects included.
- Newey-West t-statistics with 120-day lag.

**Table 10 results:**

- Time trend is negative and highly significant for both `rho` and `|rho|`.
- `|rho|` shrinks about 11% per year; the text elsewhere says about 22% per year and over 44% over the whole sample. Treat the exact rate as specification-dependent; the direction is robust.
- Past returns are positive and highly significant for both `rho` and `|rho|`.
- Volatility is not significantly related to `|rho|`.
- R-squared: 0.07 to 0.25 depending on specification.
- **Interpretation:** The common factor in deviations is more consistent with relative demand/sentiment than with time-varying funding constraints.

### Section 6: Conclusion (p. 45)

- Perps are important and may be adopted in non-crypto markets.
- In a frictionless world, arbitrageurs enforce a constant proportional relationship between futures and spot.
- With trading costs, deviations lie within a bound.
- Empirically, deviations comove across cryptocurrencies and the theory-motivated strategy earns high Sharpe ratios.
- The evidence supports the random-maturity arbitrage interpretation.
- The common factor is explained by sentiment/relative demand, with past-return momentum accounting for a significant portion of time-series variation.

### Appendix A: Additional figures (pp. 49-53)

- Figure A.1: Binance BTC perp trading view.
- Figure A.2: Spot trading-cost tiers (VIP 1, 5, 8).
- Figure A.3: Perp trading-cost tiers (VIP 1, 5, 8).
- Figure A.4: Aave annualized supply and borrow rates for USDT, USDC, DAI.
- Figure A.5: Trading strategy visualization with high, medium, and low cost thresholds.

### Appendix B: Funding rate calculation on Binance (pp. 54-56)

Five steps:

1. Compute Impact Bid/Ask Price Series:
   ```
   IMN = 200 / initial margin rate at maximum leverage
   ```
2. Compute premium index every 5 seconds:
   ```
   P = [max(0, IBP - PriceIndex) - max(0, PriceIndex - IAP)] / PriceIndex
   ```
3. Compute time-weighted average premium index over the 8-hour period:
   ```
   P_bar = sum_t w_t P_t / sum_t w_t,  w_t = t
   ```
   with `T = 5760` five-second intervals.
4. Compute funding rate:
   ```
   fr = P_bar + clamp(iota - P_bar, -0.05%, 0.05%)
   ```
5. Apply capped funding rate based on maximum leverage:
   - Max leverage 30x or above: cap/floor determined by maintenance margin ratio.
   - Max leverage 25x or below: fixed at +/-3%.
   - Higher leverage can expand the cap/floor.

- The clamp keeps funding predictable near parity.
- The cap limits losses from extreme funding during volatile periods.
- The paper's theoretical model uses the uncapped `Phi`; the cap is a practical detail that can matter in extreme markets.

### Appendix C: Data-driven arbitrage strategy (pp. 57-65)

- Two-threshold strategy `(u, l)`.
- Open when `rho > u` or `rho < -u`.
- Close when `-l < rho < l`.
- Grid: 0% to 250% in 10% increments; 351 specifications.
- Choose the model with the highest Sharpe ratio in the past six months.
- Results are similar to or slightly worse than the theory-motivated strategy.
- **Finding:** The theory-motivated strategy is close to optimal; extra flexibility adds little.

### Appendix D: Robustness to alternative risk-free rates (pp. 66-67)

- Uses T-bill rates from Kenneth French's data library and Binance margin borrowing rates.
- Performance is robust across cost tiers.
- Example BTC no-fee Sharpe: 11.65 (Aave), 11.73 (Binance), 11.65 (French).
- Example BTC high-cost Sharpe: 3.35 (Aave), 2.88 (Binance), 3.79 (French).

### Appendix E: Proofs (pp. 69-73)

- Proof of Proposition 2 (edge case).
- Proof of Corollary 2 (Binance price).
- Proof of Proposition 3 (bounds with costs).
- Key exponential divergence result:
  ```
  u_t = e^{(kappa - gamma) t} u_0
  ```
  which violates the bounded-deviation assumption if the deviation persists.

## Equation ledger

| Result | Equation |
|---|---|
| General no-arbitrage price | `F = (1 - Phi^{-1}(r))^{-1} S` |
| Binance funding rule | `Phi(x) = kappa x + clamp(iota - kappa x, -gamma, gamma)` |
| Binance price, `r != iota` | `F = kappa / (kappa + sgn(iota - r) gamma - r) * S` |
| Binance price interval, `r = iota` | `F in [kappa/(kappa + gamma - r) S, kappa/(kappa - gamma - r) S]` |
| Bounds with cost `C` | `F in [benchmark - C, benchmark + C]` |
| Deviation measure | `rho = kappa ((F - S) / F) + sgn(iota - r) gamma - r` |
| Cost threshold | `rho ~= kappa c` |
| Time-series regression | `rho_i,t = beta_0,i + beta_1(t/365) + beta_2 R_i,t-120:t-1 + beta_3 sigma_i,t-120:t-1 + epsilon_i,t` |
| Liquidation bound (upper region) | `u_bar_t = e^{(kappa - gamma) t} (u_0 + M)` |

## Paper-specific findings

1. **Perps need a new arbitrage concept.** Because there is no expiry, riskless arbitrage does not pin the price. Random-maturity arbitrage does.
2. **The fundamental price is `F = (1 - Phi^{-1}(r))^{-1} S`.** The exchange's funding function and the risk-free rate determine the futures-spot ratio.
3. **The clamp is economically important.** Ignoring it roughly halves the BTC strategy Sharpe ratio (11.65 vs 6.06).
4. **Real deviations are large.** Mean absolute deviation is 52-90% per year, much larger than traditional FX CIP deviations.
5. **Deviations comove across coins.** Pairwise correlations are roughly 0.44-0.88, indicating a common factor.
6. **Deviations shrink over time.** After 2022, the mean deviation falls by almost 80% and volatility falls by more than 50%.
7. **The arbitrage strategy is profitable even at high fees.** BTC Sharpe 3.35 at high retail costs; 11.65 with no fees. Other coins are higher.
8. **Price convergence, not funding carry, drives returns.** The decomposition shows convergence dominates funding payments.
9. **Liquidation risk is mild.** BTC max drawdown is above -4%; modest capital avoids early liquidation.
10. **Past returns explain deviations.** Momentum/extrapolative demand is significant; volatility is not. This favors a demand/sentiment explanation over a funding-constraint explanation.
11. **The market is becoming more efficient.** The trend is negative and significant, consistent with more arbitrage capital and competition.
12. **Data-driven thresholds do not beat the theory.** Extra flexibility adds little, supporting the theoretical benchmark.

## Limitations and caveats

- **Binance only.** The empirical work uses a single CEX; no onchain perp DEXs (dYdX, GMX, Hyperliquid) or other CEXs.
- **Five assets.** BTC, ETH, BNB, DOGE, ADA only.
- **Sample ends March 2024.** The post-2024 period is not covered.
- **Constant risk-free rate.** The theory assumes a constant `r`; stochastic rates are left for future work.
- **No structural model of demand.** The sentiment/relative-demand explanation is reduced-form.
- **Trading costs are maker fees.** Taker fees, latency, and execution risk are not the focus.
- **Indirect costs** are estimated from one month of Kaiko data and are small relative to direct fees.
- **Funding cap ignored in theory.** Binance applies a capped funding rate; the model uses the uncapped `Phi`. This may matter in extreme markets.
- **Possible omitted risks.** High Sharpe ratios could reflect risks not captured by the 3-factor and 5-factor models, or capacity constraints.
- **Caption inconsistency.** Table 6's "high trading costs" label conflicts with its numbers matching the no-fee tier in Table 8; use Table 8 for fee-tier comparisons.
- **Decline-rate inconsistency.** The introduction says about 22% per year; Section 5 says about 11% per year for `|rho|`. Treat the precise rate as specification-dependent; the sign and significance are robust.
- **No execution engine.** The strategy is a backtest, not a live trading system.
- **No cross-venue arbitrage.** The paper does not exploit funding differences across exchanges.
- **No DeFi execution.** Shorting spot and financing onchain have different frictions from Binance.

## Hackathon prep for this paper

### What this paper supports building

- A **clamp-aware perp basis scanner**: compute `rho`, the no-arbitrage band, and alerts in real time.
- A **cross-venue funding dashboard**: compare Binance, Bybit, Hyperliquid, dYdX, GMX.
- A **backtesting engine** for random-maturity arbitrage with configurable fees and risk bounds.
- A **risk/analytics oracle**: publish the no-arbitrage band and liquidation-risk bound.
- A **research notebook/demo** reproducing Table 4 and Table 8 on public data.

### What this paper does not support building directly

- A live, regulated, capital-deploying arbitrage fund.
- A production execution system with custody, margin, and cross-exchange transfer.
- A guaranteed-profit bot; deviations can widen and liquidations can happen.
- A full structural model of crypto demand.

### Data and tech needed

- CEX APIs: Binance, Bybit, OKX, Hyperliquid, dYdX.
- Funding rates, mark/index prices, order books.
- Stablecoin lending rates: Aave, Compound, or a money-market benchmark.
- Historical data for backtests: at least 2020-2024 for a paper-comparable sample.
- Backend: Python/FastAPI or Node; PostgreSQL/TimescaleDB; WebSocket ingestion.
- Frontend: React/Next.js with charts.
- Alerts: Telegram/Discord bot.
- Optional: onchain alert contract or a Chainlink/Pyth-based dashboard.

### Demo script (3 minutes)

1. Show live `rho` for BTC and ETH across two venues.
2. Show the clamp-aware no-arbitrage band vs a naive linear-funding band.
3. Toggle the clamp on/off and show how the signal changes.
4. Show the historical 2021 vs 2024 deviation regime.
5. Show the backtest: Sharpe ratio under no/low/medium/high fees.
6. Show the liquidation-risk bound and the open-to-close duration.
7. Trigger an alert when `rho` exits the band.

### Judge Q&A prep

- **"Isn't this just funding-rate arbitrage?"** No. The contribution is the clamp-aware no-arbitrage benchmark, the random-maturity risk framing, the cost bounds, and the empirical decomposition showing convergence dominates funding carry.
- **"Why not execute trades?"** Execution requires custody, margin, cross-venue transfers, and regulatory compliance. A scanner/backtester is the credible first product; execution can be a roadmap item.
- **"How is this different from Coinglass or Laevitas?"** Those show funding rates. This computes the paper's no-arbitrage band, cross-coin common factor, liquidation-risk bound, and fee-tier backtest.
- **"Is the edge gone?"** Deviations have shrunk, but the paper still finds a BTC Sharpe of 3.35 at high fees through March 2024. Capacity and execution matter.
- **"What about data biases?"** We use public exchange APIs, maker fees, and conservative cost tiers; we disclose latency and slippage assumptions.
- **"Can this work onchain?"** The analytics layer can; execution on perp DEXs is a later step because of liquidity and margin differences.
- **"What is the business model?"** Subscription analytics, alerts, API access, or a research/risk product for funds and protocols.

### Claims to avoid

- "Risk-free arbitrage."
- "Guaranteed profit."
- "Works at any size."
- "The edge is permanent."
- "Backtest equals live performance."
- "This is investment advice."

### Track and sponsor fit

- DeFi/analytics, data infrastructure, trading tools, risk management, oracle/data sponsors.
- Strong for a **product/demo** prize; the idea is easy for judges to understand.
- Weaker on novelty because funding dashboards exist; differentiation must be the clamp-aware benchmark and risk framework.

## Hackathon-level idea for this paper

### Basis Mesh: data, signal, and trust layers for perp basis

The original "Basis Radar" was a clamp-aware scanner. The evolved idea is a three-layer network.

**One-liner:** A cross-venue, cross-chain basis intelligence network with a clamp-aware no-arbitrage band, an onchain fair-value oracle, and an AI risk copilot.

**User:** Crypto quant traders, market makers, prop desks, DeFi risk teams, lending protocols, and perp DEX designers.

**Core flow:**

1. **Data layer:** ingest prices, funding rates, and stablecoin yields from CEXs and perp DEXs.
2. **Signal layer:** compute `rho`, the clamp-aware band `+/- C`, the cross-coin common-factor index, and liquidation bounds.
3. **Trust layer:** publish the fair-value band and a risk score onchain for other protocols.
4. **Copilot:** an agent explains dislocations, proposes hedges, and shows the risk decomposition.
5. **Backtest:** fee-tier backtests, convergence-vs-funding decomposition, and capacity estimates.

**Differentiator:** The scanner is the UI, the oracle is the moat, and the copilot is the demo. This is a protocol primitive, not another funding table.

**Build scope:**

- Two or three venues and three assets.
- Live `rho`, band, and common-factor charts.
- Historical backtest with fee tiers and liquidation bounds.
- Onchain fair-value oracle on a testnet.
- AI copilot with deterministic signals and human-approved execution.
- Optional: intent-based execution layer for basis trades.

**Judging alignment:** High demoability, strong data/DeFi/AI narrative, and a credible research foundation. The oracle and copilot raise the ceiling beyond a dashboard.

**Full ideation:** See [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md), especially the Basis Mesh, Basis Oracle, Perp Sim, Agentic Copilot, and Funding Firewall sections, plus the Wave 2 Funding DNA and Cascade Sim additions.
