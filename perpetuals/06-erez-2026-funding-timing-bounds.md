# Funding Timing and No-Arbitrage Bounds in Decentralized Perpetual Markets

## Metadata

- **Authors:** Erce Erez and Mikhail Smirnov (Department of Mathematics, Columbia University)
- **Working paper:** SSRN Electronic Journal, May 20, 2026
- **SSRN abstract ID:** 6805838
- **DOI:** 10.2139/ssrn.6805838
- **Length:** 47 pages, 22 tables, 9 figures
- **JEL classification:** G12; G13; G14; G23
- **Keywords:** decentralized finance; perpetual futures; funding rates; no-arbitrage bounds; oracle pricing; market design
- **Type:** Theory plus empirical market microstructure. No-arbitrage bounds, protocol-specific funding mechanics, high-frequency on-chain data, out-of-sample band tests, reduced-form reversion diagnostics.
- **Data:** Drift Protocol BTC/ETH/SOL perpetuals, 52,307 hourly settlement records, 1 January 2023 to 31 December 2024; dYdX v4 BTC/ETH/SOL perpetuals, 57,330 hourly funding records, 26 October 2023 to 30 December 2025.
- **Read status:** Full paper read end to end, including all propositions, appendices, tables, figures, robustness checks, and references.

## One-paragraph summary

The paper asks when a no-arbitrage bound for a decentralized perpetual future is actually testable. The answer depends on **funding timing**: whether the next funding transfer is known at trade entry. If funding is observable at entry, realized funding can enter a one-period no-arbitrage bound and tighten it even when the protocol's funding cap does not bind. If funding is forward-looking, only a guaranteed floor or cap known at entry can enter the bound, so the theory has empirical content only in a clamped regime. The authors build a generic one-period bound and apply it to two leading on-chain venues. Drift Protocol computes funding from entry-time EMA-TWAP state, so its funding is observable and its bounds are informative: using 2024 out-of-sample data, BTC, ETH, and SOL have out-of-band rates of 3-5%, short mean durations of 5-6 hours, and mean exceedances of 9-10 basis points. dYdX v4 computes funding from subsequent premium samples, and its margin-based caps are so wide that they never bind: across 2025, the maximum realized hourly funding rate is below 9% of the cap, and even a tenfold tighter cap produces zero clamped observations. The dYdX bound is therefore valid in theory but empirically uninformative in the sample. The broader message is that funding timing and cap calibration are core protocol design variables, not implementation details: they determine whether no-arbitrage restrictions can be tested, and whether a protocol can credibly commit to tight perp-oracle alignment.

## Section-by-section findings

### Abstract and Introduction (pp. 1-3)

- Perpetual futures do not expire. The link between the perp price and the underlying is maintained by recurring funding transfers, not by convergence at maturity.
- A no-arbitrage portfolio can use funding only to the extent that the funding transfer is guaranteed when the position is opened.
- Funding timing is therefore a protocol-design variable that determines whether a theoretical bound can be taken to the data.
- Perpetuals are central to crypto derivatives:
  - In Q1 2024, Bitcoin perpetuals on the largest CEX averaged about USD 57.7 billion daily notional, roughly 10% of CME E-mini S&P 500 futures volume.
  - As of 8 May 2026, Hyperliquid processed about USD 8.6 billion daily notional.
  - Drift and dYdX v4 intermediated about USD 370 billion cumulative notional on their BTC, ETH, and SOL markets over the analyzed windows.
- Benchmark: Dai, Li, and Yang (2025), who derive model-free one-period no-arbitrage bounds for centralized perpetuals under a clamped funding rule.
- The paper extends that framework to oracle-based decentralized venues.
- Two cases:
  - **Observable funding (Drift):** funding is computed from entry-time EMA-TWAP mark and oracle state, so the next payment is fixed when the arbitrage portfolio is formed. Realized funding can enter the bound.
  - **Forward-looking funding (dYdX v4):** funding is computed from premium samples that arrive after entry. Only a guaranteed cap or floor known at entry can enter the bound.
- Empirical contrast:
  - Drift: cap never reached, but realized funding is known at entry; out-of-band rates 3-5%; short-lived deviations that mean revert.
  - dYdX: margin-based caps so wide that the clamped regime is never reached; the bound is valid but empirically uninformative.
- Contributions:
  1. Identify funding timing as the design variable that determines testability.
  2. Extend the centralized no-arbitrage framework to oracle-based DeFi venues.
  3. Provide comparative evidence from Drift and dYdX.
- Related literature: Ackerer, Hugonnier, and Jermann (2025); He, Manela, Ross, and von Wachter (2022); Dai, Li, and Yang (2025); Chen, Ma, and Nie (2024); Kim and Park (2025); Werapun et al. (2025); MEV and oracle-latency literature (Daian et al., Qin et al., Angeris and Chitra, Mackinga et al.).
- The paper explicitly does not model MEV or execution-layer effects. The bounds describe idealized arbitrage portfolios; the empirical tests are evidence on band exits and reversion, not executable trading rents.

### Section 2: Drift Protocol institutional background (pp. 3-5)

- Drift is a decentralized perpetual futures exchange on Solana.
- Funding updates are scheduled hourly but performed lazily and may be delayed in low-activity periods.
- Key state variables are recorded on-chain and exposed via historical repositories, enabling direct empirical validation.
- Sample window: January 2023 to December 2024.
- The protocol suffered a security incident on 1 April 2026 and announced a relaunch with revised settlement infrastructure. The sample ends before the incident, so the empirical analysis is unaffected.

#### 2.1 EMA-TWAPs

Let `t_k` be update times and `Delta t_k = t_k - t_{k-1}`. Bars denote EMA-based TWAPs.

```
Xbar_{t_k} = alpha_k X_{t_k} + (1 - alpha_k) Xbar_{t_{k-1}},   0 < alpha_k <= 1
alpha_k = 1 - exp(-Delta t_k / tau),   tau = 3600 seconds
```

for `X in {B, A, I}` (best bid, best ask, oracle price).

Mark TWAP:

```
M_{t_k} = (Abar_{t_k} + Bbar_{t_k}) / 2
```

Funding pushes `M` toward `Ibar`:

| Condition | Market statement | Funding sign | Payment direction |
|---|---|---|---|
| `M_t > I_t` | Perpetual rich | `f_t > 0` | Longs pay shorts |
| `M_t < I_t` | Perpetual cheap | `f_t < 0` | Shorts pay longs |
| `M_t = I_t` | No basis | `f_t = 0` | No transfer |

#### 2.2 Funding rate

Clamp:

```
clamp(x, -eta, eta) =
  eta,    x >= eta
  x,      -eta < x < eta
  -eta,   x <= -eta
```

Pre-clamp hourly funding fraction:

```
f_pre_{t_k} = (1/24) * (M_{t_k} - Ibar_{t_k} + |Ibar_{t_k}|/5000) / Ibar_{t_k}
```

- `1/24` converts the premium to an hourly rate.
- `|Ibar|/5000` is an implementation offset equal to 0.02% of the oracle price.
- In the on-chain implementation, the cap is applied to the adjusted spread in price units before normalizing by `Ibar`.

Adjusted spread:

```
S_{t_k} = M_{t_k} - Ibar_{t_k} + |Ibar_{t_k}|/5000
f_pre_{t_k} = (1/24) * S_{t_k} / Ibar_{t_k}
```

Tier-dependent cap:

```
eta_{t_k}(tier) = (1/24) * S_max(tier, Ibar_{t_k}) / Ibar_{t_k}
f_{t_k} = clamp(f_pre_{t_k}, -eta_{t_k}, eta_{t_k})
```

Equivalently:

```
f_{t_k} = (1/24) * clamp(S_{t_k}, -S_max(tier, Ibar_{t_k}), S_max(tier, Ibar_{t_k})) / Ibar_{t_k}
```

Implied hourly caps in the data:

```
eta(tier) =
  0.00125,   tier in {B or greater}
  0.00208,   tier = C
  0.004167,  tier < C
```

These are 0.125%, 0.208%, and 0.4167% per hour.

**Storage format.** On chain, funding is stored as a scaled quote-per-base quantity. Let `Iraw` be the raw integer oracle TWAP; the economic oracle TWAP is `Ibar = Iraw / 10^6`. Drift's precision convention gives:

