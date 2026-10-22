# Hedging with Automatic Liquidation and Leverage Selection on Bitcoin Futures

## Metadata

- **Authors:** Carol Alexander (University of Sussex Business School; Peking University HSBC Business School), Jun Deng (University of International Business and Economics), Bin Zou (University of Connecticut)
- **Journal:** European Journal of Operational Research, 2023, vol. 306, issue 1, pp. 478-493
- **DOI:** 10.1016/j.ejor.2022.07.037
- **History:** Received 21 February 2022; accepted 22 July 2022; published 2023
- **License:** CC BY-NC-ND 4.0
- **Length:** 16 pages main text plus online appendix
- **Type:** Applied theory plus empirical market microstructure. New hedging model, new speculation metrics, minute-level data.
- **Data:** Coinbase bitcoin spot and seven bitcoin-dollar perpetuals on five exchanges (Binance, BitMEX, Bybit, Deribit, OKEx); minute-level price data; 4-hour volume, open interest, and liquidation data; 1 July 2020 to 31 May 2021 for most contracts; Coinbase spot and BitMEX inverse perpetual from 1 November 2017.
- **Read status:** Full main text read, including all numbered equations, tables, figures, institutional details, and references. The online appendix (GEV estimation details and additional tables) was not included in the retrieved PDF; claims that depend on it are marked as such.

## One-paragraph summary

The paper asks how a bitcoin holder should hedge spot price risk with bitcoin perpetual futures when the exchange can automatically liquidate the hedge without notice. The hedger chooses leverage and collateral, so the hedge has two conflicting objectives: minimize the variance of the hedged portfolio and minimize the probability that the futures leg is liquidated. The authors derive a semi-closed-form optimal static hedge ratio. The solution combines the classical minimum-variance ratio with a nonlinear correction driven by the right tail of extreme futures returns, the maintenance margin rate, the hedger's collateral constraint, and a loss-aversion parameter. Empirically, the optimal strategy achieves hedge effectiveness above 90-95% when collateral is adequate, cuts liquidation probability to roughly 0.1-3%, and keeps implied leverage mostly below 5x. Inverse (BTC-margined) perpetuals slightly outperform direct (USDT-margined) perpetuals, especially at longer horizons, because direct products attract much more speculative and liquidation-prone trading. The paper also introduces leverage-weighted liquidation and aggressiveness indices that are better suited to crypto exchanges than the traditional volume-to-open-interest speculation index.

## Section-by-section findings

### Abstract and Introduction (pp. 1-3)

- The core problem: on 24/7 non-KYC crypto derivatives exchanges, the trader self-selects margin and leverage. If the margin account falls below the maintenance level, the matching engine liquidates the position immediately and automatically. There are no margin calls in the traditional sense.
- Scale: according to CryptoQuant, almost USD 80 billion of positions on centralised exchanges were liquidated during 2021, an average above USD 200 million per day.
- Hedgers of bitcoin price risk include miners, crypto exchanges, market makers, brokers, and custodians whose revenues are bitcoin-denominated but whose costs are fiat-denominated.
- The paper is about risk reduction, not utility-maximising speculation. This distinguishes it from Deng, Pan, Zhang, and Zou (2021), who study optimal bitcoin trading with inverse futures.
- Bitcoin markets are segmented across venues and contract types. There are direct perpetuals (USDT-margined) and inverse perpetuals (BTC-margined), plus fixed-expiry futures. CME has margin calls but closes on weekends; the 24/7 venues have no margin calls and act as their own central counterparty.
- Leverage can reach 100x or more. Initial margin is the reciprocal of leverage; maintenance margin is roughly 50% of initial margin.
- On 19 May 2021 the bitcoin spot price fell from about USD 38,000 to USD 30,000 in 30 minutes. Binance futures froze for nearly two hours, so a liquidated hedger could not re-enter at the same price. This motivates liquidation loss aversion.
- Main contributions:
  1. A new hedging problem with dual objectives: variance and liquidation probability.
  2. A semi-closed-form optimal strategy under a margin constraint and loss aversion.
  3. New speculation metrics that account for leverage and liquidations.
  4. An empirical comparison of direct versus inverse perpetuals across five exchanges.
- Main findings previewed:
  1. Hedge size falls with tighter margin and higher liquidation loss aversion.
  2. Hedge effectiveness exceeds 90% (sometimes almost 99%) if margin is not too low.
  3. Liquidation probability falls below 1% in most scenarios; optimal leverage is mostly below 5x, sometimes only 2x.
  4. Inverse and direct products are both effective; inverse has a slight advantage.
  5. Hedge effectiveness is similar across exchanges, but speculative activity differs: Deribit lowest, Bybit/OKEx/Binance highest depending on metric.

### Section 2.1: Types of bitcoin futures (p. 3)

- **Standard futures:** fixed expiry, regular roll schedule. CME is the main regulated venue; BitMEX and Bybit list quarterly contracts; Huobi, Kraken, and OKEx list weekly contracts.
- **Perpetual futures:** no expiry. The dominant crypto derivative.
- **Direct perpetual:** quoted, margined, and settled in USDT; notional typically 1 BTC; P&L in USDT.
- **Inverse perpetual:** quoted in USD but margined and settled in BTC; notional typically USD 100; P&L in BTC.
- The contract type changes both the P&L formula and the collateral asset, which matters for liquidation.