```
fundingRate_stored = f_{t_k} * 10^9 * Ibar_{t_k} = f_{t_k} * Iraw_{t_k} * 10^3
f_{t_k} = fundingRate_stored / (10^9 * Ibar_{t_k})
```

For a position of `q` base units, the hourly funding cash flow in USD is:

```
FundingPaymentUSD_{t_k} = q * Ibar_{t_k} * f_{t_k}
```

### Section 3: dYdX v4 institutional background (pp. 6-7)

- dYdX v4 is a standalone Cosmos SDK blockchain powering a decentralized perpetual exchange.
- Funding uses impact bid/ask prices from the order book, validator votes aggregated into minute samples, and an hourly average.
- Exact reconstruction is possible from on-chain minute premium samples; approximate reconstruction is possible from public order-book snapshots.

#### 3.1 Margin requirements and impact notional

Let `IMF` and `MMF` be the initial and maintenance margin fractions, with `0 < MMF < IMF < 1`.

```
Q = 500 USDC / IMF
```

The impact notional is tied to the market's risk parameters: higher leverage (lower `IMF`) implies a larger impact notional.

#### 3.2 Impact prices

For ask levels `{(a_i(t), u_i(t))}` and a market buy spending `Q`:

```
x_i(t) = min(u_i(t), (Q - sum_{j<i} a_j(t) x_j(t))^+ / a_i(t))
X_buy(t) = sum_i x_i(t)
A_imp_t = Q / X_buy(t)
```

The impact bid `B_imp_t` is symmetric from a market sell targeting quote proceeds `Q`.

#### 3.3 Premium index

```
pi_t = (max{0, B_imp_t - I_t} - max{0, I_t - A_imp_t}) / I_t
```

- If `B_imp_t <= I_t <= A_imp_t`, then `pi_t = 0`.
- The zero-premium region reflects execution costs inherent in the order book.
- Minute sample: median across validator votes `pi^sam_{h,j} = median_l pi^vote_{h,j,l}`.
- Hourly premium: simple average of 60 minute samples `pibar_h = (1/60) sum_{j=1}^{60} pi^sam_{h,j}`.

#### 3.4 Funding rate and caps

For cross-margin markets, the hourly interest-rate component `r_int_h = 0`.

```
f_8h(h) = pibar_h + 8 r_int_h
f_h = (1/8) pibar_h + r_int_h
eta_8h = 6 (IMF - MMF)
f^cap_8h(h) = clamp(f_8h(h), -eta_8h, eta_8h)
f^cap_h = (1/8) f^cap_8h(h)
```

For BTC-USD in February 2026: `IMF = 2.0%`, `MMF = 1.2%`, so `eta_8h = 4.80%` and `eta_1h = 0.60%` (60 bps per hour). For SOL-USD: `IMF = 5.0%`, `MMF = 3.0%`, so `eta_8h = 12.0%` and `eta_1h = 1.50%` (150 bps per hour).

### Section 4: Comparison of funding mechanisms (p. 8)

| Feature | Drift | dYdX v4 |
|---|---|---|
| Chain | Solana | Cosmos (dYdX Chain) |
| Price anchor | Oracle (Pyth) | Index price |
| Premium | Adjusted mark-oracle premium `S/Ibar` | Impact-price premium `pi` |
| Aggregation | Mark and oracle EMA-TWAPs | Median validator votes; 60-minute average |
| Funding | `f = clamp((1/24) S/Ibar, -eta, eta)` | `f^cap_h = (1/8) clamp(pibar + 8 r_int, -eta_8h, eta_8h)` |
| Cap | Tier-dependent hourly | `eta_8h = 6(IMF - MMF)` |
| Offset | `+|Ibar|/5000` | None |
| Interest | None | `r_int` zero for cross-margin |

The design choices determine which funding terms are guaranteed at entry and therefore which protocol-specific bound has empirical content.

### Section 5: General one-period funding-timing bound (pp. 8-9)

Setup: settlement interval `[t_k, t_{k+1}]`; perp entry price `P_k`; reference price `I_k > 0`; residual basis bound:

```
|P_{k+1} - I_{k+1}| <= Gamma
```

Proportional entry costs `kappa_F` and `kappa_S`; cash rate `r`; spot borrow rate `r_C`. Denominators are well defined.

**Definition 5.1 (Guaranteed funding income).**

- `g^-_k >= 0`: funding income per dollar of reference value guaranteed at `t_k` to a long perp over `[t_k, t_{k+1}]`.
- `g^+_k >= 0`: the corresponding guaranteed income to a short perp.
- Guaranteed cash flows: `g^-_k I_k` and `g^+_k I_k`.

**Theorem 5.2 (Generic funding-timing bound).** Under the assumptions above, no-arbitrage implies:

```
P_k / I_k >= L_k(g^-_k) := 1/(1 + kappa_F(1+r)) * [ (1 - kappa_S)(1+r)/(1+r_C) + g^-_k - Gamma/I_k ]
P_k / I_k <= U_k(g^+_k) := 1/(1 - kappa_F(1+r)) * [ (1 + kappa_S)(1+r)/(1+r_C) - g^+_k + Gamma/I_k ]
```

If both `g^-` and `g^+` are guaranteed, `P_k/I_k` lies in `[L_k, U_k]`.

Proof sketch:

- Lower bound: long perp, short `1/(1+r_C)` spot, invest residual at `r`. Worst-case payoff uses `P_{k+1} - I_{k+1} >= -Gamma`; non-positive worst-case payoff gives the bound.
- Upper bound: mirror portfolio: short perp, buy spot, borrow at `r_C`; uses `P_{k+1} - I_{k+1} <= Gamma`.

**Remark 5.3.** In an observable-funding design, `g^-` or `g^+` can be the realized next-period transfer. In a forward-looking design, only a path-independent floor, cap, or guaranteed component can enter. Drift and dYdX differ exactly here.

### Section 6: Arbitrage bounds for Drift (pp. 9-12)

Notation: `P` tradeable perp mid; `I` oracle price; `Ibar` oracle EMA-TWAP; `M` mark TWAP; `f` realized hourly funding; `eta` tier cap; `rho = Ibar/I`; `epsilon` net EMA tracking error; `r`, `r_C`; `kappa_F`, `kappa_S`; `LDr`, `UDr`.

**Definition 6.1 (Funding fraction and cash flow).**

```
f_{t_k} = clamp((1/24) S_{t_k}/Ibar_{t_k}, -eta_{t_k}, eta_{t_k})
S_{t_k} = M_{t_k} - Ibar_{t_k} + |Ibar_{t_k}|/5000
```

For 1 unit of base asset, the funding cash flow is `f_{t_k} Ibar_{t_k}`.

**Definition 6.2 (Net EMA tracking error).**

```
epsilon_k = (M_{t_k} - P_{t_k}) - (Ibar_{t_k} - I_{t_k})
```

**Lemma 6.3 (Funding spread decomposition).**

```
S_{t_k} = (P_{t_k} - I_{t_k}) + epsilon_k + rho_k I_{t_k}/5000
(1/24) S_{t_k}/Ibar_{t_k} = (1/(24 rho_k I_{t_k})) [ (P_{t_k} - I_{t_k}) + epsilon_k + rho_k I_{t_k}/5000 ]
```

**Definition 6.4 (Clamped regimes).**

Lower clamped regime:

```
(P_{t_k} - I_{t_k})/I_{t_k} <= -24 eta_{t_k} rho_k - rho_k/5000 - epsilon_k/I_{t_k}
```

equivalently `f = -eta`.

Upper clamped regime:

```
(P_{t_k} - I_{t_k})/I_{t_k} >= 24 eta_{t_k} rho_k - rho_k/5000 - epsilon_k/I_{t_k}
```

equivalently `f = +eta`.

**Proposition 6.5 (Conditional no-arbitrage bounds).** Under Assumptions B.1-B.8:

```
LDr_{t_k} = 1/(1 + kappa_F(1+r)) * [ (1 - kappa_S)(1+r)/(1+r_C) + eta_{t_k} rho_k - Gamma/I_{t_k} ]
UDr_{t_k} = 1/(1 - kappa_F(1+r)) * [ (1 + kappa_S)(1+r)/(1+r_C) - eta_{t_k} rho_k + Gamma/I_{t_k} ]
```

In the lower clamped regime, `P/I >= LDr`. In the upper clamped regime, `P/I <= UDr`.

**Corollary 6.6 (Observable-funding extension outside the cap).** Because `f` is determined entirely by EMA-TWAPs recorded at `t_k`, any nonzero realized funding transfer can enter Theorem 5.2. If `f < 0`, the long-perp portfolio receives `(-f) Ibar`:

```
P/I >= 1/(1 + kappa_F(1+r)) * [ (1 - kappa_S)(1+r)/(1+r_C) + (-f) rho_k - Gamma/I ]
```

If `f > 0`, the short-perp portfolio receives `f Ibar`:

```
P/I <= 1/(1 - kappa_F(1+r)) * [ (1 + kappa_S)(1+r)/(1+r_C) - f rho_k + Gamma/I ]
```

The clamped bounds are the special cases `|f| = eta`.

**Remark 6.7.** `Gamma` is not just an auxiliary device. Because funding is fixed at entry but the exit price is realized later, the residual basis `P_{k+1} - I_{k+1}` does not cancel. A single-period argument must either bound it or use a longer horizon over which cumulative funding dominates any finite basis drift.

**Band structure.** The interval is non-empty if `LDr <= UDr`. With zero fees/rates and `rho = 1`:

```
Gamma >= eta_{t_k} I_{t_k}
```

The one-period absolute basis must be at least as large as the hourly funding cap in dollar terms. For liquid assets this holds.

Band width:

```
UDr - LDr = [ 2 Gamma/I - 2 eta rho + 2 kappa_S(1+r)/(1+r_C) + 2 kappa_F(1+r)^2/(1+r_C) ] / [ 1 - (kappa_F(1+r))^2 ]
```

The band widens with `Gamma` and narrows with `eta`. Higher caps tighten the entry threshold.

Fee-free, zero-rate limit with `rho = 1`:

```
1 + eta - Gamma/I <= P/I <= 1 - eta + Gamma/I
```

The band is centered at oracle parity with half-width `Gamma/I - eta`. It collapses to `P = I` when `Gamma = eta I`, and is dominated by basis risk when `Gamma >> eta I`.

### Section 7: Arbitrage bounds for dYdX v4 (pp. 13-16)

Notation: `P` impact midpoint `(B_imp + A_imp)/2`; `I` index; `pi^sam` minute sample; `N_k` number of samples (nominally 60, stochastic); `pibar` sample average; `f` hourly funding; `eta_8h = 6(IMF - MMF)`; `eta_1h = eta_8h/8`; `epsilon^dY` premium proxy error; `r`, `r_C`, `kappa_F`, `kappa_S`, `Gamma`.

**Definition 7.1 (Minute premium sample).**

```
pi_t = (max{0, B_imp_t - I_t} - max{0, I_t - A_imp_t}) / I_t
pi^sam_{k,j} = median_l pi^vote_{k,j,l}
```

The premium is zero when the index lies inside the impact spread.

**Definition 7.2 (Funding fraction).**

For cross-margin, `r_int = 0`:

```
f_8h,k = clamp(pibar_k, -eta_8h, eta_8h)
f_k = (1/8) f_8h,k = clamp(pibar_k/8, -eta_1h, eta_1h)
```

For a long position of one base unit, the hourly funding transfer is modeled as `f_k I_{t_k}`, using the entry-time oracle price as a tractable settlement convention.

**Definition 7.3 (Contemporaneous premium proxy and error).**

```
P_{t_k} = (B_imp_{t_k} + A_imp_{t_k})/2
tilde_pi_k = (P_{t_k} - I_{t_k})/I_{t_k}
epsilon^dY_k = pibar_k - tilde_pi_k
```

Note `tilde_pi_k != pi_{t_k}` in general: when the index is inside the impact spread, `pi = 0` but `tilde_pi` may be nonzero. `epsilon^dY` absorbs within-period drift, the impact-midpoint vs protocol-premium gap, and validator-median noise.

**Lemma 7.4 (Premium decomposition).**

```
pibar_k = (P_{t_k} - I_{t_k})/I_{t_k} + epsilon^dY_k
pibar_k/8 = (P_{t_k} - I_{t_k})/(8 I_{t_k}) + epsilon^dY_k/8
```

**Definition 7.6 (dYdX clamped regimes).**

Lower:

```
pibar_k <= -eta_8h  <=>  f_k = -eta_1h
(P_{t_k} - I_{t_k})/I_{t_k} <= -eta_8h - epsilon^dY_k
```

Upper:

```
pibar_k >= +eta_8h  <=>  f_k = +eta_1h
(P_{t_k} - I_{t_k})/I_{t_k} >= +eta_8h - epsilon^dY_k
```

These identify clamped funding ex post. They satisfy the entry-time premise only when the cap or floor is path-independent or otherwise guaranteed at `t_k`.

**Proposition 7.7 (Conditional no-arbitrage bounds for dYdX).** Under Assumptions B.9, B.10, B.5, B.6, B.7, and conditional on the relevant clamped transfer being guaranteed at `t_k`:

```
LdY_k = 1/(1 + kappa_F(1+r)) * [ (1 - kappa_S)(1+r)/(1+r_C) + eta_1h - Gamma/I_{t_k} ]
UdY_k = 1/(1 - kappa_F(1+r)) * [ (1 + kappa_S)(1+r)/(1+r_C) - eta_1h + Gamma/I_{t_k} ]
```

If the lower clamped transfer is guaranteed, `P/I >= LdY`. If the upper clamped transfer is guaranteed, `P/I <= UdY`.

**Remark 7.8.** Because dYdX funding is computed from samples after entry, an ex post clamped rate is not an entry-time guarantee. The proposition applies only when the clamped transfer is known or guaranteed at entry. In the sample, the cap is never reached even ex post.

**Comparison.** With zero fees/rates, both bound systems reduce to:

```
1 + eta - Gamma/I <= P/I <= 1 - eta + Gamma/I
```

with `eta = eta_{t_k} rho_k` for Drift and `eta = eta_1h` for dYdX. The band width formula is the same with `eta` replaced. The protocols differ only in how the effective cap is set: a tier table for Drift versus `eta_1h = (3/4)(IMF - MMF)` for dYdX.

### Section 8: Drift data analysis (pp. 17-29)

#### 8.1 Data

- Three Drift markets: BTC-PERP, ETH-PERP, SOL-PERP.
- 1 January 2023 to 31 December 2024.
- 52,307 hourly settlement records:
  - BTC-PERP: 17,417.
  - ETH-PERP: 17,396.
  - SOL-PERP: 17,494.
- All three markets are Tier B throughout the sample, so `eta = 0.00125` per hour (12.5 bps).
- Train/test split: 2023 training (25,987 records); 2024 held-out test (26,320 records). `Gamma` is estimated only on 2023 and fixed for 2024.

#### 8.2 Empirical implementation

- Theoretical ratio is `P/I`; the archive observes mark TWAP `M` and oracle TWAP `Ibar`, not a synchronous tradeable mid.
- Empirical proxy: `r_{t_k} = M_{t_k}/Ibar_{t_k}`.
- `rho_k = 1` in the empirical implementation because the archive lacks a synchronous spot oracle.
- `P/I` vs `M/Ibar` proxy error is bounded in Proposition 8.2.

**Proposition 8.2 (Empirical proxy inheritance).** With `R_k = P/I`, `Remp_k = M/Ibar`, `rho_k = Ibar/I`:

```
L_k - Delta_k <= Remp_k <= U_k + Delta_k
Delta_k = |(1/rho_k - 1) R_k + (rho_k - 1)/rho_k + epsilon_k/(rho_k I_k)|
Delta_k <= |rho_k - 1|/rho_k * |R_k - 1| + |epsilon_k|/(rho_k I_k)
```

If `rho = 1`, the `rho` component is zero; the remaining unobserved component is the normalized EMA tracking term. This is a data limitation, not a change in the theory.

**Empirical bounds.** Set `r = r_C = 0`, `kappa_F = 0` (maker rebate assumption), retain `kappa_S = 1.8` bps:

```
Lemp = 1 - kappa_S + |f| rho - Gamma/Ibar
Uemp = 1 + kappa_S - |f| rho + Gamma/Ibar
```

Because `f > 0` when the upper bound is relevant and `f < 0` when the lower bound is relevant, `|f|` enters symmetrically.

**Regime coverage.** In 2024, the realized hourly funding never reaches the Tier B cap:
- Max `|f|`: BTC 3.30 bps; ETH 2.67 bps; SOL 4.54 bps.
- Cap: 12.5 bps.
- The test covers only the unclamped regime. The bounds remain valid via Corollary 6.6.
- Since `|f| < eta`, the realized bounds are wider than the clamped limiting case, so out-of-band rates are conservative.

**Gamma calibration.** `Gamma` is the 99th percentile of `|M - Ibar|` on 2023, held fixed for 2024:

| Market | Gamma (USD) | Gamma / mean Ibar (%) |
|---|---:|---:|
| BTC-PERP | 176.29 | 0.611 |
| ETH-PERP | 7.82 | 0.435 |
| SOL-PERP | 0.42 | 1.449 |

`Gamma` is the empirically load-bearing parameter: a larger `Gamma` widens the band and lowers the out-of-band rate; a smaller `Gamma` does the opposite. The resulting interval is a high-probability empirical band, not a literal sample no-arbitrage region.

#### 8.3 Test 1: Core out-of-band rates

| Market | Obs | Exits | Exit rate | Wilson 95% CI | Mean mag. (bps) | Mean dur. (h) | Mean band (bps) |
|---|---:|---:|---:|---|---:|---:|---:|
| BTC-PERP | 8,774 | 399 | 4.55% | [4.13, 5.00]% | 10.0 | 6.2 | 58.9 |
| ETH-PERP | 8,773 | 282 | 3.21% | [2.87, 3.60]% | 9.7 | 5.6 | 55.9 |
| SOL-PERP | 8,773 | 440 | 5.02% | [4.58, 5.49]% | 9.2 | 4.9 | 60.8 |

- Out-of-band rate 3-5%.
- Mean exceedance 9-10 bps.
- Mean duration 5-6 hours.
- Bands are dynamic and narrow over 2024 as the oracle price rises (because `Gamma/I` shrinks).

**Proxy-error threshold diagnostic.** The distance `m_k` from the proxy to the nearest band edge:

```
m_k = (Lemp - Remp)^+ + (Remp - Uemp)^+
```

The true ratio could be inside the band only if the proxy error `Delta_k >= m_k`. Since `Delta_k` is not observed, the table reports how large it would need to be to reclassify exits:

| Market | Exits | Median m_k (bps) | Mean (bps) | 95th pct (bps) | Max (bps) |
|---|---:|---:|---:|---:|---:|
| BTC-PERP | 399 | 6.4 | 10.0 | 31.4 | 50.7 |
| ETH-PERP | 282 | 6.8 | 9.7 | 28.3 | 42.7 |
| SOL-PERP | 440 | 6.9 | 9.2 | 25.6 | 68.2 |

#### 8.4 Out-of-band exit characteristics

- Mean exceedance 9-10 bps; right tail extends to 50-70 bps.
- Most exits are within Drift's taker fee of 4-9 bps, suggesting liquidity-cost effects rather than model failure.
- Mean durations: BTC 6.2h, ETH 5.6h, SOL 4.9h.
- Modal duration: 1 hour for SOL and ETH; 2-3 hours for BTC.
- Fewer than 5% of episodes persist beyond 15 hours.
- SOL has a higher exit rate (5.02%) but shorter duration (4.9h) than BTC (4.55%, 6.2h), consistent with SOL's larger normalized `Gamma` (1.45% vs 0.61%): a wider band admits more but shorter-lived exceedances.

#### 8.5 Band width dynamics

- Bands narrow over 2024 as the oracle price rises; `Gamma` is fixed in USD, so `Gamma/I` shrinks.
- This tightens the bounds in bull markets and loosens them in bear markets.
- August and November 2024 widenings correspond to sharp intra-hour volatility.

#### 8.6 Robustness

**Gamma sensitivity.**

| Gamma pct | Market | Gamma (USD) | Out-of-band rate |
|---|---|---:|---:|
| 99th | BTC | 176.29 | 4.55% |
| 99th | ETH | 7.82 | 3.21% |
| 99th | SOL | 0.42 | 5.02% |
| 95th | BTC | 88.48 | 14.47% |
| 95th | ETH | 4.57 | 9.96% |
| 95th | SOL | 0.11 | 23.38% |
| 90th | BTC | 51.86 | 24.71% |
| 90th | ETH | 2.85 | 19.35% |
| 90th | SOL | 0.07 | 32.06% |

The 99th percentile is the widest-band, most conservative baseline. Tighter `Gamma` sharply increases exits.

**Fee sensitivity.**

| Fee assumption | Market | kappa_S (bps) | Out-of-band rate |
|---|---|---:|---:|
| Baseline | BTC | 1.8 | 4.55% |
| Baseline | ETH | 1.8 | 3.21% |
| Baseline | SOL | 1.8 | 5.02% |
| Conservative | BTC | 4.5 | 3.42% |
| Conservative | ETH | 4.5 | 2.37% |
| Conservative | SOL | 4.5 | 3.83% |

**Rolling Gamma.**

| Gamma spec | BTC | ETH | SOL |
|---|---:|---:|---:|
| Static 2023 | 4.55% | 3.21% | 5.02% |
| Expanding window | 1.79% | 1.86% | 1.87% |
| Trailing 365 days | 1.44% | 1.72% | 1.39% |

All specifications produce positive, economically meaningful out-of-band rates. The static split is the cleanest.

**Within-test stability.**

| Market | H1 exits | H1 rate | H2 exits | H2 rate |
|---|---:|---:|---:|---:|
| BTC | 330 | 7.56% | 69 | 1.56% |
| ETH | 258 | 5.91% | 24 | 0.54% |
| SOL | 313 | 7.17% | 127 | 2.88% |

Exits concentrate in H1, especially the March-April 2024 momentum episode. H2 rates remain positive in all markets.

#### 8.7 Reduced-form reversion test

- Enter at each band exit: long ratio if below lower band, short if above upper band.
- Exit when `|r - 1| < 0.0010` (within 10 bps of parity) or after 48 settlement periods.
- Price-only PnL; frictionless `kappa_S = 0` and net `kappa_S = 1.8` bps one-way (3.6 bps round trip).
- This is a diagnostic, not an executable arbitrage backtest. It omits margin, liquidation, borrow, impact, priority fees, MEV, and capital.

| Scenario | Market | Trades | Win rate | Mean PnL (bps) | Total PnL (bps) | Sharpe |
|---|---|---:|---:|---:|---:|---:|
| Frictionless | BTC | 30 | 86.7% | +15.75 | +472.4 | 1.12 |
| Frictionless | ETH | 32 | 93.8% | +16.80 | +537.6 | 1.76 |
| Frictionless | SOL | 43 | 93.0% | +21.44 | +922.1 | 0.99 |
| Net 3.6 bps | BTC | 30 | 83.3% | +12.15 | +364.4 | 0.86 |
| Net 3.6 bps | ETH | 32 | 90.6% | +13.20 | +422.4 | 1.38 |
| Net 3.6 bps | SOL | 43 | 93.0% | +17.84 | +767.3 | 0.82 |

- Mean duration / neutral exits / forced exits: BTC 26.1h / 70% / 30%; ETH 16.9h / 91% / 9%; SOL 21.2h / 84% / 16%.
- Forced exits at the 48-period cap, mainly March-April 2024, drive most price-only losses.
- The 70-91% neutral exits are profitable in most cases, consistent with fast reversion when it occurs.
- The point is not scalable profit; it is that band exits contain directional information about subsequent mark-oracle reversion.

### Section 9: dYdX v4 data analysis (pp. 29-36)

#### 9.1 Data

- BTC-USD, ETH-USD, SOL-USD on dYdX v4.
- 26 October 2023 to 30 December 2025.
- 57,330 hourly funding records (19,110 per market).
- Training: October 2023 to December 2024; test: 2025.
- Caps:
  - BTC/ETH: `IMF = 2.0%`, `MMF = 1.2%`, `eta_8h = 4.8%`, `eta_1h = 60` bps.
  - SOL: `IMF = 5.0%`, `MMF = 3.0%`, `eta_8h = 12.0%`, `eta_1h = 150` bps.