### Section 2.2: Choice of futures contract in hedging (pp. 3-4)

- The paper focuses on 24/7 venues because bitcoin spot trades continuously and CME closes on weekends.
- Perpetuals are preferred for hedging because:
  1. They match any hedge horizon exactly.
  2. There is no rollover.
  3. Each 24/7 venue typically offers one inverse and/or one direct perpetual, avoiding term-structure choice.
  4. Perpetuals are more liquid than any single fixed-expiry contract. Deribit's weekly perpetual volume is three to eight times its total standard futures volume.
  5. No roll means lower transaction costs; funding payments are small and often offset over the hedge horizon.
- The perpetual price is kept close to spot by the funding rate. Typical funding has a small base rate (about 1 bp) plus a premium of about 3-5 bp paid by the long to the short when the perp trades above spot, and vice versa.
- Basis risk is therefore small relative to fixed-expiry futures, where backwardation and contango create roll-cost uncertainty.

### Section 2.3: Data description (p. 4)

- Inverse perpetuals: BitMEX, Bybit, Deribit, OKEx.
- Direct perpetuals: Binance, Bybit, OKEx.
- Spot: Coinbase.
- Data vendor: CoinAPI, minute-level.
- Most perpetual data: 1 July 2020 to 31 May 2021, 482,400 minute observations.
- Coinbase spot and BitMEX inverse perpetual: 1 November 2017 to 31 May 2021, used for longer GEV parameter estimation.
- Deribit is included because about 90% of the bitcoin options market trades there and professional option traders use the perpetual for delta hedging.
- The paper acknowledges that 24/7 non-KYC exchanges may engage in wash trading, which can inflate volume data.

### Section 2.4: Margin and liquidation mechanisms (pp. 4-6)

- Previous bitcoin hedging papers either set margins to zero or ignore them. This paper argues that is wrong because leverage is extremely high and liquidation is automatic.
- Liquidation data:
  - Five exchanges have average weekly open interest of USD 30.68bn (Binance), 29.56bn (BitMEX), 75.04bn (Bybit), 23.42bn (Deribit), and 14.64bn (OKEx).
  - Bybit has the largest unsettled open perpetual positions.
  - Average daily long liquidations: USD 130.76m (Binance), 113.24m (BitMEX), 349.70m (Bybit), 22.19m (Deribit), 37.11m (OKEx).
  - Average daily short liquidations: USD 42.42m (Binance), 59.88m (BitMEX), 116.37m (Bybit), 19.42m (Deribit), 15.93m (OKEx).
  - In the sample, Bybit accounts for 51.3% of liquidations, Binance 19.1%, BitMEX 19.2%, Deribit 4.6%, OKEx 5.8%.
- On 19 May 2021, total liquidations across all products and exchanges were USD 8.6bn. Only USD 339m were reported for the instruments in this study. The paper attributes much of the gap to Binance reporting inaccurate data in May 2021.
- A simulation using Bybit direct perpetuals (Table C.1 in the online appendix) finds:
  - At 100x leverage, more than 95% of 1-day positions are liquidated.
  - Liquidation probability becomes very high beyond five days.
  - Long positions have lower liquidation probability than short positions, but a bitcoin holder needs a short hedge.
  - A 5x short held for 15 days has more than a 10% average chance of liquidation, and much higher in volatile periods.
- Therefore the paper restricts hedge horizons to at most five days and treats the hedge as a sequence of short static hedges that can be rebalanced (for example every 8 hours).
- Liquidation price mechanics: the exchange triggers liquidation when the margin account falls below the maintenance level. The paper uses a maintenance margin rate `m_0` and a collateral constraint `m`, with the initial margin rate equal to `m / theta` for direct perpetuals. The implied leverage is `theta / m`.

### Section 2.5: Speculation metrics (pp. 6-7)

The standard speculative index is:

```
SI = Trading Volume / Open Interest
```

The paper argues `SI` is inadequate for crypto because it ignores leverage and liquidations, and because negative maker fees and wash trading distort volume.

It proposes three families of metrics.

**Liquidation indices:**

```
LIQ_short = Short Liquidation Volume / Open Interest
LIQ_long  = Long Liquidation Volume / Open Interest
LIQ       = (Total Liquidation Volume) / Open Interest
```

**Leverage estimates from liquidated positions.** For an inverse contract, if a position opened at `F_t1` is liquidated at `F_t2` with maintenance margin rate `m_0`, the estimated leverage is:

```
lambda_long  = F_t2 / ((1 + m_0) F_t1 - F_t2),   F_t1 > F_t2
lambda_short = F_t2 / (F_t2 - (1 - m_0) F_t1),   F_t1 < F_t2
```

For a direct contract:

```
lambda_long  = F_t1 / (F_t1 - (1 - m_0) F_t2),   F_t1 > F_t2
lambda_short = F_t1 / ((1 + m_0) F_t2 - F_t1),   F_t1 < F_t2
```

**Liquidation-volume-weighted leverage indices:**

```
LEV_long  = sum_j LongLiquidationVolume(j) * lambda_long(j)  / TotalLongLiquidation
LEV_short = sum_j ShortLiquidationVolume(j) * lambda_short(j) / TotalShortLiquidation
```

**Aggressiveness indices:**

```
AI_long  = sum_j LongLiquidationVolume(j) * lambda_long(j)  / OpenInterest
AI_short = sum_j ShortLiquidationVolume(j) * lambda_short(j) / OpenInterest
```

Main results:

- Deribit has the lowest leverage and speculation on every metric.
- Leverage elsewhere is roughly 14-22x.
- For inverse perpetuals, long positions have lower leverage than short positions.
- Direct perpetuals show much more speculative activity than inverse perpetuals.
- On Bybit, the overall liquidation index for the direct contract is almost double the inverse.
- On Binance, the long liquidation index for the direct contract is about twice the inverse.
- On OKEx, the short liquidation index for the direct contract is about five times the inverse.
- On Bybit, the overall aggressiveness index for the direct contract is about double the inverse; on Binance nearly three times; on OKEx over six times.
- Interpretation: speculators prefer USDT collateral; bitcoin holders who hedge prefer BTC-margined inverse products. Tether growth appears driven by speculative trading rather than hedging.

### Section 3.1: P&L on direct and inverse futures (p. 7)

- **Standard futures:** notional 1 BTC, price in USD; P&L is `F_t2 - F_t1`, settled in USD.
- **Direct perpetual:** notional 1 BTC, price in USDT; P&L is `F_t2 - F_t1`, settled in USDT.
- **Inverse perpetual:** notional USD 1, price in USD; P&L is `1/F_t1 - 1/F_t2`, settled in BTC.

The paper defines the inverse notional and its difference:

```
Fhat_t = 1 / F_t
Delta_n Fhat_t = Fhat_t - Fhat_{t+n Delta t}
R_Fhat_{t,n} = Delta_n Fhat_t / Fhat_t
```

This sign convention makes an increase in the inverse futures price a positive P&L for a long inverse position, consistent with standard and direct futures.

### Section 3.2: Optimal hedging problem (pp. 8-10)

A representative hedger holds 1 BTC and shorts `theta` perpetual contracts for `N` periods of length `Delta t`. The hedge is static over each horizon and rebalanced across horizons.

Hedged P&L in USD:

```
P&L = Delta_N S_t - theta Delta_N F_t                         (direct)
P&L = Delta_N S_t - theta Delta_N Fhat_t * S_{t+N Delta t}    (inverse)
```

The USDT price is assumed to be 1 USD for comparison. The paper notes that only a known or modelable USDT/USD rate is required; it need not be exactly 1.

Minimum-variance objective:

```
sigma_h^2(theta) = Var((Delta_N S_t - theta Delta_N F_t) / S_t)              (direct)
sigma_h^2(theta) = Var((Delta_N S_t - theta Delta_N Fhat_t * S_{t+N Delta t}) / S_t)  (inverse)
```

Liquidation condition for direct perpetuals:

```
Delta_N F_t > (m / theta) F_t - m_0 F_{t+N Delta t}
iff R_F_{t,N} = Delta_N F_t / F_t > (m / theta - m_0) / (1 + m_0)
```

Liquidation condition for inverse perpetuals:

```
Delta_N Fhat_t > m / theta - m_0 Fhat_{t+N Delta t}
iff R_Fhat_{t,N} = Delta_N Fhat_t / Fhat_t > (m / theta_hat - m_0) / (1 - m_0)
```

where `theta_hat = theta / F_t`.

Extreme returns over the hedge horizon:

```
Rbar_S_t    = max_{1 <= n <= N} R_S_{t,n}
Rbar_F_t    = max_{1 <= n <= N} R_F_{t,n}
Rbar_Fhat_t = max_{1 <= n <= N} R_Fhat_{t,n}
```

Liquidation probability:

```
P(m, theta) = Prob(Rbar_F_t > (m/theta - m_0)/(1 + m_0))          (direct)
P(m, theta) = Prob(Rbar_Fhat_t > (m/theta_hat - m_0)/(1 - m_0))  (inverse)
```

The dual-objective problem:

```
min_{theta > 0} [ sigma_h^2(theta) + gamma sigma_S^2 P(m, theta) ]
```

where `gamma > 0` is the loss-aversion or aggregation parameter and `sigma_S^2` is the variance of the N-period spot return. The variance term penalises both favourable and unfavourable deviations; the liquidation term only penalises the right tail (losses on the short futures position).

Key comparative statics:

- `P(m, theta)` decreases in collateral `m` and increases in position `theta`.
- `m = +infinity` or `gamma = 0` recovers the classical minimum-variance hedge.
- `theta = 0` avoids liquidation but provides no hedge.
- The paper argues that a mean-variance or utility objective that includes liquidation probability would be mathematically problematic and likely intractable, which is why it uses this additive dual objective.

### Theorem 3.2: Semi-closed-form optimal strategy (p. 10)

```
theta* = omega * theta_0
```

with `omega = 1` for direct perpetuals and `omega = F_t` for inverse perpetuals. `theta_0` is the positive root of:

```
a(x) x^(-2) + x - b = 0
```

where:

```
a(x) = gamma * nu * (mhat^2 / alpha) * exp(-kappa(x)^(-1/tau)) * kappa(x)^(-1/tau - 1)
kappa(x) = 1 + (tau / alpha) * (mhat * x^(-1) - mhat_0 - beta)
b = sigma_SF^2 / sigma_F^2
nu = sigma_S^2 / sigma_F^2
mhat = m / (1 + omega_0 m_0)
mhat_0 = m_0 / (1 + omega_0 m_0)
omega_0 = 1 (direct), -1 (inverse)
```

Here `sigma_S^2` and `sigma_F^2` are the variances of the N-period spot and direct futures returns, `sigma_SF^2` is their covariance, and `alpha`, `beta`, `tau` are the scale, location, and tail index of the right tail of the extreme futures return `Rbar_F` or `Rbar_Fhat`, modelled with a generalised extreme value (GEV) distribution.

The `gamma = 0` or `m = infinity` limit:

```
theta_tilde* = omega * b = omega * rho_SF * (sigma_S / sigma_F)
```

This is the classical minimum-variance ratio, scaled by `F_t` for inverse contracts. The paper stresses that omitting the `F_t` factor for inverse perpetuals can be catastrophic because bitcoin trades in five digits.

The `a(x)` term is the nonlinear correction from the margin constraint and liquidation loss aversion. It is not a simple haircut; it depends on the tail index and on the ratio of collateral to maintenance margin.

### Section 4.1: Parameter estimation (pp. 10-11)

- Return frequencies: 1 minute, 1 hour, 1 day.
- Spot/futures return means are near zero; standard deviations are large; kurtosis is extremely high.
- Extreme spot return in an 8-hour window on Coinbase reaches 19.5%.
- Variances and covariance are estimated with a 4-month rolling window of one-minute data.
- GEV parameters require long samples. Direct perpetual data start in July 2020, so the paper uses Coinbase spot tail estimates as a proxy for direct perpetuals and BitMEX inverse tail estimates as a proxy for other inverse perpetuals.
- Justification: estimated tail index `tau` for Coinbase spot is close to Binance and OKEx direct perpetuals, and close to Bybit. BitMEX inverse `tau` is close to other inverse perpetuals. Correlations of estimated `tau` are 0.95-0.98 for direct and 0.96-0.98 for inverse.
- Inverse perpetual tail indices are lower than direct ones; direct tails are closest to spot tails.
- GEV parameters are estimated with a three-year rolling window (1095 days). Results are robust to window length.
- At each time `t`, all parameters are estimated and `theta*_t` is computed, then applied from `t` to `t + N Delta t`. The window rolls forward by `N` periods.

### Section 4.2: Optimal strategy (p. 12)

- `theta_0` lies in `[0, 1]`, making inverse and direct positions comparable.
- `theta_0` decreases as the hedge horizon increases, because liquidation risk rises with horizon and the optimal position shrinks.
- For a fixed horizon, `theta_0` is fairly stable over time and marginally lower for direct perpetuals.
- Deribit's `theta_0` is noticeably lower than the other inverse perpetuals, likely because its returns are less correlated with Coinbase spot.

### Section 4.3: Hedge effectiveness (pp. 12-14)

Hedge effectiveness:

```
HE(theta) = 1 - Var(R(theta)) / Var(R(0)),   theta > 0
```

where `R(0)` is the unhedged spot return. The paper evaluates `m in {10%, 20%, 50%}`, `gamma in {10, 20, 40}`, and horizons `{8h, 1d, 5d}`.

Key results:

- Margin constraint matters. At `m = 50%`, HE is above 95% in most cases and close to 100% in some. At `m = 10%`, HE is still good if `gamma` is low and the horizon is short, but deteriorates sharply otherwise.
- Worst case (`m = 10%`, `gamma = 40`, 5 days) HE is only about 20% for inverse perpetuals hedging Coinbase spot.
- To achieve 99% HE with high loss aversion (`gamma = 40`) and a five-day horizon, the hedger should allocate about 50% of the initial futures contract value to margin.
- Loss aversion matters most when margin is tight. At `m = 10%`, 1-day direct hedging gives HE above 80% with `gamma = 10` but below 40% with `gamma = 40`.
- At `m = 50%`, the impact of `gamma` is small because liquidation probability is already low.
- Inverse perpetuals generally have slightly higher HE than direct, but the difference is not large. Example: `m = 20%`, `gamma = 20`, 1 day, BitMEX inverse 93.3% versus Binance direct 90.1%.
- Best inverse venue is BitMEX; best direct venue is Binance; the superiority is marginal.
- Deribit's inverse perpetual is the worst hedge for Coinbase spot despite being the least speculative product.
- HE falls with horizon, especially when margin is tight or loss aversion is high. At `m = 10%`, `gamma = 40`, HE more than halves when moving from 1 day to 5 days. The drop is largest for the most speculative products (Binance and OKEx direct perpetuals).

### Section 4.4: Liquidation probability under the optimal strategy (pp. 14-15)

Using the GEV tail:

```
P(m, theta*) ~= 1 - exp[ -(1 + (tau/alpha)(mhat/theta* - mhat_0 - beta))^(-1/tau) ]
```

Findings:

- `P(m, theta*)` is generally small: below 1% when `m = 50%`, below 3% when `m = 20%` or `10%`. The table spans about 0.1-3%.
- This holds across margin, loss aversion, horizon, and instrument.
- Margin has a large effect for low loss aversion. With `gamma = 10`, 8 hours, Binance inverse, raising `m` from 10% to 50% cuts liquidation probability from 1.5% to 0.1%.
- The effect is smaller for high loss aversion: with `gamma = 40` and 8 hours, `P` is already 0.7% even at `m = 10%`.
- Increasing `m` has two opposing effects: it directly lowers liquidation probability for a fixed position, but it also lets the hedger take a larger position to reduce variance. So the net effect is not monotone at longer horizons.
- Higher `gamma` lowers liquidation probability. Example: Bybit inverse, 8 hours, `m = 10%`; `P` falls from 1.5% to 0.6% as `gamma` rises from 10 to 40.
- For 8-hour and 1-day horizons, direct and inverse liquidation probabilities are very close. At 5 days, inverse outperforms direct. Deribit has the lowest liquidation probability among inverse products, the opposite of its HE ranking. Binance, Bybit, and OKEx direct products are almost identical.

### Section 4.5: Optimal implied leverage (p. 15)

```
Implied leverage = theta* / (F_t * m)   (inverse)
Implied leverage = theta* / m           (direct)
```

Findings:

- Optimal implied leverage ranges from about 1x to 8x, far below the 100x-125x available.
- BitMEX inverse maximum is 100x, but optimal leverage is below 5x when `m >= 20%` and below 2x when `m = 50%`.
- Implied leverage decreases in margin `m` and loss aversion `gamma`.
- At `m = 50%`, implied leverage is below 2x in all cases and stable across `gamma` and venues.
- Deribit option delta hedgers take the least leverage.
- Implied leverage decreases as the hedge horizon increases, because both variance and liquidation probability rise, so the hedger shrinks the position.

### Section 5: Summary and conclusions (p. 15)

- Standard hedging results do not apply to crypto because of contract design, fragmented venues, self-selected leverage, automatic liquidation, and loss aversion.
- The paper provides a new theory and a semi-closed-form solution.
- It introduces speculation metrics that account for aggressive liquidation.
- Empirically, ignoring margin and liquidation creates a "very wrong" impression of hedging ability.
- At short horizons, all products except Deribit inverse are similar. Deribit is least speculative but not the most effective hedge; it is the least effective.
- Inverse perpetuals outperform direct as the horizon lengthens, despite direct tails being more correlated with spot, because direct products attract more speculation and liquidation risk.
- Following the optimal strategy cuts liquidation probability to below 1% in most scenarios and keeps leverage mostly below 5x.
- The conclusion is nuanced: high leverage improves liquidity and market depth but also creates mass-liquidation risk.

### Appendix and online appendix (not fully retrieved)

- The main PDF references an online appendix with:
  - GEV distribution details (equation A.2).
  - Proof of Theorem 3.2.
  - Additional tables: summary statistics by frequency, historical liquidation simulation (Table C.1), sensitivity analysis (Fig. D.1), tail-index correlations (Table E.4).
- The main text gives enough to reconstruct the model and the empirical design, but the exact proof and some robustness tables are not in the retrieved file.

## Equation ledger

| # | Object | Expression / definition | Role |
|---|---|---|---|
| 1 | Speculative index | `SI = Volume / OI` | Traditional speculation measure |
| 2 | Liquidation indices | `LIQ_short = ShortLiq/OI`, `LIQ_long = LongLiq/OI`, `LIQ = TotalLiq/OI` | Liquidation intensity |
| 3 | Leverage-weighted liquidation | `LEV = sum_j LiqVol(j) lambda(j) / TotalLiq` | Average leverage of liquidated positions |
| 4 | Aggressiveness index | `AI = sum_j LiqVol(j) lambda(j) / OI` | Leverage-weighted liquidation relative to OI |
| 5 | Returns | `Delta_n S_t = S_{t+nDt} - S_t`, `R^S_{t,n} = Delta_n S_t / S_t`; same for `F` | Discrete returns |
| 6 | Inverse notional | `Fhat_t = 1/F_t`, `Delta_n Fhat_t = Fhat_t - Fhat_{t+nDt}`, `R^Fhat_{t,n} = Delta_n Fhat_t / Fhat_t` | Inverse P&L |
| 7 | Hedged P&L | direct: `Delta_N S - theta Delta_N F`; inverse: `Delta_N S - theta Delta_N Fhat * S_{t+NDt}` | Portfolio P&L |
| 8 | Variance objective | `sigma_h^2(theta) = Var(P&L / S_t)` | First objective |
| 9 | Direct liquidation condition | `R^F_{t,N} > (m/theta - m_0)/(1 + m_0)` | Liquidation trigger |
| 10 | Inverse liquidation condition | `R^Fhat_{t,N} > (m/theta_hat - m_0)/(1 - m_0)` | Liquidation trigger |
| 11 | Extreme returns | `Rbar = max_{1<=n<=N} R_{t,n}` | Horizon tail risk |
| 12 | Liquidation probability | `P(m,theta) = Prob(Rbar > threshold)` | Second objective |
| 13 | Dual objective | `min_{theta>0} [sigma_h^2(theta) + gamma sigma_S^2 P(m,theta)]` | Optimisation |
| 14 | Optimal hedge | `theta* = omega theta_0`, `omega = 1` direct, `F_t` inverse | Main result |
| 15 | Root equation | `a(x)x^{-2} + x - b = 0`; definitions of `a`, `kappa`, `b`, `nu`, `mhat`, `mhat_0`, `omega_0` | Semi-closed form |
| 16 | Classical limit | `theta~* = omega b = omega rho_SF sigma_S / sigma_F` | `gamma=0` or `m=inf` |
| 17 | Hedge effectiveness | `HE = 1 - Var(R(theta))/Var(R(0))` | Performance metric |
| 18 | Optimal liquidation probability | `P(m,theta*) ~= 1 - exp[-(1 + (tau/alpha)(mhat/theta* - mhat_0 - beta))^{-1/tau}]` | GEV approximation |
| 19 | Implied leverage | inverse `theta*/(F_t m)`; direct `theta*/m` | Leverage diagnostic |