- Caps are 5-12x wider than Drift's Tier B cap.
- Margin parameters are retrieved after the sample; this affects exact cap levels but not the conclusion.

#### 9.2 Empirical implementation

- `P` proxied by hourly candle close at settlement; `I` is index price.
- Candle close is noisier than Drift's mark TWAP; ratio SDs are 5-10x larger.
- 1,297 of 57,330 records (2.3%) have no candle match within 1 hour; excluded from ratio statistics. In the 2025 test window, all funding records have a matched candle.
- Bounds with `r = r_C = 0`, `kappa_F = 0`, `kappa_S = 10` bps:

```
LdY = 1 - kappa_S + eta_1h - Gamma/I
UdY = 1 + kappa_S - eta_1h + Gamma/I
```

- `Gamma` is the 99th percentile of `|P_{k+1} - I_{k+1}|` on the training window:

| Market | Gamma (USD) | Gamma / I_train (%) |
|---|---:|---:|
| BTC-USD | 1,393.31 | 2.255 |
| ETH-USD | 74.49 | 2.570 |
| SOL-USD | 5.34 | 3.775 |

- Normalized `Gamma` is 3-4x larger than Drift's, reflecting the noisier candle-close proxy.

#### 9.3 Test 1: Clamped-regime coverage

| Market | Obs | Clamped obs | Testable exits | Exit rate | Mean band (bps) |
|---|---:|---:|---:|---|---:|
| BTC-USD | 8,736 | 0 | 0 | N/A | 177.9 |
| ETH-USD | 8,736 | 0 | 0 | N/A | 430.4 |
| SOL-USD | 8,736 | 0 | 0 | N/A | 374.6 |

- Max realized hourly funding: BTC 5.38 bps (8.96% of 60 bps cap); ETH 2.17 bps (3.62%); SOL 3.41 bps (2.27%).
- The cap never binds.
- The clamped-regime precondition for Proposition 7.7 is never satisfied, so there are zero testable exits.
- Cap-tightening robustness:

| Counterfactual cap | BTC | ETH | SOL |
|---|---|---|---|
| Baseline | 8.96% (0) | 3.62% (0) | 2.27% (0) |
| 2x tighter | 17.92% (0) | 7.23% (0) | 4.54% (0) |
| 5x tighter | 44.80% (0) | 18.08% (0) | 11.35% (0) |
| 10x tighter | 89.60% (0) | 36.17% (0) | 22.71% (0) |

Even a tenfold tighter cap produces zero clamped observations.

#### 9.4 Unclamped basis dislocations

| Market | Dislocations | % of obs | Max downside (bps) | Max upside (bps) |
|---|---:|---:|---:|---:|
| BTC-USD | 503 | 5.8% | 678.8 | 579.2 |
| ETH-USD | 288 | 3.3% | 982.5 | 892.2 |
| SOL-USD | 659 | 7.5% | 846.6 | 948.5 |

- These are ratio exits while funding is not at the cap. They are not tests of Proposition 7.7; the clamped-regime precondition fails.
- The most prominent episode is February-March 2025: ETH ratio 0.8804, SOL 0.9306, while realized hourly funding stayed far below the caps.
- The conclusion does not depend on whether the measured dislocations are inflated by candle-close noise: even a fraction of the measured basis would leave funding below the cap.

#### 9.5 Comparison with Drift

- Both propositions are applications of the same generic bound.
- Drift can use realized entry-time funding; dYdX can use only the cap/floor in clamped regimes.
- Drift's Tier B cap (12.5 bps/h) is small enough that sub-cap realized funding makes a meaningful contribution to the unclamped bound.
- dYdX's caps (60-150 bps/h) are never reached; the bound is valid but empirically uninformative.
- The structural difference is not whether the cap binds (neither does), but whether funding is observable at entry.
- Design implication: a protocol seeking verifiably tight no-arbitrage bounds should decouple its funding cap from its margin parameters.

### Section 10: Conclusion (pp. 36-37)

- The empirical content of no-arbitrage bounds for DeFi perpetuals depends on funding timing.
- The central object is not only the size of the funding cap, but the amount of funding income an arbitrageur can guarantee at entry.
- Drift: funding observable at entry; cap not reached; out-of-band rates 3-5%; short durations; reversion diagnostic positive after costs.
- dYdX: forward-looking funding; caps too wide to bind; zero clamped observations; bound theoretically valid but empirically uninformative.
- Protocol design implications: funding timing and cap calibration are core choices, not secondary implementation parameters.
- Limitations: oracle reference prices, no margin/liquidation/market impact/priority fees/MEV, candle-close proxy on dYdX, post-sample margin parameters.
- Future work:
  1. Derive `Gamma` from an ergodic multi-period argument rather than calibrating it.
  2. Resolve the stochasticity of `N_k` on dYdX.
  3. Extend to lower-liquidity markets, isolated-margin contracts, and other on-chain venues.
  4. Test the tier-dependent cap predictions using Tier C and below.
- Data and code: public Drift archive and dYdX v4 indexer; replication code at `https://github.com/ee2625/defi-perps`.

### Appendices A-E (pp. 38-46)

**Appendix A: Strategy robustness and funding-augmented diagnostic.**

- Adds realized funding to the price-only reversion test.
- Funding-augmented net:

| Market | Trades | Price-only net (bps) | Funding increment (bps) | With-funding net (bps) | Win rate |
|---|---:|---:|---:|---:|---:|
| BTC | 30 | +364.4 | +984.2 | +1,348.6 | 100% |
| ETH | 32 | +422.4 | +605.6 | +1,028.0 | 100% |
| SOL | 43 | +767.3 | +1,046.1 | +1,813.4 | 100% |

- Bootstrap 95% intervals (1,000 resamples):

| Market | Total PnL (bps) | Mean PnL (bps) | Sharpe |
|---|---|---|---|
| BTC | +1,349 [1,107, 1,587] | +44.9 [36.9, 52.9] | 1.92 [1.55, 2.54] |
| ETH | +1,028 [830, 1,253] | +32.1 [25.9, 39.1] | 1.68 [1.40, 2.26] |
| SOL | +1,813 [1,522, 2,177] | +42.2 [35.4, 50.6] | 1.60 [1.37, 2.11] |

- Random-entry placebo: actual +1,349/+1,028/+1,813 vs placebo means -110/-116/-150 bps, p < 0.001.
- Funding-magnitude-matched placebo: actual +1,349/+1,028/+1,813 vs matched +1,269/+981/+1,471; p = 0.180, 0.263, 0.001. The strongest evidence beyond funding magnitude is cross-sectional (SOL), not uniform.
- The 100% win rate should not be read as frictionless arbitrage: entries occur in mechanically favorable funding states and the test omits execution/balance-sheet frictions.

**Appendix B: Assumptions.**

- B.1 funding settlement regularity (approximately hourly, no skipped settlements).
- B.2 remark on lazy settlement.
- B.3 bilateral funding symmetry (`f_long = f_short`).
- B.4 remark on rebate-pool shortfall and asymmetry term.
- B.5 oracle faithfulness (trade at oracle price with proportional fee).
- B.6 proportional transaction costs (entry only; exit fee-free).
- B.7 no-arbitrage definition.
- B.8 bounded one-period residual basis for Drift.
- B.9 dYdX funding-period regularity (`N_k >= 1`).
- B.10 bounded residual basis for dYdX.
- B.11 calibrating `Gamma`: (a) `24 eta Ibar` (circular), (b) 99th percentile (used), (c) multi-period/ergodic (future work).

**Appendix C: EMA tracking error bound.**

- One-step contraction and iterated bound.
- Steady-state `|epsilon_k| <= 2 delta / alpha_min`.
- For Drift `tau = 3600s`, `Delta t = 3600s`, `alpha = 1 - e^-1 ~= 0.632`, so `|epsilon| <~ 3.16 delta`.
- Tracking error is small when settlement-interval price increments are small, independent of within-hour updates.

**Appendix D: Derivation of Drift clamped-regime conditions.**

- Starting from `f_pre = (1/24)(S/Ibar)`, the lower/upper clamp conditions reduce to the expressions in Definition 6.4.
- The `rho/5000` offset appears with the same sign in both conditions due to the asymmetric `+|Ibar|/5000` offset.

**Appendix E: dYdX premium proxy error bound.**

- Decomposes `epsilon^dY` into within-period drift `D_k` and protocol-to-proxy discrepancy `Xi_k`.
- Worst-case `|D_k| <= delta_pi_k`.
- Under stationarity, the averaging component is `O_p(N_k^-1/2)`, but the entry-time proxy contributes an `O(1)` term.
- Protocol-to-proxy discrepancy includes impact-vs-midpoint gap and validator-median noise.
- Combined worst-case `|epsilon^dY| <= delta_pi + xi_max`.
- Probabilistic bound: `SD <= sqrt((sigma_D^2 + sigma_xi^2)/N_k)` under independence; without independence, `SD <= sigma_D + sigma_xi/sqrt(N_k)`.
- Comparison with Drift: Drift's EMA error does not shrink with within-period updates; dYdX's sample average does, giving dYdX an identification advantage in volatile periods.

### References (pp. 46-47)

Dai, Li, Yang (2025); Ackerer, Hugonnier, Jermann (2025); He, Manela, Ross, von Wachter (2022); Chen, Ma, Nie (2024); Kim and Park (2025); Werapun et al. (2025); Daian et al. (2020); Qin, Zhou, Gervais (2022); Angeris and Chitra (2020); Mackinga, Nadahalli, Wattenhofer (2022); Drift Protocol docs/source; dYdX docs.

## Equation ledger

| # | Object | Expression | Role |
|---|---|---|---|
| 1 | Drift EMA-TWAP | `Xbar_k = alpha_k X_k + (1-alpha_k) Xbar_{k-1}`, `alpha_k = 1 - exp(-Delta t_k/tau)` | Oracle/mark smoothing |
| 2 | Mark TWAP | `M_k = (Abar_k + Bbar_k)/2` | Drift mark |
| 3 | Clamp | `clamp(x, -eta, eta)` | Funding cap |
| 4 | Drift pre-clamp funding | `f_pre = (1/24)(M - Ibar + |Ibar|/5000)/Ibar` | Funding rule |
| 5 | Drift adjusted spread | `S = M - Ibar + |Ibar|/5000` | Pre-clamp spread |
| 6 | Drift cap | `eta(tier) = (1/24) S_max(tier, Ibar)/Ibar` | Tier cap |
| 7 | Drift funding | `f = clamp(f_pre, -eta, eta)` | Realized funding |
| 8 | Drift storage | `fundingRate_stored = f * 10^9 * Ibar` | On-chain representation |
| 9 | Drift cash flow | `q * Ibar * f` | Funding payment |
| 10 | dYdX impact notional | `Q = 500 USDC / IMF` | Impact size |
| 11 | dYdX premium | `pi_t = (max{0, B_imp - I} - max{0, I - A_imp})/I` | Premium index |
| 12 | dYdX hourly premium | `pibar_h = (1/60) sum_j pi^sam_{h,j}` | Funding input |
| 13 | dYdX funding | `f_8h = pibar + 8 r_int`, `f_h = (1/8)pibar + r_int` | Funding rule |
| 14 | dYdX cap | `eta_8h = 6(IMF - MMF)`, `eta_1h = eta_8h/8` | Cap |
| 15 | Residual basis | `|P_{k+1} - I_{k+1}| <= Gamma` | One-period bound |
| 16 | Guaranteed funding | `g^-`, `g^+` | Funding-timing input |
| 17 | Generic lower bound | `L_k = [ (1-kappa_S)(1+r)/(1+r_C) + g^- - Gamma/I ] / (1+kappa_F(1+r))` | Theorem 5.2 |
| 18 | Generic upper bound | `U_k = [ (1+kappa_S)(1+r)/(1+r_C) - g^+ + Gamma/I ] / (1-kappa_F(1+r))` | Theorem 5.2 |
| 19 | Drift tracking error | `epsilon = (M-P) - (Ibar-I)` | Proxy correction |
| 20 | Drift spread decomposition | `S = (P-I) + epsilon + rho I/5000` | Lemma 6.3 |
| 21 | Drift clamp thresholds | lower/upper in Definition 6.4 | Regime identification |
| 22 | Drift conditional bounds | `LDr`, `UDr` with `eta rho` | Proposition 6.5 |
| 23 | Drift outside-cap bounds | `LDr` with `(-f)rho`, `UDr` with `f rho` | Corollary 6.6 |
| 24 | Band width | formula in (37) | Width comparative statics |
| 25 | dYdX proxy | `tilde_pi = (P-I)/I` | Contemporaneous proxy |
| 26 | dYdX proxy error | `epsilon^dY = pibar - tilde_pi` | Definition 7.3 |
| 27 | dYdX decomposition | `pibar = (P-I)/I + epsilon^dY` | Lemma 7.4 |
| 28 | dYdX clamp thresholds | lower/upper in Definition 7.6 | Regime identification |
| 29 | dYdX conditional bounds | `LdY`, `UdY` with `eta_1h` | Proposition 7.7 |
| 30 | Empirical Drift bounds | `1 - kappa_S + |f|rho - Gamma/Ibar`; `1 + kappa_S - |f|rho + Gamma/Ibar` | Equation (60) |
| 31 | Empirical dYdX bounds | `1 - kappa_S + eta_1h - Gamma/I`; `1 + kappa_S - eta_1h + Gamma/I` | Equation (61) |
| 32 | Out-of-band rate | fraction of test observations with `r` outside band | Test 1 |
| 33 | Gamma calibration | 99th percentile of residual basis on training window | Empirical implementation |
| 34 | Proxy-error threshold | `m_k = (L-Remp)^+ + (Remp-U)^+` | Diagnostic |
| 35 | Reversion PnL | signed change in ratio from entry to exit | Reduced-form test |

## Paper-specific findings

### The central idea

**Funding timing determines testability.** A no-arbitrage bound is only as strong as the funding income the arbitrageur can guarantee at entry.

- If funding is **observable at entry**, the realized transfer can enter the bound. The bound can be informative even when the cap never binds.
- If funding is **forward-looking**, only a guaranteed floor or cap known at entry can enter. If the cap never binds, the bound is vacuous in practice.

This is a clean, general principle, not a protocol-specific quirk.

### The empirical contrast

| Feature | Drift | dYdX v4 |
|---|---|---|
| Funding timing | Observable at entry | Forward-looking |
| Cap | Tier B, 12.5 bps/h | 60-150 bps/h |
| Cap binding | Never | Never |
| Bound informative? | Yes, via realized funding | No, because cap never binds |
| Out-of-band rate | 3-5% | N/A (zero testable exits) |
| Mean exceedance | 9-10 bps | Unclamped dislocations only |
| Mean duration | 5-6 h | N/A |
| Reversion | Positive after costs (diagnostic) | Not testable |

The paradox is resolved: **the same theoretical bound can be informative on one venue and empty on another because of funding timing and cap design.**

### Why Drift is informative even without a binding cap

- Drift's funding is computed from EMA-TWAPs recorded at entry, so the next payment is known.
- Corollary 6.6 lets realized `|f|` replace the cap in the bound.
- Even though max `|f|` is only 2.67-4.54 bps versus a 12.5 bps cap, the realized funding still tightens the band enough to produce 3-5% out-of-band rates.
- The band exits are short-lived (5-6 hours) and mean revert; the reduced-form test is positive after 3.6 bps round-trip costs.

### Why dYdX is uninformative in this sample

- dYdX funding is a running average of post-entry premium samples; the next transfer is not known at entry.
- Only the cap or floor can be guaranteed, so Proposition 7.7 applies only in a clamped regime.
- The margin-based caps are 60-150 bps/h; the maximum realized hourly funding is below 9% of the cap.
- Even a tenfold tighter cap produces zero clamped observations.
- The bound is theoretically valid but empirically untestable in the sample. Large unclamped dislocations (ETH 0.8804, SOL 0.9306 in Feb-Mar 2025) do not imply arbitrage because the funding precondition fails.

### The design lesson