## Paper-specific findings

### What is genuinely new

1. **Liquidation is a first-class hedging constraint, not a footnote.** Classical minimum-variance hedging assumes the hedge survives. This paper treats survival as a second objective and shows that ignoring it overstates hedge effectiveness.
2. **Self-selected leverage is an endogenous choice.** The hedger chooses `m` and `theta`; the exchange chooses `m_0`. The optimal solution depends on all three.
3. **The inverse contract P&L changes the hedge ratio by a factor `F_t`.** A naive minimum-variance hedge on inverse perpetuals can be off by orders of magnitude.
4. **New speculation metrics.** `LIQ`, `LEV`, and `AI` capture leverage-weighted liquidation pressure, which `SI` misses.
5. **Direct versus inverse is not just a contract detail.** Direct USDT-margined perpetuals have much higher speculative activity and liquidation risk; inverse BTC-margined perpetuals are better for longer-horizon hedges.

### The central trade-off

The hedger wants a large short futures position to reduce variance. But a large short position raises the probability of a liquidation, which leaves the spot unhedged at exactly the worst time. The optimal hedge is the point where the marginal variance reduction equals the marginal cost of liquidation risk, weighted by `gamma`.

The GEV tail matters because liquidation is a tail event. The tail index `tau` controls how heavy the right tail is; a heavier tail makes liquidation more likely and pushes the optimal position down.

### Numerical anchors

| Quantity | Result |
|---|---|
| Global liquidations in 2021 | ~USD 80bn; >USD 200m/day average |
| 19 May 2021 liquidations | USD 8.6bn across products; USD 339m in sample |
| 100x, 1-day liquidation probability | >95% |
| 5x short, 15-day liquidation probability | >10% average |
| `m = 50%` hedge effectiveness | >95% most cases, near 100% some |
| `m = 10%`, `gamma = 40`, 5-day HE | ~20% worst case |
| `m = 20%`, `gamma = 20`, 1-day HE | BitMEX inverse 93.3%; Binance direct 90.1% |
| Optimal liquidation probability | 0.1-3%; <1% at `m = 50%` |
| Optimal implied leverage | 1x-8x; <5x at `m >= 20%`; <2x at `m = 50%` |
| Deribit speculation | Lowest `LIQ`, `LEV`, `AI`; worst HE |
| Direct vs inverse speculation | Direct `AI` 25.78-36.37%; inverse 2.01-12.41% (6.00-12.41% excluding Deribit) |

### Why inverse beats direct at longer horizons

Direct perpetual tails are more correlated with Coinbase spot tails, which should make direct a better hedge on paper. But direct products are much more speculative and have higher liquidation indices. A loss-averse hedger internalises that liquidation risk and shrinks the direct hedge more. At longer horizons, the liquidation penalty dominates the correlation advantage. This is a nice example of market microstructure changing an ostensibly pure statistical hedge.

### What the paper does not claim

- It does not claim the strategy is risk-free or that it dominates all alternatives.
- It does not model funding payments explicitly in the P&L; it argues they are small and often offset.
- It does not model auto-deleveraging, insurance funds, clawbacks, or exchange insolvency.
- It does not model dynamic collateral top-ups or re-entry after liquidation.
- It does not claim the results hold after 2021 or on DEXs.
- It does not claim inverse products are always better; the advantage is horizon-dependent and modest at short horizons.

## Limitations and caveats