**Do not tie the funding cap to margin parameters if the goal is tight, testable perp-oracle alignment.** dYdX's `eta_8h = 6(IMF - MMF)` is calibrated to collateral requirements, producing caps that are too wide to ever bind. Drift's tier-based cap is calibrated to oracle tracking, producing tighter bounds and testable predictions. A protocol that wants verifiable no-arbitrage bounds should decouple the two.

### The reversion evidence

- Drift band exits carry directional information: price-only net PnL +364/+422/+767 bps across BTC/ETH/SOL, win rates 83-93%, per-trade Sharpe 0.82-1.38.
- Funding-augmented net PnL +1,349/+1,028/+1,813 bps, 100% win, but entries are mechanically favorable and the test omits frictions.
- The random-entry placebo is easy to beat (p < 0.001); the funding-magnitude-matched placebo is stricter and only SOL remains significant (p = 0.001). BTC and ETH are directionally stronger but not significant at 5%.
- The honest conclusion: band exits contain reversion information, but the evidence is not uniform and is not an executable arbitrage backtest.

### What the paper does not claim

- It does not claim that Drift band exits are scalable arbitrage profits.
- It does not claim that dYdX has no arbitrage; it claims the specific clamped-regime bound is untestable in the sample.
- It does not claim that `Gamma` is model-free; it is an empirical 99th percentile.
- It does not model MEV, oracle latency, market impact, margin, liquidation, or capital constraints.
- It does not claim the results generalize beyond BTC/ETH/SOL or beyond the sample windows.

## Critical review and limitations

### Theoretical limitations

1. **Single-period bound.** The theorem is one-period. A multi-period/ergodic argument could dispense with `Gamma`, but it is left for future work. The one-period bound is sensitive to the residual basis.
2. **`Gamma` calibration is empirical.** The 99th percentile is not a model-free almost-sure bound. Out-of-band rates are mechanically sensitive to `Gamma`: moving from 99th to 90th percentile roughly quintuples BTC exits and increases SOL exits sixfold. The headline 3-5% is a function of this choice.
3. **No-arbitrage definition assumes frictionless close.** Entry costs are modeled, exit costs are assumed zero. Adding exit costs would tighten the bounds and change the out-of-band rates.
4. **Funding cash-flow convention.** The dYdX bound models the hourly transfer as `f_k I_{t_k}` using the entry-time oracle price. This is a tractable convention, not a verbatim on-chain settlement rule.
5. **Bilateral funding symmetry.** Drift can have separate long/short funding rates when the rebate pool is insufficient. The asymmetry term is assumed away.
6. **Lazy settlement.** Drift's hourly updates can be delayed; Assumption B.1 rules this out. The paper notes it is empirically supported for liquid markets but not for illiquid ones.
7. **No MEV/oracle latency.** The paper explicitly abstracts from execution-layer effects. In practice, oracle latency and MEV can determine who captures a band exit.
8. **No margin/liquidation/borrow constraints.** The arbitrage portfolio is idealized. A real delta-neutral trade has two margin accounts, funding-timing risk, and liquidation risk.
9. **No market impact or capital constraints.** The bound assumes the arbitrageur can transact at the reference price in the required size.
10. **Single-asset, single-venue per protocol.** Only BTC/ETH/SOL on Drift and dYdX. Cross-sectional tier predictions (Tier C and below) are not tested.

### Empirical limitations

11. **Drift proxy error.** The archive lacks a synchronous tradeable mid and spot oracle, so the paper uses `M/Ibar` with `rho = 1`. The true `P/I` may differ; the proxy-error threshold diagnostic shows median distances of 6-7 bps and 95th percentiles of 26-31 bps, so some exits could be proxy artifacts.
12. **Drift sample is Tier B only.** All three markets are Tier B with a 12.5 bps cap. The cross-tier prediction is not tested.
13. **Drift sample ends in 2024.** The protocol had a security incident in April 2026 and relaunched; the sample predates it. Market structure may have changed.
14. **dYdX proxy is candle close.** It is not the impact midpoint and is 5-10x noisier than Drift's mark TWAP. This inflates `Gamma` and widens the bands, making the dYdX test conservative in the direction of no exits.
15. **dYdX margin parameters are post-sample.** The caps are computed from parameters retrieved in April 2026, not a historical time series. The paper argues this does not change the conclusion because even a tenfold tighter cap has zero clamped observations.
16. **dYdX has zero testable exits.** This is a strong negative result, but it is sample-specific. A future stress regime could bind the cap.
17. **Candle matching.** 1,297 of 57,330 dYdX records (2.3%) lack a candle match within one hour and are excluded from ratio statistics. In the 2025 test window all records match.
18. **Reversion test is not executable.** It omits margin, liquidation, borrow availability, market impact, priority fees, MEV, and capital usage. The funding-augmented 100% win rate is not evidence of riskless arbitrage.
19. **Placebo evidence is mixed.** Random-entry placebo is easy to beat; funding-magnitude-matched placebo only leaves SOL significant (p = 0.001), with BTC/ETH p = 0.18/0.26. The paper is appropriately cautious.
20. **Short sample for dYdX training.** October 2023 to December 2024 is about 15 months; regime coverage may be limited.
21. **No out-of-sample test of the dYdX cap-binding counterfactual.** The tenfold tightening is a counterfactual on the same sample, not a different regime.
22. **No transaction-cost model for closing.** The 3.6 bps round trip is a simple assumption; actual costs vary by venue, size, and market conditions.
23. **No cross-venue comparison with CEX.** The paper compares two DEXs; it does not include Binance/OKX funding or execution.
24. **No regulatory/tax treatment.** Not modeled.
25. **Minor internal inconsistency in the conclusion.** The conclusion says maximum realized `|f|` is 26-36% of `eta` across the three markets; based on the 2024 maxima in Section 8.2 (3.30, 2.67, 4.54 bps vs 12.5 bps), ETH is about 21%, not 26%. The range should be read as approximately 21-36%. This does not affect the main results.

### What the paper does not claim

- It does not claim that Drift is risk-free or that the reversion strategy is scalable.
- It does not claim that dYdX is inefficient; it claims the specific bound is untestable in the sample.
- It does not claim that `Gamma = 99th percentile` is the unique or correct calibration.
- It does not claim that the results hold for all markets or regimes.
- It does not claim that funding timing is the only determinant of testability; cap calibration and proxy quality also matter.

## Hackathon prep for this paper

### What this paper supports building

1. **A funding-timing oracle.** For each perp, classify funding as observable-at-entry vs forward-looking; publish a `FundingDeterminism` score and whether a no-arbitrage bound is testable.
2. **A bound engine.** Compute protocol-specific lower/upper bands, but only publish them when the guaranteed funding term is valid; otherwise emit a "vacuous" flag.
3. **A cap-binding monitor.** Track how close realized funding is to the cap; publish the probability that the clamped regime will bind in stress. This is the key diagnostic for dYdX-style protocols.
4. **A design auditor for perp DEXs.** Given a funding rule, prove whether its no-arbitrage bound is empirically testable; recommend decoupling the cap from margin parameters if it is not.
5. **A band-exit scanner.** Detect out-of-band observations and estimate reversion probability, with proxy-error and cost adjustments.
6. **A protocol comparison dashboard.** Drift vs dYdX side by side: funding timing, cap width, band width, out-of-band rate, clamp frequency, reversion.
7. **A funding-rule simulator.** Test alternative caps and aggregation rules; show how cap width and timing change testability.
8. **A research agent.** An AI copilot that explains why a band is informative or vacuous, and what design change would make it testable.

### What this paper does not support building directly

- A general payoff compiler. That is Paper 1.
- A cross-venue execution router. That is Paper 5.
- A liquidation-optimal hedge. That is Paper 3.
- A funding-feedback calibration. That is Paper 4.
- A production arbitrage system. The reversion test is a diagnostic, not an execution strategy.
- A claim that dYdX has free money. The paper's point is the opposite: the bound is vacuous when the cap never binds.

### Data and tech needed

- On-chain funding records: Drift `fundingRateHistory`, dYdX `/v4/historicalFunding`, premium samples, candles, market parameters.
- Contract specs: funding interval, cap, tier, margin parameters, oracle source.
- Synchronous perp mid and spot/oracle prices for a proper proxy-error analysis.
- Fee schedules and execution costs.
- A bound engine for protocol-specific formulas.
- A `Gamma` calibration module with train/test split and sensitivity analysis.
- A clamp-regime classifier.
- A band-exit detector and reversion diagnostic.
- An onchain oracle for `FundingDeterminism`, `BoundStatus`, `CapBindingRisk`, and `BandExit`.
- A frontend for cross-protocol comparison and rule design.

### Demo script (3 minutes)

**Act 1: The same theory, two outcomes.** Show the generic bound. Drift: funding is known at entry, the cap never binds, but the band still produces 3-5% out-of-band exits that revert. dYdX: funding is forward-looking, the cap never binds, and the bound has zero testable exits despite large unclamped dislocations. The judge sees that funding timing, not the formula alone, determines testability.

**Act 2: The oracle and auditor.** Clockwork classifies each perp, computes the band only when valid, and publishes a `FundingDeterminism` score and `CapBindingRisk`. The auditor flags dYdX's cap as structurally too wide and recommends decoupling it from margin parameters. Show the cap-tightening counterfactual.

**Act 3: The design fix.** Use the rule simulator to design a cap that binds in stress without destabilizing normal markets. Re-run the dYdX-style rule with the new cap; the clamped regime appears, the bound becomes testable, and the band-exit scanner produces signals. End with the platform vision: funding timing as a first-class design primitive.

### Judge Q&A prep

**"What is the core insight?"** Funding timing determines whether no-arbitrage bounds are testable. If funding is known at entry, realized funding enters the bound. If it is forward-looking, only a guaranteed cap or floor enters, and the cap must actually bind.

**"Why does this matter for a hackathon?"** Every perp DEX copies a funding rule. Clockwork tells them whether their rule produces testable, enforceable bounds, and how to fix it.

**"Isn't this just an academic curiosity?"** No. The paper shows a real protocol design failure: dYdX's margin-based caps are so wide that the no-arbitrage mechanism never activates. That is a design choice with measurable consequences.

**"What is the product?"** An oracle and auditor: `FundingDeterminism`, `BoundStatus`, `CapBindingRisk`, and `BandExit`. Plus a rule simulator for protocol designers.

**"How do you avoid overclaiming?"** The bound is only published when the guaranteed funding term is valid. `Gamma` is a calibrated high-probability parameter, not a model-free support bound. The reversion test is a diagnostic, not an arbitrage backtest.

**"What is the moat?"** The cross-protocol dataset, the timing classifier, the bound engine, the cap-binding model, and the design auditor.

**"Why would a DEX use it?"** To avoid launching a funding rule whose no-arbitrage bound is vacuous, and to calibrate caps that bind in stress without overreacting in normal markets.

### Claims to avoid

- "Risk-free arbitrage from Drift band exits."
- "dYdX is inefficient because its basis dislocations are large."
- "The 99th-percentile `Gamma` is a model-free bound."
- "The reversion test is an executable strategy."
- "All forward-looking funding rules are untestable." They are testable if a cap or floor binds or another entry-time guarantee exists.
- "Funding timing is the only thing that matters." Cap calibration, proxy quality, and execution costs also matter.

### Track and sponsor fit

- **DeFi/derivatives track:** funding-timing oracle, perp DEX design auditor.
- **Oracle track:** `FundingDeterminism`, `BoundStatus`, `CapBindingRisk` oracles.
- **Data/analytics track:** Drift vs dYdX comparison, band-exit scanner.
- **Risk/security track:** cap-binding and bound-vacuity risk.
- **AI/agent track:** research copilot for protocol design.
- **Ethereum/L2 track:** apply the framework to L2 perp DEXs and oracle design.
- **Market microstructure track:** funding timing and no-arbitrage testability.

## Hackathon-level idea for this paper

### Clockwork: the funding-timing oracle and no-arbitrage band engine

**One sentence.** Clockwork tells any DeFi perp whether its no-arbitrage bound is actually testable, publishes the band only when the funding is guaranteed, and helps protocols redesign their funding rule so the bound has teeth.

**Why it is stronger than a dashboard.** A dashboard shows funding rates. Clockwork publishes a **testability verdict** and a **design fix**. It turns a subtle theory result into a protocol primitive.

**Core modules.**

1. **Rule parser.** Ingest funding interval, cap, floor, aggregation, oracle source, and settlement timing.
2. **Timing classifier.** Determine whether the next funding transfer is observable at entry or forward-looking.
3. **Bound engine.** Compute `L` and `U` for the protocol; publish only when the guaranteed funding term is valid.
4. **Cap-binding monitor.** Estimate the probability that the cap/floor binds in normal and stress regimes.
5. **Gamma calibrator.** Train/test split, 99th percentile, sensitivity to 95th/90th, proxy-error diagnostic.
6. **Band-exit scanner.** Detect out-of-band observations, estimate reversion, adjust for proxy error and costs.
7. **Design auditor.** Flag vacuous bounds; recommend decoupling caps from margin parameters; simulate alternative rules.
8. **Oracle.** Publish `FundingDeterminism`, `BoundStatus`, `CapBindingRisk`, `BandExit`, and `ProxyError`.

**Onchain architecture.**

- `FundingTimingOracle.sol`: stores the timing classification and guaranteed-funding flag.
- `BoundOracle.sol`: stores `L`, `U`, `Gamma`, and `BoundStatus` (valid/vacuous).
- `CapRiskOracle.sol`: stores cap-binding probability and stress scenarios.
- `BandExitOracle.sol`: stores out-of-band signals and reversion scores.
- `RuleRegistry.sol`: stores protocol specs and auditor recommendations.
- Off-chain keeper: ingests on-chain data, computes bounds, signs reports.
- Consumer adapters: perp DEX, basis vault, risk dashboard, router.

**Demo moment.** Side-by-side Drift and dYdX. Drift: cap never binds, but `FundingDeterminism = observable`, `BoundStatus = valid`, band exits 3-5%, reversion positive. dYdX: `FundingDeterminism = forward-looking`, `BoundStatus = vacuous`, `CapBindingRisk = ~0`, zero testable exits. The auditor recommends a tighter cap decoupled from margin; the simulator shows the clamped regime appearing and the bound becoming testable. Publish the new `BoundStatus` onchain.

**Moat.** The cross-protocol dataset, the timing classifier, the bound engine, the cap-binding model, and the design auditor. The oracle improves with every protocol and regime.

**Risks.** Data quality, proxy error, `Gamma` sensitivity, and protocol changes. Keep the bound advisory and publish uncertainty.

**Integration with the existing portfolio.** Clockwork is the **testability layer** of the Perp Forge stack:
- **Perp Forge** designs the payoff and funding.
- **Funding DNA** calibrates effective feedback.
- **Clockwork** checks whether the no-arbitrage bound is testable and whether the cap binds.
- **Basis Mesh** provides the market anchor.
- **LiquidMesh** keeps the hedge alive.
- **TierBridge** executes the cross-venue trade.

**Score.** Novelty 5, technical 5, demo 4, feasibility 4, judge 5, moat 5. Total 28.

## Connection to the other papers

- **Paper 1 (Angeris et al.):** general funding and replication. Clockwork asks whether the funding rule produces testable no-arbitrage restrictions in practice.
- **Paper 2 (He et al.):** no-arbitrage band and clamp-aware benchmark. Clockwork extends the clamp logic to DeFi protocols and makes funding timing explicit.
- **Paper 3 (Alexander et al.):** liquidation-aware hedging. A real band-exit trade must survive margin and liquidation; Clockwork's signal is not a hedge.
- **Paper 4 (Zhang):** effective funding feedback. Clockwork's `FundingDeterminism` and `CapBindingRisk` are complementary to Funding DNA's effective-feedback score.
- **Paper 5 (Zhivkov):** cross-venue execution. Clockwork tells TierBridge which venues have testable, enforceable funding; TierBridge tells Clockwork whether the band exit is executable.
- **Synthesis:** Paper 1 designs; Paper 2 anchors; Paper 3 survives; Paper 4 calibrates; Paper 5 executes; Paper 6 tests whether the whole thing is enforceable.

**Full ideation:** See [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md), especially the new Wave 4 sections on Clockwork, the funding-timing oracle, the cap-binding auditor, and the Perp Forge 4.0 stack.