1. **Sample period.** Data end on 31 May 2021. Crypto market structure, leverage limits, funding rules, and liquidation engines have changed substantially since then. The 2022 bear market, the FTX collapse, and the growth of DEX perpetuals are not in the sample.
2. **Single spot venue.** Coinbase is the spot leg. Hedging results may differ for other spot venues or for a basket of venues.
3. **Static hedge within a horizon.** The hedge is not continuously rebalanced. The paper models a sequence of static hedges and recommends frequent rebalancing, but the optimal stopping/rebalancing policy is not solved.
4. **No funding payments in the P&L.** Funding is argued to be small and mean-reverting. In stress periods, funding can be large and can interact with liquidation.
5. **USDT is treated as USD.** The paper says a known USDT/USD rate would suffice, but the empirical implementation assumes 1:1. A depeg would change direct-contract P&L and liquidation.
6. **GEV tail estimation.** Tail parameters are estimated from finite samples and proxied across venues. Tail index uncertainty is not fully propagated into the optimal position or the liquidation probability.
7. **Maintenance margin is treated as a constant `m_0`.** Real exchanges use tiered maintenance margins that depend on position size and may change.
8. **Liquidation data quality.** The paper itself documents Binance misreporting in May 2021. Exchange-reported liquidation data can be incomplete or manipulated.
9. **No auto-deleveraging or socialised losses.** When the insurance fund is depleted, exchanges may auto-deleverage profitable positions. That is an additional risk for a hedger.
10. **No counterparty or custody risk.** The hedger's collateral sits on a centralised exchange. Exchange failure is not modelled.
11. **No transaction costs, spreads, or slippage.** These are likely small relative to the liquidation effect but matter for high-frequency rebalancing.
12. **One asset.** The empirical work is bitcoin only. The model should generalise, but the parameters and tail behaviour will differ for other assets.
13. **Table inconsistency.** The text describes horizons `{8h, 1d, 5d}`, and Table 4 columns are labelled `8h, 1d, 5d`, but the table note says `{8h, 1d, 2d}`. Treat the horizon labels with care when reproducing numbers.
14. **Online appendix not retrieved.** The proof of Theorem 3.2 and some robustness tables are in the online appendix. The main text gives the result and intuition but not the full derivation.
15. **Optimality is within a restricted class.** The strategy is static within each horizon and constrained to a short futures position. A dynamic strategy with collateral injections, partial liquidation, or options could do better.

## Hackathon prep for this paper

### What this paper supports building

1. **A liquidation-aware hedge optimiser.** Given spot exposure, venue, contract type, collateral, maintenance margin, loss aversion, and horizon, compute the optimal hedge ratio, liquidation probability, and implied leverage.
2. **A margin and liquidation oracle.** Publish a survival probability or liquidation score for a given position, not just a spot price.
3. **A perp risk simulator.** Stress-test funding, margin, liquidation cascades, and exchange/product choice on historical minute data.
4. **A collateral autopilot.** Recommend top-ups, deleveraging, or position reductions before liquidation. This can be advisory in the hackathon and autonomous only with strict guardrails.
5. **A venue/product router.** Choose direct versus inverse, and choose the exchange, based on hedge effectiveness, liquidation risk, and speculation metrics.
6. **An inverse-contract accounting library.** Handle the `1/F` P&L, the `F_t` scaling, and BTC-margined collateral correctly.
7. **A liquidation-aware vault or structured product.** A vault that hedges a crypto treasury and explicitly targets a liquidation probability budget.

### What this paper does not support building directly

- A general payoff compiler. That is Paper 1.
- A no-arbitrage pricing anchor for arbitrary funding rules. That is Paper 2.
- A cross-venue basis signal. That is Paper 2.
- A prediction-market perp or RWA perp. The paper is bitcoin-specific.
- A production trading system. The model is a research prototype.

### Data and tech needed

- Minute-level spot and perp OHLC data from multiple venues.
- Funding rates, mark prices, index prices, and contract specifications.
- Maintenance margin tiers and liquidation rules per venue.
- Open interest and liquidation feeds.
- GEV estimation (Python: `scipy.stats.genextreme`, `pyextremes`, or a custom MLE).
- Rolling-window covariance estimation.
- Solidity or Rust contracts for an onchain oracle/vault.
- A backtesting engine with realistic liquidation logic.
- A frontend for scenario analysis and heatmaps.

### Demo script (3 minutes)

**Act 1: The hidden risk.** Show a treasury with 100 BTC and a naive 1:1 short hedge at 20x leverage. Run a historical 20% crash. The hedge is liquidated before the crash bottom; the treasury is naked. Show the liquidation timestamp and the loss.

**Act 2: The optimiser.** Input collateral `m`, loss aversion `gamma`, horizon, venue, and contract type. The optimiser outputs `theta*`, liquidation probability `P`, implied leverage, and expected HE. Show how the position shrinks as `gamma` or the horizon rises. Show direct versus inverse and venue choice.

**Act 3: The oracle and autopilot.** Publish the survival score onchain. A lending protocol or vault consumes it. Simulate a margin top-up or a partial deleverage before the liquidation threshold. End with the portfolio surviving the same crash.

### Judge Q&A prep

**"Isn't this just a stop-loss?"** No. A stop-loss is a reactive order that may slip or fail in a crash. This is an ex ante position size that internalises the probability of forced liquidation. It also chooses collateral and contract type.

**"Why not just use 1x leverage?"** At 1x, liquidation risk is low but capital efficiency is poor. The model finds the efficient frontier between variance reduction and liquidation risk for a given loss aversion.

**"How do you estimate the tail?"** GEV on rolling windows of extreme returns, with cross-venue proxies when history is short. We show the sensitivity and avoid claiming precision.

**"What happens if the exchange changes margin rules?"** The oracle consumes the current maintenance margin and tier. A rule change updates the recommended position. Governance and monitoring are required.

**"Is this safe onchain?"** The hackathon version is advisory plus a capped vault. Autonomous execution requires multisig, spend limits, circuit breakers, and an audited margin engine.

**"Why inverse perpetuals?"** They are BTC-margined, which matches a bitcoin holder's collateral, and they have lower speculative/liquidation intensity. But the advantage is horizon-dependent.

### Claims to avoid

- "Risk-free hedging."
- "Guaranteed liquidation prevention."
- "The optimal strategy for all market conditions."
- "Backtested returns" unless the backtest includes funding, fees, slippage, and realistic liquidation.
- "Works on any asset" without re-estimating tails and margin rules.
- "Exchange liquidation data is accurate."

### Track and sponsor fit

- **DeFi/derivatives track:** liquidation-aware margin engine, perp risk oracle.
- **Risk/security track:** liquidation cascade simulation, exchange risk dashboard.
- **Oracle track:** survival-probability oracle, margin oracle.
- **AI track:** an agentic risk copilot that explains the hedge and proposes actions.
- **Ethereum track:** restaking/validator treasury hedging, DAO treasury hedging.
- **Institutional/RWA track:** bitcoin treasury risk management for funds and miners.

## Hackathon-level idea for this paper

### LiquidMesh: a liquidation-aware hedge autopilot and margin oracle

**One sentence.** LiquidMesh is the risk layer that tells any bitcoin holder, vault, or perp DEX how much to hedge, how much collateral to post, and how likely the hedge is to survive, using the dual-objective model from Alexander, Deng, and Zou.

**Why it is stronger than a dashboard.** A dashboard shows liquidation levels. LiquidMesh computes the optimal position and publishes a forward-looking survival probability that other protocols can consume. It is an oracle plus an execution policy, not a chart.

**Core modules.**

1. **Position intake.** Spot exposure, venue, contract type, collateral, maintenance margin tier, horizon, loss aversion.
2. **Tail engine.** Rolling GEV estimates for spot and futures extreme returns, with cross-venue proxies and uncertainty bands.
3. **Optimiser.** Solve the `theta*` root equation and return hedge ratio, liquidation probability, implied leverage, and HE.
4. **Venue router.** Compare direct versus inverse and across exchanges using HE, liquidation probability, speculation metrics, and liquidity.
5. **Survival oracle.** Publish a signed survival score and recommended collateral buffer onchain.
6. **Autopilot.** Advisory alerts, then guarded execution: top up collateral, reduce position, or rotate venue.
7. **Scenario lab.** Historical and synthetic stress tests: 19 May 2021, a 20% intraday crash, a funding spike, an exchange freeze.

**Onchain architecture.**

- `LiquidationOracle.sol`: consumes signed risk reports, stores survival scores and parameter hashes.
- `HedgeVault.sol`: holds collateral, executes a capped hedge, enforces a liquidation-probability budget.
- `MarginPolicy.sol`: encodes maintenance tiers and venue rules.
- `RiskRegistry.sol`: maps assets, venues, and contract types to model parameters.
- Off-chain keeper: computes GEV tails and the optimal root, signs reports, updates the oracle.
- Consumer adapters: lending protocol, DAO treasury, restaking vault, perp DEX.

**Demo moment.** A DAO treasury with 100 BTC inputs `m = 20%`, `gamma = 20`, 1-day horizon. LiquidMesh recommends a BitMEX inverse hedge, shows 93% HE and 0.5% liquidation probability, and publishes the survival score. A 20% crash hits. The naive 1:1 hedge is liquidated; LiquidMesh's position survives and the treasury is hedged. Then the autopilot tops up margin when the score crosses a threshold.

**Moat.** The calibrated tail and liquidation model, the venue-specific margin registry, the survival oracle integrations, and the execution track record.

**Risks.** Data quality, exchange rule changes, model risk, custody, and regulatory classification. Keep the hackathon version testnet-only and advisory-first.

**Integration with the existing portfolio.** LiquidMesh is the missing risk layer for Perp Forge and Basis Mesh. Forge launches the perp; Basis Mesh prices the basis; LiquidMesh ensures the hedge survives. Together they form a full stack: **design, price, and survive**.

**Score.** Novelty 4, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 28.

## Connection to the other papers

- **Paper 1 (Angeris et al.):** provides the general funding and replication language. LiquidMesh is a risk overlay for any perp, not just linear bitcoin futures.
- **Paper 2 (He et al.):** provides the no-arbitrage anchor and basis signals. LiquidMesh adds the liquidation and margin dimension that Paper 2 bounds but does not optimise.
- **Paper 4 (Zhang):** explains why the same funding template produces different effective feedback across assets. LiquidMesh's survival score should consume the effective funding regime, because weak feedback and persistent basis gaps change the liquidation path.

**Full ideation:** See [06-hackathon-idea-lab.md](06-hackathon-idea-lab.md), especially the new Wave 2 sections on LiquidMesh, Funding DNA, and the unified Perp Forge risk stack.
