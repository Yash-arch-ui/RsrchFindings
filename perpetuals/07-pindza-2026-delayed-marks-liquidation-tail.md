# Delayed Marks, Funding Memory, and Forecasting Liquidation-Tail Risk in Crypto Perpetual Futures

## Metadata

- **Authors:** Edson Pindza and Hopolang Phillip Mashele (Department of Decision Sciences, School of Economic and Financial Sciences, College of Economic and Management Sciences, University of South Africa, UNISA)
- **Journal:** Forecasting (MDPI), 2026, vol. 8, issue 5, article 76
- **DOI:** 10.3390/forecast8050076
- **History:** Received 15 July 2026; revised 19 August 2026; accepted 28 August 2026; published 30 August 2026
- **License:** CC BY 4.0
- **Length:** 36 pages; 9 main tables, 2 appendix tables, 8 figures, 44 references
- **MSC:** 60G22; 60H30; 91G20; 91G60
- **JEL classification:** C58; G12; G13; G15; G23
- **Keywords:** crypto perpetual futures; liquidation risk forecasting; predictive distributions; oracle latency; funding rates; order-book depth; first-passage; market microstructure
- **Type:** Theory plus empirical calibration. First-passage forecasting, stopping-time formulas, fractional funding memory, path-level simulation, public-data calibration, rolling proper-score evaluation.
- **Data:** Binance USD-M futures hourly and one-minute prices for BTCUSDT, ETHUSDT, SOLUSDT; 1,000 hourly and 1,000 one-minute observations per asset; 200 native 8-hourly funding records per asset; 15 depth-feature rows; hourly panel 3 May to 14 June 2026; later sensitivity snapshot 4 July 2026; 379 aggregate stablecoin observations and 1,684 public BTC/ETH option summaries as context only.
- **Read status:** Full paper read end to end, including all propositions, theorem proofs, tables, figures, appendices, robustness checks, and references.

## One-paragraph summary

The paper treats crypto liquidation as a first-passage problem in which the exchange observes the executable price through a delayed or smoothed mark, funding persistently drains collateral, and liquidation must be executed against finite depth. The central forecast is a joint event-and-severity distribution: the probability that an isolated-margin account breaches its maintenance boundary over a horizon, and, conditional on detection, the distribution of catch-up overshoot and bad debt hidden by the delayed mark. For a fixed delay `delta`, expected overshoot is `sigma * sqrt(delta / (2 pi))` and bad-debt probability is a Gaussian tail governed by the latency-to-margin ratio `sigma * sqrt(delta) / m`. The leverage cancellation is exact only under constant maintenance, fixed non-price drains, and no execution concession. For a TWAP mark, fixed-time variance falls to one third of the fixed-delay variance, but liquidation samples the stale error at a downcrossing; a stationary-crossing approximation size-biases it adversely, producing mean overshoot about 1.6 times the same-window fixed-delay value and a bad-debt crossover near a latency-margin ratio of 0.89. Empirically, one-week breach probabilities range from 0.07-0.23 at 10x to 0.39-0.56 at 20x and 0.76-0.83 at 50x. The structural forecast beats historical simulation on Brier scores at 10x across 24-, 72-, and 168-hour horizons and across the 24-hour grid, but historical simulation wins for one-week forecasts at 20x and 50x. The paper is careful to call these model-consistent hypothetical-account forecasts, not observed venue liquidations.

## Section-by-section findings

### Highlights and Abstract (p. 1)

- The model produces a horizon-specific first-passage breach probability and a conditional catch-up-loss distribution for an isolated-margin position.
- In rolling forecasts, the structural model has lower Brier scores than historical simulation at 10x across 24-, 72-, and 168-hour horizons, but not uniformly at high weekly leverage.
- Probability forecasts should jointly condition on mark delay, maintenance margin, funding state, and executable depth.
- The benchmark results point to conditionally heteroskedastic or regime-sensitive price densities as the next extension for stressed high-leverage forecasts.
- The abstract adds:
  - Fixed-delay expected overshoot `sigma sqrt(delta / (2 pi))`.
  - Bad-debt probability is a Gaussian tail governed by `sigma sqrt(delta) / m`.
  - Leverage independence is exact only in the constant-maintenance, fixed-non-price-drain benchmark.
  - TWAP fixed-time variance reduction does not imply liquidation-time safety.
  - Stationary-downcrossing approximation size-biases the stale error adversely, with mean overshoot about 1.6 times the same-window fixed-delay value.
  - Rolling comparison: structural forecast better at 10x across horizons; historical simulation better for one-week 20x and 50x.
  - The evidence supports conditional risk forecasting, not uniform forecast dominance.

### Section 1: Introduction (pp. 2-5)

- Perpetual futures are a major venue for leveraged crypto exposure without fixed maturity.
- Three linked mechanisms:
  1. Funding rates are feedback variables that respond to and influence positioning.
  2. The mark price is an exchange-defined observation operator that may smooth, delay, or discretize information.
  3. The order book determines how shocks convert into slippage and collateral depletion.
- The interaction is a first-passage problem:
  - Leverage and funding determine when executable margin surplus reaches the maintenance boundary.
  - Mark construction determines when the crossing is detected and how much executable price risk remains.
  - Depth determines the additional execution loss.
- The primary research question: how to forecast the probability and conditional severity of an isolated-margin liquidation when funding changes the account boundary, the mark observes prices with delay or smoothing, and liquidation is executed against finite depth.
- Forecast object:
  - At a forecast origin, assign a probability to a boundary breach over a stated horizon.
  - Conditional on a detected breach, assign a continuous distribution to catch-up overshoot and bad-debt state.
- Contributions:
  1. A unified first-passage forecast with distinct roles for leverage, funding, price observation, maintenance margin, and execution depth.
  2. Stopping-time formulas for conditional catch-up loss under fixed-delay and TWAP marks, including the difference between fixed-time smoothing variance and crossing-selected error.
  3. A validation design combining analytical checks, path-level mechanism simulation, public-data calibration, and rolling proper-score comparison with historical simulation.
  4. Explicit claim boundaries separating model-consistent hypothetical-account forecasts from unobserved venue-level customer liquidations.
- Table 1 maps theory to evidence:
  - Boundary-breach event: price path, leverage, maintenance, funding -> `pi_{t,h,L}` -> rolling Brier/log scores vs historical simulation.
  - Conditional delayed-mark severity: volatility, feed delay, maintenance -> fixed-delay overshoot and bad-debt distribution -> one-minute stale errors and path simulation.
  - Conditional smoothing/execution severity: TWAP window, volatility, crossing selection, liquidation size, depth -> TWAP loss plus average execution concession -> TWAP simulation and public depth descriptors.
- Principal findings:
  - Fixed-delay overshoot `sigma sqrt(delta / (2 pi))`.
  - Bad-debt probability `Phi(log(1-m)/(sigma sqrt(delta))) ~= Phi(-m/(sigma sqrt(delta)))`.
  - Dimensionless driver `sigma sqrt(delta)/m`.
  - Leverage cancellation only under constant maintenance and fixed non-price drains.
  - TWAP unconditional stale error has one third of fixed-delay variance, but crossing-conditioned overshoot is about 1.6x.
  - Funding memory scales cumulative-drain variance as `T^{2H+2}` under fractional noise without mean reversion.
  - One-week breach probabilities: 0.07-0.23 at 10x, 0.39-0.56 at 20x.
  - Funding memory is second-order at calm funding levels but material in crowded regimes.
- Empirical design:
  - Public Binance USD-M data accessed 14 June 2026.
  - 1,000 hourly and 1,000 one-minute observations per asset; 200 native 8-hourly funding records per asset; 15 depth rows.
  - Hourly panel 3 May to 14 June 2026.
  - 379 stablecoin observations and 1,684 option summaries are context only, not calibration inputs.
- Paper organisation: Section 2 literature; Section 3 primitives; Section 4 theory; Section 5 data; Section 6 results; Sections 7-9 limitations/implications; Section 10 conclusion.

### Section 2: Literature review (pp. 5-7)

- First passage and margin boundaries: Merton (1974), Black-Cox (1976), Leland (1994), Longstaff-Schwartz (1995). Crypto liquidation is faster and more rule-based, but the mathematics is the same: a stochastic surplus reaches a boundary before intervention.
- Microstructure, impact, depth: limit-order books do not convert quantity to price at a constant rate; marginal cost rises as liquidity is consumed. Makarov-Schoar document segmentation; Brauneis et al. benchmark liquidity; Donier-Bonart find square-root impact on Bitcoin. The paper uses a convex depth function.
- Long memory and rough dynamics: Mandelbrot-Van Ness, Cont, Gatheral et al., Takaishi. The natural object for persistent funding is fractional Ornstein-Uhlenbeck; the empirical analysis tests positive serial dependence but does not identify a universal fractional law.
- Perpetual futures, collateral liquidity, DeFi liquidations: He et al. (no-arbitrage prices, slow funding-basis decay); Ackerer et al. (perpetual equals spot sampled at a funding-determined random time); Brunnermeier-Pedersen (margin/loss spirals); Qin et al. (DeFi liquidations); Werner et al. (liquidation/oracle dependence); Lehar-Parlour (on-chain liquidation impact); Klages-Mundt-Minca (deleveraging spirals); stablecoin literature.
- Oracle risk: Qin et al. (flash-loan oracle manipulation); Mackinga et al. (TWAP oracle attacks). Those concern adversarial price formation; this paper concerns stopping-time consequences of latency even with an honest feed.
- Probabilistic risk forecasting: proper scoring rules (Gneiting-Raftery), density forecast evaluation (Diebold et al.). The forecast is a Bernoulli breach distribution plus continuous catch-up severity.
- Closest related work:
  - Cheng et al. (2021): GEV tail theory for Bitcoin-futures liquidation and optimal margins; no delayed mark or funding memory.
  - He et al. and Ackerer et al.: pricing and funding; no latency or liquidation stopping problem.
  - Kim-Park (2025): path-dependent funding design; no latency.
  - Donnelly-Lin-Lorig (2026): optimal active liquidation; complementary to passive margin first passage.
  - Burnashev-Tchamkerten (2012): first passage observed through delay in detection theory; signal estimation, not margin loss.
- Critical comparison: extreme-value margin models estimate tails from realized returns; perpetual-pricing models discipline no-arbitrage; oracle-manipulation studies quantify adversarial distortion; optimal-execution models begin with the decision to liquidate. None can be substituted unchanged for the joint boundary-breach and catch-up-loss distribution.

### Section 3: Mathematical framework (pp. 7-9)

#### 3.1 Executable price, delayed mark, filtration

```
X_t = log S_t, S_t > 0
M_t = O_{delta, K, eta}(S)_t
log M_t = X_{(t-delta)^+} + eta_t    (pure fixed delay)
F^M_t = sigma(M_u : u <= t) coarser than F_t
D_t(delta) = X_t - X_{(t-delta)^+} - eta_t    (stale-mark log error)
D_t(K, eta) = X_t - log M_t    (general kernel)
```

- The mark is an observation functional, not assumed equal to the executable price.
- `delta >= 0` is a discrete feed delay; `K` is a smoothing kernel; `eta_t` is additive measurement disturbance. Smoothing is represented by `K`, not by `eta_t`.
- The gap between the executable filtration and the mark filtration is the mathematical content of oracle latency.

#### 3.2 Funding as a persistent feedback state

```
dF_t = -kappa F_t dt + sigma_F dB^H_t + chi dQ_t
```

- `kappa >= 0` mean reversion; `B^H` fractional Brownian motion with Hurst `H in (1/2, 1)` under long memory; `Q_t` net positioning.
- Nests short-memory funding at `H = 1/2, chi = 0`; the empirical layer works at `chi = 0` and retains the positioning term for interpreting directional stress through `q_t F_t`.
- A position `q_t` pays `dCF_t = -q_t S_t F_t dt`: for a long, positive funding drains collateral; for a short, negative funding does.
- The stress variable is `q_t F_t`, not `F_t` alone.

#### 3.3 Depth, impact, collateral loss

- `L_t(y)` dollar liquidity between mid and terminal fractional displacement `y >= 0`; cumulative depth is concave at the inside and thins under stress.
- Total execution cost `Phi_t(z)` is convex: `Phi(0)=0`, `Phi'(z)>0`, `Phi''(z)>=0`.
- Power-law book `L(y) = a y^gamma`, `0 < gamma <= 1`.
- Band depth `D = a b^gamma`.
- Terminal book displacement:

```
y_gamma(z; D) = b (z/D)^{1/gamma}
```

- Average fractional execution-price concession:

```
phi_bar_gamma(z; D) = (1/z) integral_0^z y_gamma(u; D) du
                    = (gamma/(1+gamma)) b (z/D)^{1/gamma}
```

- Log-equivalent concession used with log-return thresholds:

```
ell_gamma(z; D) = -log(1 - phi_bar_gamma(z; D)),   0 <= phi_bar < 1
```

- `y_gamma` is the terminal level reached; `phi_bar` is the average execution price; `ell_gamma` is its log-return representation.
- Thin depth raises all three.
- A smaller `gamma` raises terminal displacement when `z/D > 1`; for average concession the ordering requires sufficiently stressed utilisation, with local condition `log(z/D) > gamma/(1+gamma)`.

#### 3.4 Margin surplus, isolated-margin boundary, liquidation time

- Isolated-margin long: leverage `L > 1`, collateral `C_0`, notional `N_0 = L C_0`, entry `P_0`.
- Equity `E_t = C_0 + N_0(P_t/P_0 - 1)`; maintenance requirement `m N_0(P_t/P_0)`, `m in (0,1)`.
- Liquidation when `E_t <= m N_0(P_t/P_0)`.
- Liquidation price ratio and simple-return drop:

```
rho* = (1 - 1/L)/(1 - m)
d_L = (1/L - m)/(1 - m)
b_L = log(1 - d_L) < 0
```

- Residual equity at the boundary in collateral units:

```
xi(L) = m(L - 1)/(1 - m) = m L rho*
```

- Cumulative funding and fee drain `G^F_t`; normalized `g_t = G^F_t/C_0`.
- Normalized surplus when equity and maintenance are evaluated on the mark:

```
Y^{delta,G}_t / C_0 = 1 + L(M_t/P_0 - 1) - g_t - m L M_t/P_0
```

- For pure delay, a generally time-varying log-price boundary:

```
b_L(g_t) = log((1 - 1/L + g_t/L)/(1 - m))
tau_m(delta, G) = inf{t >= 0 : X_{(t-delta)^+} + eta_t <= b_L(g_t)}
Pi(T; m, delta, H, Phi) = P(tau_m(delta,G) <= T)
```

- Simple time shift requires more than a delayed price. If `eta=0` and funding/fees/maintenance/position are fixed, so `b_L(g_t)=b_L`, continuity gives:

```
tau^price_m(delta) = tau^price_m(0) + delta
P{tau^price_m(delta) <= T} = P{tau^price_m(0) <= T - delta}, T >= delta
```

- If `g_t` evolves during the delay, `b_L(g_t)` moves, and the time shift need not hold. The first-passage simulation evaluates funding contemporaneously through the full surplus.

### Section 4: Theoretical results (pp. 9-19)

#### Assumption 1 (Locally sub-Gaussian increments)

For each `delta > 0`, conditional on `F_{t-delta}`, the increment `X_t - X_{t-delta}` is centred with conditional variance `v_t(delta)` and satisfies a sub-Gaussian tail bound. This is a tractable benchmark, not a denial of heavy tails.

#### Lemma 1 (Delay increases stale-price dispersion)

If `X` is a square-integrable local martingale over the intraday window, `E[D_t(delta)^2]` is non-decreasing in `delta` under `eta=0`. Stationary increments are not required.

Proof uses the martingale isometry:

```
E[D_t(delta)^2] = E[<X>_t - <X>_{t-delta}]
```

and the nested interval inequality. A centred measurement disturbance with equal variance and orthogonality preserves the ordering.

#### Proposition 1 (Boundary shrinking by a stale mark)

If the long is marked at stale price `M_t` with `S_t = M_t e^{D_t}` and adverse staleness `D_t < 0`, then to first order executable equity is below mark-reported equity by `-Ntilde_t D_t`, where `Ntilde_t = N_0 M_t/P_0`. The maintenance-requirement error is `-m Ntilde_t D_t`, smaller by `m`. The dominant effect is overstated equity, not misstated requirement.

- `Y_t - Ytilde_t = (1-m) Ntilde_t (e^{D_t}-1) = (1-m) Ntilde_t D_t + O(D_t^2)`.
- For `D_t < 0`, `Ytilde_t - Y_t = (1-m) Ntilde_t |D_t| + O(D_t^2) > 0`: the stale mark overstates surplus.
- The practical consequence is catch-up risk.

#### Proposition 2 (Funding memory raises cumulative-drain variance)

Let funding be driven by fractional noise with `H in (1/2,1)` and no mean reversion, `F_t = sigma_F B^H_t`. For constant exposure over `[0,T]`:

```
Var(integral_0^T F_t dt) = (sigma_F^2 / (2H+2)) T^{2H+2}  proportional to T^{2H+2}
```

- `2H+2 > 3` for `H > 1/2`, strictly stronger horizon scaling than Brownian `T^3`.
- With `kappa > 0` and a non-stationary initial condition, local scaling holds only for `T << 1/kappa`.
- A stationary fractional OU process has covariance of order `t^{2H-2}`, so the variance of its time integral grows as `T^{2H}` at long horizons, not `T^{2H+2}`.

Proof by self-similarity and covariance integration; unit-interval variance `1/(2H+2)`. At `H=1/2` it becomes `sigma_F^2 T^3/3`.

**Numerical validation.** Exact fractional Gaussian noise simulation; fitted log-log slopes 2.99, 3.36, 3.79 at `H=0.5, 0.7, 0.9`, against theoretical 3.0, 3.4, 3.8 (within 1.1%).

- The power-law exponent is distinct from the level multiplier of the tractable AR(1) drain used in the breach experiment. The AR(1) raises one-week cumulative-drain variance by `(1 + b phi)/(1 - b phi) ~= 6` without changing the long-horizon exponent of the AR(1) sum.

#### Proposition 3 (Comparison principle for ordered losses)

Let `Y^{(j)}_t = Y_0 - A_t - G_t - I^{(j)}_t`, `j in {1,2}`, be two surplus processes coupled on the same price and funding paths. If `I^{(2)}_t >= I^{(1)}_t` almost surely for every `t <= T`, then:

```
P(inf_{0<=t<=T} Y^{(2)}_t <= 0) >= P(inf_{0<=t<=T} Y^{(1)}_t <= 0)
```

- For terminal displacement, decreasing `gamma` produces this order when `z/D > 1` and reverses it when `z/D < 1`.
- For average concession, the `gamma/(1+gamma)` factor means `z/D >= 1` is insufficient; the actual concessions must be ordered, with local sufficient direction `log(z/D) > gamma/(1+gamma)`.
- No analogous pathwise order is asserted for two observation delays or two Hurst indices.

#### Theorem 1 (Catch-up overshoot and bad-debt probability)

Assumption 2 (fixed-delay Gaussian stopping benchmark):
- Executable log price continuous and locally strong-Markov over `[tau, tau+delta]`.
- `X_{tau+u} - X_tau = sigma(W_{tau+u}-W_tau)`, constant `sigma > 0`.
- Maintenance `m` constant, independent of leverage; no measurement noise; funding, fees, position size, and boundary fixed or absorbed during detection.
- Execution loss applied separately through `ell_gamma`.

Define catch-up overshoot `O(delta) = max(0, -Delta X_delta)`, where `Delta X_delta = X_{tau+delta}-X_tau`.

```
E[O(delta)] = sigma sqrt(delta/(2 pi))
```

Equity at mark liquidation `E_{sigma_delta}/C_0 = 1 + L(rho* e^{Delta X_delta} - 1)`. Bad debt (negative equity) iff `Delta X_delta < log(1-m)`, with probability:

```
P_bd(delta) = Phi(log(1-m)/(sigma sqrt(delta))) = Phi(-m/(sigma sqrt(delta))) (1 + O(m))
```

- Exactly independent of leverage within the constant-`m` benchmark: the return-space cushion between liquidation price and zero equity is always the maintenance fraction `m`.
- `P_bd` is strictly increasing in `delta` and `sigma`, governed by `sigma sqrt(delta)/m`.
- With average execution concession on liquidated notional `z=N_tau`, the threshold tightens to `Delta X_delta < log(1-m) + ell_gamma(z;D)`.
- Thinner depth raises `P_bd`; a smaller `gamma` raises it only where the average concession is actually larger; execution reintroduces size dependence.

Proof:
- At executable crossing, `P_tau/P_0 = rho*`; at delayed mark liquidation `P_{sigma_delta}/P_0 = rho* e^{Delta X_delta}`.
- Equity non-negative iff `e^{Delta X_delta} >= 1-m`, i.e. `Delta X_delta >= log(1-m)`; leverage cancels.
- Overshoot: `Delta X_delta = sZ`, `s=sigma sqrt(delta)`, `E[(-sZ)^+] = s phi(0) = sigma sqrt(delta/(2 pi))`.
- Bad debt: `Phi(log(1-m)/(sigma sqrt(delta)))`; `log(1-m) = -m + O(m^2)`.

**Remark 4: What breaks leverage independence.**
- Tiered `m=m(L)` puts `log(1-m(L))` in the threshold.
- Incremental funding/fee drain `g_delta` during detection gives:

```
E^{net}_{sigma_delta}/C_0 = 1 + L(rho* e^{Delta X_delta - ell_gamma(z;D)} - 1) - g_delta
Delta X_delta < log(1-m) + ell_gamma(z;D) + log(1 + g_delta/(L-1))
```

- Incremental funding moves the threshold and generally restores leverage dependence.

**Corollary 1 (Latency-margin scaling).** A protocol holds bad-debt probability from delay below `p* < 1/2` iff:

```
sigma sqrt(delta) <= -log(1-m)/Phi^{-1}(1-p*)
```

and to first order in `m` iff `sigma sqrt(delta) <= m/Phi^{-1}(1-p*)`. This is leverage-independent only under Assumption 2. Mark delay and maintenance margin are substitutes within the benchmark: doubling tolerated delay requires raising `m` by `sqrt(2)` to hold bad-debt risk fixed.

**Corollary 2 (Leverage and breach probability).** `|b_L| ~= 1/L - m`, decreasing in `L`, so breach probability increases in leverage. Conditional on delayed liquidation, with constant `m` and before incremental funding/fees/execution, bad-debt probability is leverage-independent. Leverage changes the frequency of reaching liquidation; delay changes conditional severity.

#### 4.2 The cost of a smoothed (TWAP) mark

Two heuristics suggest smoothing is benign:
- **Mean-lag heuristic:** TWAP of window `delta` is treated as fixed delay at mean lag `delta/2`.
- **Variance heuristic:** unconditional stale error carries only one third of fixed-delay variance (`delta/3`).

Both evaluate stale error at a fixed time. Liquidation samples the error at the first downcrossing of the boundary by the smoothed mark; that conditioning is first-order and reverses the comparison.

#### Lemma 2 (TWAP stale-error law)

Let the mark be a trailing uniform TWAP of window `delta`:

```
M_t = exp((1/delta) integral_{t-delta}^t X_u du)
D^{TWAP}_t = X_t - (1/delta) integral_{t-delta}^t X_u du
Delta_t = X_t - X_{t-delta}
```

For a driftless local martingale with local volatility `sigma`, to leading order `(D^{TWAP}_t, Delta_t)` is centred bivariate Gaussian:

```
Var(D^{TWAP}_t) = sigma^2 delta / 3
Var(Delta_t) = sigma^2 delta
Cov(D^{TWAP}_t, Delta_t) = sigma^2 delta / 2
E[D^{TWAP}_t | Delta_t] = Delta_t/2
Var(D^{TWAP}_t | Delta_t) = sigma^2 delta / 12
```

- Unconditional dispersion is one third, not one half, of fixed-delay dispersion.
- A `K`-sample discrete TWAP with grid spacing `h=delta/K` has exact variance `sigma^2 delta (K-1)(2K-1)/(6K^2) -> sigma^2 delta/3`.

#### Theorem 2 (Stationary-crossing approximation for a TWAP mark)

Let liquidation occur at the first downcrossing `tau_M` of the boundary by the TWAP mark, at a crossing time large relative to the window. Approximating the first downcrossing by a stationary downcrossing, the leading-order stale-error law is:

```
D^{TWAP}_{tau_M} ~= sigma sqrt(delta)(-R/2 + Z/sqrt(12))
R ~ Rayleigh(1) independent of Z ~ N(0,1)
```

Mean stale error `-sqrt(2 pi)/4 sigma sqrt(delta) ~= -0.627 sigma sqrt(delta)`: when the smoothed mark reaches the boundary, the executable price has systematically led it down.

```
E[O^{TWAP}(delta)] = c_O sigma sqrt(delta),   c_O = E[(R/2 - Z/sqrt(12))^+] = 0.637
P^{TWAP}_bd(delta) = P(R/2 - Z/sqrt(12) > -log(1-m)/(sigma sqrt(delta)))
```

- Both leading-order objects are leverage-independent only within the constant-`m`, fixed-non-price-drain benchmark; tiered maintenance, funding during detection, or execution costs restore leverage/size dependence.
- The law is an approximation, not a proved bound for the first-passage distribution.

Proof sketch:
- At `tau_M`, `log M = b_L`, so `D^{TWAP}=X-log M`; executable price is `b_L + D^{TWAP}`.
- Kac-Rice conditioning weights a downcrossing by absolute slope; for `x<0`, `f_{Delta|down}(x) = |x| f_Delta(x) / integral_{-inf}^0 |u| f_Delta(u) du`.
- With `x=-sigma sqrt(delta) r`, density becomes `r e^{-r^2/2}` (Rayleigh).
- Conditional stale error: `N(Delta/2, sigma^2 delta/12)`.
- Residual level-conditioning term `O(delta/t)` negligible when crossing time is large relative to window.
- `E[R]=sqrt(pi/2)`, so mean stale error `-sqrt(2 pi)/4 sigma sqrt(delta)`.
- Overshoot `O^{TWAP} = (-D^{TWAP})^+ = sigma sqrt(delta)(R/2 - Z/sqrt(12))^+`; constant 0.637.
- Bad debt when `D^{TWAP} < log(1-m)`, equivalently `R/2 - Z/sqrt(12) > -log(1-m)/(sigma sqrt(delta))`.

**Remark 5: Why fixed-time heuristics fail.** Scaling `sigma^2 delta/3` into Theorem 1 would predict `E[O^{TWAP}] = sigma sqrt(delta/(6 pi)) ~= 0.23 sigma sqrt(delta)`; mean-lag heuristic predicts `0.28 sigma sqrt(delta)`. Crossing-conditioned value `0.637 sigma sqrt(delta)` is 2.3-2.8x larger.

**Corollary 3 (Stationary-crossing comparison with fixed delay).**

```
E[O^{TWAP}]/E[O] = c_O sqrt(2 pi) ~= 1.60
```

TWAP overshoot equals a fixed delay of about `2.6 delta`, not `delta/3`. Numerical bad-debt comparison gives crossover `kappa* ~= 1.12` in `kappa = -log(1-m)/(sigma sqrt(delta))`. Smoothing raises bad-debt probability when `sigma sqrt(delta)/m >~ 0.89` and lowers it below.

- Tail comparison:
  - Fixed delay: `p_fix(kappa) = Phi(-kappa)`.
  - TWAP: `p_TWAP(kappa) = integral_0^inf Phi(sqrt(12)(r/2 - kappa)) r e^{-r^2/2} dr`.
  - Integration by parts: `p_TWAP(kappa) = Phi(-sqrt(12) kappa) + (sqrt(3)/2) e^{-3 kappa^2/2} Phi(3 kappa)`.
  - Difference changes sign at `kappa* = 1.1225`.

### Section 5: Materials and methods (pp. 19-21)

#### 5.1 Data sources

- Binance USD-M futures: hourly and one-minute prices for BTCUSDT, ETHUSDT, SOLUSDT; native funding histories; current depth snapshots.
- DeFiLlama: aggregate stablecoin circulating supply.
- Deribit: public BTC/ETH option summaries.
- Primary snapshot 14 June 2026; later sensitivity snapshot 4 July 2026.
- Table 2 panel:
  - BTC: 1000 hourly obs, 3 May-14 June 2026, mean hourly return -0.0002083, hourly vol 0.004448, funding mean 0.27 bp, q05 -0.46, q95 1.00, Hurst 0.9363, AR1 0.7126.
  - ETH: mean -0.0003419, vol 0.005825, funding mean 0.23 bp, q05 -0.67, q95 0.94, Hurst 0.9323, AR1 0.6992.
  - SOL: mean -0.0002243, vol 0.006687, funding mean -0.24 bp, q05 -1.97, q95 1.00, Hurst 0.8197, AR1 0.5429.
- Table 3 depth:
  - BTC 10bp: USD 25.7m, imbalance -0.06059, 537/553 levels, not capped.
  - BTC 50bp: USD 63.8m, 1000/1000 levels, capped (lower bound).
  - ETH 10bp: USD 20.5m, 167/167 levels.
  - ETH 50bp: USD 111.6m, 817/809 levels.
  - SOL 10bp: USD 4.4m, 7/7 levels.
  - SOL 50bp: USD 19.6m, 34/34 levels.
- Depth snapshot is current, not historical; 1000-level endpoint cap. 10bp band fully covered for all; cross-asset comparisons use 10bp.
- Majors 5-6x deeper than SOL at 10bp; at 50bp SOL is at most a third of BTC's capped depth.

#### 5.2 Measurement design

- Returns, realized volatility, funding distributions, native-series persistence, stale-price errors, depth measured directly.
- Empirical volatility substituted into fixed-delay overshoot (14), bad-debt (15), and TWAP approximation (19).
- First-passage simulation uses observed volatility and funding to evaluate the mechanism; not estimated from historical liquidation records.
- Order-book depth enters separately through impact function (6).
- Stale-mark errors from one-minute prices. Path-level simulation validates fixed-delay formulas and evaluates TWAP approximation (Appendix B).
- First-passage breach probabilities are calibrated simulations because public endpoints do not provide complete user-level liquidation trajectories.
- Supplementary File S1: data, code, tables, figures, diagnostics, provenance.

#### 5.2.1 Hourly volatility and drift calibration

```
r_{i,t} = log P_{i,t} - log P_{i,t-1}
mu_hat_{h,i} = (1/n_i) sum_t r_{i,t}
sigma_hat_{h,i} = [ (1/(n_i-1)) sum_t (r_{i,t}-mu_hat)^2 ]^{1/2}
```

- 1000 hourly closes -> 999 non-missing returns; variance denominator 998.
- `sigma_hat_h = 0.004448, 0.005825, 0.006687` for BTC/ETH/SOL.
- Principal simulation draws `N(0, sigma_hat_h^2)` because sample means are not statistically different from zero at conventional two-sided levels (`|t| <= 1.9`).
- Observed-drift sensitivity draws `N(mu_hat, sigma_hat^2)` and raises several one-week breach probabilities; reported as stress.

#### 5.2.2 Funding persistence

- R/S Hurst on native 8-hourly funding (200 observations per asset), not forward-filled hourly.
- Reports moving-block-bootstrap 95% CI, i.i.d.-shuffle surrogate baseline, DFA exponent, AR(1).
- Forward-filling would repeat each 8-hourly value eight times and manufacture persistence.

#### 5.2.3 Stale-mark and catch-up experiment

- From one-minute prices, empirical stale error `|X_t - X_{t-delta}|` on `delta in {1,5,15,30,60}` minutes.
- From minute-scale volatility `sigma`, evaluate fixed-delay (14), (15), TWAP (19).
- Mechanism simulation liquidates minute-scale paths on delayed mark (`sigma_delta = tau + delta`) and on first downcrossing by trailing TWAP.
- Validates fixed-delay formulas and measures combined first-passage/discrete-monitoring error of TWAP approximation (Appendix B).

#### 5.2.4 First-passage simulation

- Each asset, `L in {3,5,10,20,50}`, `N=20,000` one-week paths.
- Mean-zero Gaussian price increments at estimated hourly volatility (20); observed-drift alternative separately.
- At each hour, equity and maintenance from `rho_t=P_t/P_0`:

```
E_t/C_0 = 1 + L(rho_t - 1) - L sum_{u<=t} rho_u f_u
M^{maint}_t/C_0 = m L rho_t
```

- `f_u` is the hourly allocation of the 8-hour funding rate. This implements notional funding payment, not subtraction from log return.
- AR(1), fractional-Gaussian-noise, and i.i.d. funding models have matched marginal variance and share price draws. Funding is centred to isolate serial dependence; observed mean is small relative to weekly price move.
- Wilson intervals report Monte Carlo binomial error conditional on estimated inputs; do not include calibration uncertainty.

#### 5.3 Forecast target and rolling comparison

At forecast origin `t`, horizon `h`, leverage `L`, binary target:

```
Y_{t,h,L} = 1{ min_{1<=j<=h} [ 1 + L(rho_{t,j}-1) - L sum_{k=1}^j rho_{t,k} fbar_{t+k} - m L rho_{t,j} ] <= 0 }
rho_{t,j} = P_{t+j}/P_t
```

- `fbar_{t+k}` is the hourly allocation of the contemporaneous 8-hour funding rate.
- Structural first-passage simulation supplies `pi_{t,h,L} = P(Y_{t,h,L}=1 | I_t)` and a Bernoulli predictive distribution.
- Conditional on mark-detected crossing, Theorems 1 and 2 add continuous catch-up severity. Full forecast is joint event-severity, but public-data comparison scores only the event component.
- Forecast origins one day apart. Each origin uses prior 336 hourly observations to estimate volatility and all native funding observations then available for funding scale and AR(1).
- Benchmark: historical simulation over every overlapping return-funding path in prior 336h, with Jeffreys correction for probabilities of zero/one.
- Evaluate 24-, 72-, 168-hour horizons at 10x, 20x, 50x.
- Brier and log scores; lower better.
- Realized `Y` from subsequent public price/funding path: model-consistent hypothetical-account outcome, not exchange record of customer liquidation.
- Conditional severity checked through path-level mechanism simulations because public endpoints lack complete mark histories/account trajectories.

### Section 6: Results (pp. 21-29)

#### 6.1 Prices and funding

- Figure 2: normalized public futures prices, substantial movement but no single crisis.
- Figure 3: funding dynamics. BTC/ETH positive mean funding, SOL negative. Fragility is direction-dependent; model `q_t F_t`, not `F_t`.

#### 6.2 Funding-rate persistence on native data

| Asset | Obs | H (R/S) | H CI low | H CI high | i.i.d. surrogate | surrogate s.d. | DFA alpha | AR(1) | z vs null |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| BTC | 200 | 0.9363 | 0.7522 | 1.01 | 0.6218 | 0.06575 | 1.059 | 0.7126 | 4.784 |
| ETH | 200 | 0.9323 | 0.6984 | 0.9665 | 0.6112 | 0.06588 | 1.049 | 0.6992 | 4.874 |
| SOL | 200 | 0.8197 | 0.6447 | 0.8967 | 0.6155 | 0.06687 | 0.9113 | 0.5429 | 3.054 |

- R/S Hurst 0.82-0.94, 3.1-4.9 surrogate standard deviations above i.i.d. shuffle baseline of 0.62.
- Block-bootstrap intervals wide at n=200; point estimates method-sensitive. Only positive serial dependence relative to shuffle benchmark is inferred; raw R/S level is not a fractional parameter.
- Runs at venue's 0.01% default rate contribute genuine serial dependence but may reflect funding formula regime stickiness.

#### 6.3 The cost of oracle delay

Table 5 (key cells at `m=0.5%`):

| Asset | Delay (min) | p95 stale error | sigma sqrt(delta)/m | E[O(delta)] | P_bd | P_TWAP_bd |
|---|---:|---:|---:|---:|---:|---:|
| BTC | 1 | 0.0005669 | 0.06107 | 0.0001218 | <1e-6 | <1e-6 |
| BTC | 15 | 0.00203 | 0.2365 | 0.0004718 | 1.1e-5 | <1e-6 |
| BTC | 60 | 0.003582 | 0.473 | 0.0009436 | 0.017 | 0.001 |
| ETH | 1 | 0.0006753 | 0.07457 | 0.0001487 | <1e-6 | <1e-6 |
| ETH | 15 | 0.002601 | 0.2888 | 0.0005761 | 2.6e-4 | <1e-6 |
| ETH | 60 | 0.00437 | 0.5776 | 0.001152 | 0.041 | 0.009 |
| SOL | 1 | 0.001158 | 0.1173 | 0.000234 | <1e-6 | <1e-6 |
| SOL | 15 | 0.004075 | 0.4543 | 0.0009062 | 0.014 | 5.8e-4 |
| SOL | 60 | 0.008306 | 0.9086 | 0.001812 | 0.135 | 0.139 |

- p95 stale error increases with delay for every asset (Lemma 1).
- Fixed-delay mechanism simulation: realized mean overshoot matches (14) within 1.3%; resolved bad-debt frequencies match (15) within 3%.
- Gaussian benchmark rises from negligible at one minute to 0.017 (BTC), 0.041 (ETH), 0.135 (SOL) at one hour.
- Cross-asset ordering follows `sigma sqrt(delta)/m`: 0.91 for SOL vs 0.47 for BTC.
- Delay grid is a stress range; one-hour cell is prolonged feed impairment, not normal operation.

**Fixed delay vs TWAP.**

- At SOL one-hour cell, realized bad-debt frequency at TWAP liquidation is 0.164, vs 0.028 from naive unconditional substitution.
- Stationary-crossing approximation tracks mean overshoot: realized TWAP overshoot within 5% of `c_O sigma sqrt(delta)` in every cell; 1.6-1.7x fixed-delay overshoot.
- Realized bad-debt frequency 15-40% above approximation in populated cells; gap combines no-earlier-crossing condition and discrete boundary overshoot.
- At one hour, mechanism comparison lowers bad-debt for BTC/ETH but raises for SOL: 0.164 TWAP vs 0.136 fixed delay. Stationary approximation predicts same sign change near latency-margin ratio 0.89.

#### 6.4 First-passage breach probabilities

Table 6 (one-week, `m=0.5%`, one-sided long):

| Asset | L | sigma_h | Pi_hat | CI low | CI high | Mean hrs to liq |
|---|---:|---:|---:|---:|---:|---:|
| BTC | 3 | 0.004448 | 0 | 0 | 1.9e-4 | - |
| BTC | 5 | 0.004448 | 1.0e-4 | 2.7e-5 | 3.6e-4 | 133.5 |
| BTC | 10 | 0.004448 | 0.071 | 0.068 | 0.075 | 118.9 |
| BTC | 20 | 0.004448 | 0.392 | 0.385 | 0.398 | 79.4 |
| BTC | 50 | 0.004448 | 0.757 | 0.751 | 0.763 | 35.4 |
| ETH | 3 | 0.005825 | 0 | 0 | 1.9e-4 | - |
| ETH | 5 | 0.005825 | 0.004 | 0.003 | 0.005 | 140.6 |
| ETH | 10 | 0.005825 | 0.166 | 0.161 | 0.171 | 105.4 |
| ETH | 20 | 0.005825 | 0.506 | 0.499 | 0.512 | 66.1 |
| ETH | 50 | 0.005825 | 0.807 | 0.801 | 0.812 | 28.9 |
| SOL | 3 | 0.006687 | 0 | 0 | 1.9e-4 | - |
| SOL | 5 | 0.006687 | 0.012 | 0.010 | 0.013 | 137.3 |
| SOL | 10 | 0.006687 | 0.224 | 0.219 | 0.230 | 98.0 |
| SOL | 20 | 0.006687 | 0.559 | 0.552 | 0.566 | 59.9 |
| SOL | 50 | 0.006687 | 0.826 | 0.821 | 0.832 | 26.1 |

- Essentially zero at 3-5x; 0.07-0.23 at 10x; 0.39-0.56 at 20x; 0.76-0.83 at 50x.
- Increasing in leverage and volatility (SOL > ETH > BTC). Mean time-to-liquidation falls from roughly 80h at 20x to 30h at 50x.
- Boundary algebra material: cross-margin algebra applied to isolated-margin account saturates probabilities at 0.87-1.0 and removes comparative statics.
- Flat `m=0.5%` across leverages is conservative lower bound on breach frequency; a real tiered schedule raises `m` at high leverage and pushes breach probabilities up. At 20x, moving `m` from 0.5% to 2% raises BTC breach prob from 0.39 to 0.56. Higher `m` also lowers bad-debt severity; risks move oppositely (Corollary 2).

#### 6.5 Out-of-sample first-passage forecasts

Table 7:

| Horizon | Leverage | Forecasts | Events | FP Brier | HS Brier | FP-HS | FP log | HS log |
|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 24 | 10x | 81 | 1 | 0.012 | 0.013 | -0.000 | 0.091 | 0.088 |
| 24 | 20x | 81 | 11 | 0.125 | 0.126 | -0.001 | 0.473 | 0.452 |
| 24 | 50x | 81 | 41 | 0.270 | 0.283 | -0.014 | 0.734 | 0.763 |
| 72 | 10x | 75 | 15 | 0.195 | 0.224 | -0.029 | 1.115 | 1.132 |
| 72 | 20x | 75 | 30 | 0.271 | 0.270 | +0.001 | 0.768 | 0.737 |
| 72 | 50x | 75 | 59 | 0.192 | 0.169 | +0.023 | 0.575 | 0.519 |
| 168 | 10x | 63 | 28 | 0.377 | 0.457 | -0.080 | 1.248 | 2.154 |
| 168 | 20x | 63 | 51 | 0.325 | 0.183 | +0.142 | 0.852 | 0.644 |
| 168 | 50x | 63 | 60 | 0.091 | 0.060 | +0.031 | 0.353 | 0.327 |

- Structural forecast lower Brier at all horizons for 10x, including 0.377 vs 0.457 at one week, and at every leverage over 24h.
- Log score lower at 24h for 50x, 72h for 10x, one week for 10x.
- Not uniformly favorable: at 20x/50x one week, historical simulation better. In sample, those configurations realize breaches in 51 and 60 of 63 overlapping forecasts; short rolling history responds more directly to sustained drawdown than stationary Gaussian specification.
- Diagnostic evidence, not universal superiority. Extension: conditionally heteroskedastic or regime-sensitive price density, retaining margin/funding/mark-severity map.

#### 6.6 Funding memory is regime-dependent

Table 8 (20x, one-week incremental breach probability vs matched-variance i.i.d.):

| Asset | Funding regime | b phi | Pi i.i.d. | AR(1) premium (s.e.) | fBm premium (s.e.) |
|---|---|---:|---:|---|---|
| BTC | calm | 0.7126 | 0.3907 | +0.0009 (0.0005) | +0.0011 (0.0005) |
| BTC | crowded x20 | 0.7126 | 0.3926 | +0.0048 (0.0018) | +0.0056 (0.0021) |
| BTC | extreme x60 | 0.7126 | 0.4037 | +0.0204 (0.0030) | +0.0226 (0.0034) |
| ETH | calm | 0.6992 | 0.506 | -0.0005 (0.0004) | -0.0004 (0.0005) |
| ETH | crowded x20 | 0.6992 | 0.5044 | +0.0022 (0.0017) | +0.0032 (0.0020) |
| ETH | extreme x60 | 0.6992 | 0.511 | +0.0131 (0.0029) | +0.0045 (0.0032) |
| SOL | calm | 0.5429 | 0.5591 | +0.0003 (0.0004) | +0.0003 (0.0004) |
| SOL | crowded x20 | 0.5429 | 0.5585 | +0.0034 (0.0018) | +0.0022 (0.0020) |
| SOL | extreme x60 | 0.5429 | 0.5669 | +0.0095 (0.0029) | +0.0036 (0.0032) |

- Calm observed funding: all differences within two paired standard errors of zero.
- x20 stress: AR(1) differences +0.002 to +0.005 (1.3-2.7 s.e.).
- x60 stress: +0.010 to +0.020 (3.2-6.9 s.e.); fractional gives +0.023 for BTC but only +0.005/+0.004 for ETH/SOL.
- Serial dependence material in some stressed calibrations; magnitude/resolution specification-dependent.
- Two variance statements:
  - Fractional scaling exponent `2H+2` validated directly (fitted 2.99/3.36/3.79 vs 3.0/3.4/3.8).
  - AR(1) drain inflates level of one-week cumulative-drain variance by `(1+b phi)/(1-b phi) = 5.9` at `b phi=0.71`, a level multiplier not a change in horizon scaling.
- Funding memory inflates variance several-fold and, under long memory, steepens horizon scaling; it translates into breach probability only when funding is large, as in crowded trades.

#### 6.7 Convex depth amplification

- Figure 7: terminal book displacement `y_gamma(z;D)=b(z/D)^{1/gamma}` vs depth utilisation for linear (`gamma=1`) and convex (`gamma=0.5`).
- For small account liquidating `z/D <= 0.1`, displacement modest; more convex book reaches nearer terminal level.
- At `z/D=2`, terminal displacement 200bp for `gamma=0.5` vs 100bp for `gamma=1`. Average concessions 66.7bp vs 50bp. Terminal and average objects must not be interchanged.
- SOL 50bp book at most a third of BTC's (capped lower bound), so same dollar liquidation reaches higher `z/D` on SOL; raises terminal displacement and, in stressed utilisation ranges, average/log-equivalent concessions entering Theorem 1.

#### 6.8 Contextual evidence outside central calibration

- Stablecoin supply (Figure 8): market-state descriptor; cannot reveal individual futures trader collateral eligibility; not in boundary, persistence, or breach simulations.
- Option cross-section: 1,684 BTC/ETH summaries; forward-looking volatility layer conceptually relevant to `sigma sqrt(delta)/m`; current calibration uses realized minute volatility so stale-price measurements and analytical inputs refer to same observed price process. Combining with implied volatility requires separate term-structure treatment; left as extension.
- Neither stablecoin nor option data enters boundary, funding process, latency formulas, execution mapping, or rolling scores.

### Section 7: Discussion (pp. 28-29)

- Leverage moves breach boundary (Corollary 2).
- Oracle delay governs conditional bad-debt severity through `sqrt(delta)` overshoot and `sigma sqrt(delta)/m` (Theorem 1); leverage still changes how often liquidation occurs.
- Funding dependence changes cumulative-drain variance (Proposition 2) and affects breach probability mainly when funding is large.
- Depth and book shape determine terminal displacement and average execution concession; ordering in book convexity is utilisation-dependent (Proposition 3).
- The mechanisms answer one research question, not four separate ones.
- Detection probability and loss severity respond differently to delay:
  - Under pure lag, continuous executable crossing is recorded after lag; probability of recorded liquidation by fixed calendar horizon can fall as `delta` increases.
  - Conditional on detection, executable price contains additional increment over hidden interval; expected adverse part proportional to `sqrt(delta)`; bad-debt probability depends on maintenance cushion through `sigma sqrt(delta)/m`.
  - Counts truncated at fixed horizon can fall because detection is postponed, even when conditional loss increases.
- TWAP: averaging reduces stale-error variance at fixed time, but liquidation samples error when smoothed process downcrosses boundary. Rayleigh mixture is stationary-downcrossing law, not exact law for discretely monitored venue. Path-level experiment measures error from first passage and discrete monitoring. Fixed-time variance can understate liquidation-time error.
- Funding persistence effect depends on persistence, scale, direction, horizon; persistence alone does not imply large breach probability.
- Depth: terminal displacement and average concession distinct; smaller `gamma` not automatically more costly at `z/D=1`; ordering must be checked over actual utilisation range using (6).
- Rolling comparison: model informative at moderate leverage and short horizons; one-week high-leverage result shows historical path distribution can be more accurate in persistent drawdown. Limitation of price-density specification, not evidence that public data identify historical customer liquidations.

### Section 8: Limitations and robustness (pp. 29-31)

#### 8.1 Observability

- Public endpoints do not report trader leverage, collateral balances, liquidation paths, insurance-fund transfers, or historical mark construction.
- First-passage probabilities are calibrated mechanism estimates, not venue-level liquidation frequencies.
- Stablecoin/option summaries provide context, not account collateral or liquidation events.

#### 8.2 Sampling

- Depth observed once and may decline under stress.
- 1000-level endpoint cap makes BTC 50bp depth a lower bound; BTC-ETH ordering at 50bp unidentified; cross-asset comparisons use fully covered 10bp band.
- Funding histories short; persistence interpreted from R/S gap vs shuffle baseline, supported by DFA and AR(1), not as precisely estimated fractional parameter.

#### 8.3 Model assumptions

- Pure delay omits venue-specific index constituents, clamps, fallback rules.
- Gaussian benchmark omits jumps, skewness, tail dependence.
- Breach simulation uses zero drift because sample drift not statistically different from zero (`|t|<=1.9`). Stress: observed negative drift raises 10x breach probabilities from 0.07/0.17/0.22 to 0.19/0.40/0.36 for BTC/ETH/SOL.
- Exact detection shift (12) and fixed-delay tail (15) freeze funding boundary, fees, position size, maintenance tier during detection. If quantities evolve, liquidation governed by time-varying boundary (11); incremental funding changes bad-debt threshold (16). Formulas identify isolated price-latency channel, not exact venue rule under continuously evolving funding.

#### 8.4 Forecast evaluation

- 63-81 forecast origins per leverage-horizon cell; realized horizons overlap.
- Descriptive out-of-sample test, not large-sample forecast-encompassing test.
- Public path sufficient to score hypothetical isolated-margin event (21), not venue-specific mark detection, user collateral, liquidation fees, insurance-fund losses.
- High-leverage weekly comparison rejects uniform dominance over historical simulation.

#### 8.5 Later-snapshot sensitivity

- Later hourly window overlaps primary by ~21 days; longer funding history largely contains primary period. Rolling-window sensitivity, not independent holdout.
- Table 9:
  - Hourly volatility: BTC 0.0044/0.0052; ETH 0.0058/0.0068; SOL 0.0067/0.0079 (W1/W2).
  - Minute volatility: BTC 0.00031/0.00035; ETH 0.00037/0.00065; SOL 0.00059/0.00072.
  - Funding Hurst: BTC 0.94/0.92; ETH 0.93/0.81; SOL 0.82/0.80.
  - z vs i.i.d.: BTC 4.8/7.4; ETH 4.9/4.7; SOL 3.1/5.0.
  - Funding AR1: 0.71/0.63; 0.70/0.52; 0.54/0.59.
  - Share at default rate: 0.035/0.024; 0.040/0.024; 0.105/0.066.
  - One-week 10x breach: 0.071/0.123; 0.166/0.231; 0.224/0.303.
  - One-week 20x breach: 0.392/0.464; 0.506/0.568; 0.559/0.620.
  - `sigma sqrt(60)/m`: 0.47/0.54; 0.58/1.01; 0.91/1.11.
  - Fixed P_bd 60min: 0.017/0.032; 0.041/0.160; 0.135/0.184.
  - TWAP P_bd 60min: 0.001/0.005; 0.009/0.196; 0.139/0.257.
  - 10bp depth USD m: 25.7/29.6; 20.5/13.9; 4.4/4.0.
- In W2, 10x breach probabilities rise to 0.12-0.30; cross-asset ordering unchanged; ETH/SOL TWAP bad-debt frequencies exceed fixed-delay values.

#### 8.6 Additional stress scenarios

- Joint volatility-depth stress: `sigma_s = s sigma`, `d_s = d/s^rho`; Theorem 1 raises `P_bd` through `sigma`; (6) raises execution loss through lower depth.
- Directional funding stress uses `q_t F_t` to distinguish long-crowded and short-crowded.
- Maintenance-margin sensitivity replaces `m=0.5%` with venue tiers; latency-margin rule evaluated tier by tier rather than called leverage-independent.
- Remaining checks vary `gamma in [0.25,1]` and `T in {72,168,336}` hours.

### Section 9: Risk-management implications (pp. 31-32)

Risk controls in terms of three measured quantities:
1. Stale displacement `D_t(delta)` and ratio `sigma sqrt(delta)/m` determine fixed-delay benchmark for conditional bad debt.
2. Funding dependence determines whether cumulative drain differs materially from independent-funding benchmark.
3. Liquidation notional and depth determine terminal displacement, average concession, log-equivalent execution loss via (5)-(7); book-shape ordering utilisation-dependent (Proposition 3).

Operational recommendations:
1. Faster price detection or larger maintenance cushion reduces loss hidden before liquidation.
2. Leverage limits control how often boundary is reached; latency controls address loss after crossing.
3. Funding stress assessed in direction of position and over forecast horizon, not unconditional average rate.
4. Liquidation size compared with executable depth using average execution price, not only last book level consumed.

Disclosure recommendations: mark rule, maximum fallback delay, funding rule, maintenance schedule, liquidation fees, usable depth assumptions.

Dynamic maintenance schedule:

```
m_t = m_0 + a_1 sigma_t + a_2 |F_t| + a_3 sigma_t sqrt(delta) + a_4 phi'_t(N_t)
```

- Delay term enters through `sigma sqrt(delta)`, the quantity Theorem 1 identifies.
- For DeFi protocols, oracle design is a two-risk trade-off: slower, smoother oracle reduces manipulation but raises catch-up risk. Theorem 2 formalizes: relevant object is crossing-conditioned overshoot, not fixed-time dispersion. TWAP of window `delta` carries ~1.6x expected catch-up overshoot of same-window fixed delay, and in regime `sigma sqrt(delta)/m >~0.89` enlarges bad-debt tail. Design lever is crossing-conditioned overshoot; smoothing window trades manipulation cost against `c_O sigma sqrt(delta)`, not naive `delta/3`, which understates latency cost of smoothing by nearly factor 3.
- Regulators: disclose mark formula, maximum delay, smoothing window, funding caps, liquidation fees, insurance-fund rules.

### Section 10: Conclusions and future research (pp. 31-33)

- Liquidation risk depends not only on leverage and volatility but also on how the exchange observes prices.
- Delayed mark records liquidation after executable price has crossed boundary.
- Fixed-delay benchmark: expected catch-up overshoot `sigma sqrt(delta/(2 pi))`; conditional bad-debt probability governed by `sigma sqrt(delta)/m`.
- Leverage changes frequency of boundary crossings but not conditional bad-debt probability once liquidation occurs, under model assumptions.
- TWAP smoothing does not remove latency cost: liquidation selects adverse stale errors at boundary downcrossings; stationary approximation gives mean overshoot ~1.6x same-window fixed delay. Path simulation confirms overshoot scale but shows approximate TWAP bad-debt probability should not be treated as exact first-passage result.
- Public-data calibration: positive funding dependence vs shuffle benchmark; leverage-sensitive breach probabilities; higher conditional bad-debt risk as delay increases.
- Rolling public-path evaluation: structural forecast improves on historical simulation at moderate leverage and short horizons, but not uniformly at one-week high leverage.
- Funding dependence material mainly under stressed funding.
- Quantities are calibrated stress measures, not historical liquidation frequencies.
- Practical implication: margin design, mark construction, executable depth assessed jointly. Exchanges should disclose mark formulas, smoothing windows, fallback delays; evaluate maintenance margins against crossing-conditioned stale-price risk, not fixed-time mark variance alone.
- Future research:
  1. Replace stationary Gaussian innovation with conditionally heteroskedastic, jump-aware, or regime-sensitive density; longer non-overlapping samples.
  2. Timestamped historical marks and anonymized account-level liquidation paths for direct scoring of detection/severity.
  3. VWAP extension: volume/depth endogenous to stress; requires joint price-volume crossing law, not deterministic weights.
  4. Allow funding boundary `b_L(g_t)`, tiered maintenance `m(L)`, execution concession to evolve during detection, so benchmark correction (16) can be evaluated without freezing non-price drains.

### Appendices A-C (pp. 33-36)

**Appendix A.1 General mark operators.**

- Log-mark kernel average `log M_t = integral_0^inf K(a) X_{t-a} da + eta_t`, `integral K=1`.
- Fixed delay = Dirac mass at `delta`; moving average = uniform/exponential kernel.
- Unconditional stale-error dispersion defines variance-equivalent delay `delta_eff = Var(D_t)/sigma^2` (`delta/3` for uniform TWAP, `tau/2` for exponential kernel of mean lag `tau`); right scale for fixed-time questions such as manipulation cost.
- Liquidation-time cost different: any absolutely continuous kernel makes `log M_t` differentiable, so boundary downcrossings sample local slope with Kac-Rice size-biasing; stale error at crossing biased adverse as in Theorem 2; only pure-delay Dirac escapes via strong Markov.
- Exponential kernel: `d log M_t/dt = (X_t - log M_t)/tau = D_t/tau`; crossing slope proportional to stale error; size-biased crossing law `D^{tau_M} = -sigma sqrt(tau/2) R` with no Gaussian residual; `E[O_exp] = sqrt(pi/2) sigma sqrt(tau/2) ~= 0.886 sigma sqrt(tau)`; `P_exp_bd = P(R > kappa sqrt(2)) = exp(-log^2(1-m)/(sigma^2 tau))`, about 2.2x fixed-delay overshoot at same mean lag. Uniform and exponential examples show fixed-time dispersion need not describe liquidation-time error; no ordering for every kernel.

**Appendix A.2 VWAP extension.**

- `log M^{VWAP}_t = integral_{t-delta}^t V_u X_u du / integral_{t-delta}^t V_u du`, with transaction-volume or executable-depth weight `V_u`.
- Unlike deterministic TWAP, weights stochastic and can covary with returns and crossing slope. Valid liquidation-time law must condition jointly on price, volume, VWAP downcrossing; average historical volume weights would miss liquidity selection. Left for future work.

**Appendix A.3 Depth and impact.**

- `L(y)=a y^gamma`, terminal displacement `y(z)=(z/a)^{1/gamma}`, total concession `Phi(z)=integral_0^z (u/a)^{1/gamma} du = (gamma/(1+gamma)) a^{-1/gamma} z^{1+1/gamma}`, strictly convex for `gamma<1`.
- Dividing by `z` gives average fractional concession. Anchoring `a` to band depth `D=a b^gamma` yields (5)-(7).
- Bad-debt calculation uses log-equivalent concession, not terminal book level.

**Appendix B Computational validation protocol.**

- Deterministic simulation settings and public input data; Wilson intervals.
- Minute-scale paths liquidated on delayed and TWAP marks (~1e5 liquidations per asset-window cell; Table A1).
- Fixed-delay overshoot within 1.3% of formula; bad-debt frequency within 3% in resolved cells.
- TWAP mean overshoot within 5% of stationary approximation; bad-debt frequency 15-40% higher in stressed cells; discrepancy combines first-passage selection and discrete monitoring.
- Separate distributional checks cover half-normal overshoot mean, discrete-TWAP dispersion, `T^{2H+2}` scaling.

Table A1 key cells:
- BTC 60min: `E[O] MC .0009317`, theorem .0009436; `P_bd MC .017`, theorem .017; `E[OTW] MC .001569`, theorem .001508; `P_TWAP MC .001`, theorem .001, naive 1.2e-4.
- ETH 60min: MC .001153, theorem .001152; P_bd .042/.041; TWAP MC .001919, theorem .001841; P_TWAP .013/.009/.001.
- SOL 60min: MC .001827, theorem .001812; P_bd .136/.135; TWAP MC .00303, theorem .002895; P_TWAP .164/.139/.028.

**Appendix C Variable dictionary.** `X_t`, `M_t`; `D_t(delta)`; `F_t`; `y_gamma`, `phi_bar_gamma`, `ell_gamma`; `b_L`, `d_L`, `xi(L)`; `O(delta)`, `P_bd`; `delta_eff`; `R`, `c_O`, `P_TWAP_bd`; `Pi(T;m,delta,H,Phi)`; `P{tau^price_m(delta)<=T}`.

### References (pp. 35-36)

Key: Ackerer-Hugonnier-Jermann; He-Manela-Ross-von Wachter; Mackinga-Nadahalli-Wattenhofer; Donier-Bonart; Merton; Black-Cox; Leland; Longstaff-Schwartz; Almgren-Chriss; Avellaneda-Stoikov; Cartea-Jaimungal-Penalva; Cont; Hasbrouck; Kyle; Makarov-Schoar; Brauneis et al.; Mandelbrot-Van Ness; Cont; Gatheral et al.; Takaishi; Cheridito et al.; Hu-Nualart; Black; Brunnermeier-Pedersen; Qin et al.; Werner et al.; Lehar-Parlour; Klages-Mundt-Minca; Gudgeon et al.; Schär; Anadu et al.; Gorton et al.; Liu-Tsyvinski; Griffin-Shams; Qin et al.; Gneiting-Raftery; Diebold et al.; Cheng et al.; Kim-Park; Donnelly-Lin-Lorig; Burnashev-Tchamkerten; Binance; DeFiLlama; Deribit.

## Equation ledger

| # | Object | Expression | Role |
|---|---|---|---|
| 1 | Mark operator | `M_t = O_{delta,K,eta}(S)_t`; pure delay `log M_t = X_{(t-delta)^+}+eta_t` | Observation functional |
| 2 | Stale error | `D_t(delta)=X_t-X_{(t-delta)^+}-eta_t`; general `D_t(K,eta)=X_t-log M_t` | Hidden displacement |
| 3 | Funding process | `dF_t=-kappa F_t dt+sigma_F dB^H_t+chi dQ_t` | Persistent funding |
| 4 | Funding cash flow | `dCF_t=-q_t S_t F_t dt` | Collateral drain |
| 5 | Depth | `L(y)=a y^gamma`; `D=a b^gamma` | Book shape |
| 6 | Terminal displacement | `y_gamma(z;D)=b(z/D)^{1/gamma}` | Endpoint |
| 7 | Average concession | `phi_bar_gamma(z;D)=gamma/(1+gamma) b(z/D)^{1/gamma}` | Average execution price |
| 8 | Log concession | `ell_gamma(z;D)=-log(1-phi_bar_gamma)` | Log-return loss |
| 9 | Liquidation boundary | `rho*=(1-1/L)/(1-m)`, `d_L=(1/L-m)/(1-m)`, `b_L=log(1-d_L)` | Isolated margin |
| 10 | Residual equity | `xi(L)=m(L-1)/(1-m)=mL rho*` | Equity at boundary |
| 11 | Surplus | `Y^{delta,G}/C_0=1+L(M/P_0-1)-g_t-mLM/P_0` | Margin state |
| 12 | Time-varying boundary | `b_L(g_t)=log((1-1/L+g_t/L)/(1-m))` | Funding-adjusted |
| 13 | First-passage time | `tau_m(delta,G)=inf{t:X_{(t-delta)^+}+eta_t<=b_L(g_t)}` | Event |
| 14 | Breach probability | `Pi(T;m,delta,H,Phi)=P(tau_m<=T)` | Forecast object |
| 15 | Fixed-boundary shift | `tau^price_m(delta)=tau^price_m(0)+delta`; `P{tau(delta)<=T}=P{tau(0)<=T-delta}` | Pure delay benchmark |
| 16 | Dispersion lemma | `E[D_t(delta)^2]=E[<X>_t-<X>_{t-delta}]` | Delay increases dispersion |
| 17 | Stale equity error | `Y_t-Ytilde_t=(1-m)Ntilde_t(e^{D_t}-1)` | Prop 1 |
| 18 | Funding variance | `Var(integral_0^T F_t dt)=sigma_F^2/(2H+2) T^{2H+2}` | Prop 2 |
| 19 | Comparison principle | `I^(2)>=I^(1)` implies breach prob order | Prop 3 |
| 20 | Fixed-delay overshoot | `E[O(delta)]=sigma sqrt(delta/(2 pi))` | Theorem 1 |
| 21 | Bad-debt probability | `P_bd(delta)=Phi(log(1-m)/(sigma sqrt(delta)))` | Theorem 1 |
| 22 | Latency-margin ratio | `sigma sqrt(delta)/m` | Dimensionless driver |
| 23 | Depth-adjusted threshold | `Delta X_delta < log(1-m)+ell_gamma(z;D)` | Theorem 1 depth |
| 24 | Funding-adjusted threshold | `Delta X_delta < log(1-m)+ell_gamma+log(1+g_delta/(L-1))` | Remark 4 |
| 25 | Latency-margin constraint | `sigma sqrt(delta)<=-log(1-m)/Phi^{-1}(1-p*)` | Corollary 1 |
| 26 | TWAP variance | `Var(D^{TWAP})=sigma^2 delta/3`; `Cov=sigma^2 delta/2`; conditional `Var=sigma^2 delta/12` | Lemma 2 |
| 27 | TWAP crossing law | `D^{TWAP}_{tau_M}=sigma sqrt(delta)(-R/2+Z/sqrt(12))` | Theorem 2 |
| 28 | TWAP overshoot | `E[O^{TWAP}]=c_O sigma sqrt(delta)`, `c_O=0.637` | Theorem 2 |
| 29 | TWAP bad debt | `P^{TWAP}_bd=P(R/2-Z/sqrt(12)>-log(1-m)/(sigma sqrt(delta)))` | Theorem 2 |
| 30 | TWAP/fixed ratio | `c_O sqrt(2 pi)~=1.60`; equivalent delay `~2.6 delta`; crossover `~0.89` | Corollary 3 |
| 31 | Breach forecast | `Y_{t,h,L}=1{min_j[1+L(rho-1)-L sum rho fbar - mL rho]<=0}` | Rolling target |
| 32 | Dynamic margin | `m_t=m_0+a_1 sigma+a_2|F|+a_3 sigma sqrt(delta)+a_4 phi'(N)` | Risk management |
| 33 | Exponential kernel overshoot | `E[O_exp]=sqrt(pi/2) sigma sqrt(tau/2)~=0.886 sigma sqrt(tau)` | Appendix A.1 |

## Paper-specific findings

### The central idea

**Liquidation is a stopping problem observed through a coarsened filtration.** The exchange does not see the executable price continuously; it sees a delayed or smoothed mark. The gap between the executable filtration and the mark filtration is the source of hidden loss. This is a fresh lens: most liquidation models treat the mark as the price, or treat latency as a data-quality issue. Here latency is inside the stopping rule.

### The three mechanisms

1. **Boundary frequency:** leverage and funding determine how often the executable price reaches the maintenance boundary. Higher leverage -> closer boundary -> more breaches. Funding drains collateral and moves the boundary.
2. **Detection severity:** mark delay/smoothing determines the hidden price move at detection. Fixed delay -> `sqrt(delta)` overshoot, Gaussian bad-debt tail in `sigma sqrt(delta)/m`. TWAP -> crossing-conditioned adverse selection, ~1.6x fixed-delay overshoot, crossover at `~0.89`.
3. **Execution severity:** finite depth converts liquidation flow into terminal displacement and average concession; convex depth amplifies loss when utilisation is high.

The forecast is the product of these three, not a single volatility forecast.

### The leverage independence result

Under constant maintenance, fixed non-price drains, and no execution concession, conditional bad-debt probability is **independent of leverage**. Leverage changes how often you hit the boundary, not how bad the negative equity is once you hit it. This is exact only in the benchmark. Tiered `m(L)`, funding during detection, fees, and execution all restore leverage dependence. This is a useful warning against overclaiming "leverage doesn't matter for bad debt."

### The TWAP warning

The naive intuition: a TWAP of window `delta` has one third of the fixed-delay variance, so it is safer. The paper shows this is wrong at liquidation time:
- Unconditional stale-error variance: `sigma^2 delta/3`.
- But liquidation occurs at a **downcrossing** of the smoothed mark; the trailing window must have fallen. This conditions the error adversely.
- Stationary-crossing approximation: mean stale error `-0.627 sigma sqrt(delta)`; mean overshoot `0.637 sigma sqrt(delta)`; ratio to fixed delay `1.60`; equivalent fixed delay `2.6 delta`.
- Bad-debt crossover at latency-margin ratio `~0.89`: above it, TWAP has a larger bad-debt tail; below it, smaller.
- Path simulation confirms overshoot scale (within 5%) but bad-debt frequency is 15-40% above approximation in stressed cells.
- Practical lesson: don't evaluate a smoothing window by its fixed-time variance. Evaluate it at the mark's own crossing.

### Funding memory

- Fractional funding without mean reversion: cumulative-drain variance scales as `T^{2H+2}`, steeper than Brownian `T^3`.
- AR(1) funding with `phi=0.71` inflates one-week cumulative-drain variance by `(1+phi)/(1-phi) ~= 5.9` without changing the long-horizon exponent.
- Empirically, R/S Hurst 0.82-0.94, 3.1-4.9 surrogate s.d. above i.i.d. baseline; AR(1) 0.54-0.71. But n=200, wide CIs; only positive serial dependence is inferred, not a universal fractional law.
- Funding memory translates into breach probability mainly in crowded regimes: at 20x calm funding, premium ~0; at x60, +0.010 to +0.020 for AR(1), with fractional significant for BTC only.
- The stress variable is `q_t F_t`, not `F_t`; direction matters.

### Depth amplification

- Terminal displacement and average concession are distinct. Do not use the last book level as the average execution price.
- For a power-law book, terminal `y_gamma = b(z/D)^{1/gamma}`, average `phi_bar = gamma/(1+gamma) y_gamma`, log-equivalent `ell=-log(1-phi_bar)`.
- At `z/D=2`, `gamma=0.5` gives 200bp terminal vs 100bp for `gamma=1`; average concessions 66.7bp vs 50bp.
- SOL's 10bp depth is USD 4.4m vs BTC 25.7m and ETH 20.5m; same dollar liquidation reaches much higher utilisation on SOL. This drives cross-asset bad-debt differences.
- The 50bp BTC depth is capped by the 1000-level endpoint; BTC is a lower bound, so the true BTC-SOL gap is at least as large.

### The empirical calibration

- One-week breach probabilities at `m=0.5%`: 0.07-0.23 at 10x; 0.39-0.56 at 20x; 0.76-0.83 at 50x.
- Cross-asset ordering: SOL > ETH > BTC, consistent with volatility.
- Mean time-to-liquidation: ~80h at 20x, ~30h at 50x.
- Fixed-delay bad-debt at 60min: BTC 0.017, ETH 0.041, SOL 0.135. TWAP approximation: 0.001, 0.009, 0.139. Mechanism TWAP at SOL 1h: 0.164 vs fixed 0.136.
- Out-of-sample: structural model beats historical simulation at 10x all horizons, and at 24h all leverages; historical simulation wins at one-week 20x/50x. Not uniform dominance.

### The honest claim boundaries

- The paper is explicit: these are model-consistent hypothetical-account forecasts, not venue-level customer liquidations.
- Public data do not identify user leverage, collateral, mark history, liquidation paths, insurance-fund transfers.
- Depth is a snapshot, not a historical panel.
- The TWAP result is an approximation, not an exact first-passage law.
- The Gaussian benchmark is a tractable core, not a claim that crypto returns are Gaussian.
- Stablecoin and option data are context only.

## Critical review and limitations

### Data and sample

1. **Short sample.** 1,000 hourly observations per asset (about 42 days), 200 native 8-hourly funding records, one market regime (3 May-14 June 2026). No crisis; results may change in stress.
2. **Single venue.** Binance USD-M only. Cross-venue mark rules, maintenance tiers, insurance funds, and depth differ.
3. **Public data limitations.** No user-level leverage, collateral, liquidation paths, historical mark construction, or insurance-fund transfers. The breach probabilities are calibrated mechanism estimates, not observed venue frequencies.
4. **Depth is a snapshot.** One observation per asset; depth may decline under stress. The 1000-level endpoint caps BTC's 50bp depth, making it a lower bound and leaving BTC-ETH ordering at 50bp unidentified. Cross-asset comparisons use the 10bp band only.
5. **Funding history short.** 200 observations; R/S Hurst CIs wide; point estimates method-sensitive. The paper correctly does not claim a universal fractional law.
6. **Later snapshot overlaps.** The 4 July 2026 window overlaps the primary window by ~21 days and is a sensitivity check, not an independent holdout.

### Model assumptions

7. **Gaussian benchmark.** The fixed-delay theorem uses a local Gaussian strong-Markov assumption. Crypto returns have jumps, skew, and tail dependence; the paper stresses the boundary beyond the Gaussian core but does not prove the theorem under heavy tails.
8. **Zero drift.** The breach simulation uses zero drift because sample drift is not significant (`|t|<=1.9`). Observed negative drift raises 10x breach probabilities from 0.07/0.17/0.22 to 0.19/0.40/0.36. This is a material sensitivity.
9. **Constant maintenance.** The leverage-independence result assumes constant `m`; real venues use tiered maintenance. Tiered `m(L)` restores leverage dependence. The flat `m=0.5%` is a conservative lower bound on breach frequency at high leverage.
10. **Fixed non-price drains during detection.** The exact time shift and fixed-delay tail freeze funding, fees, position size, and maintenance tier during the detection interval. With evolving funding, the boundary moves; equation (16) adjusts the threshold but is not a full dynamic solution.
11. **Isolated margin, long only.** The calibration is a one-sided long isolated-margin account. Short positions are symmetric in principle but not separately calibrated; cross-margin, portfolio margin, and multi-asset collateral are not modeled.
12. **No insurance fund, ADL, or socialized losses.** The bad-debt probability is negative equity at liquidation, not the final loss after insurance fund, auto-deleveraging, or clawbacks.
13. **No funding-cap or funding-timing interaction.** Funding is modeled as a persistent drain; the protocol's cap, interval, and observability (Paper 4/Paper 6) are not part of the stopping rule.
14. **No mark manipulation.** The model assumes an honest but delayed/smoothed mark. Adversarial oracle manipulation (Mackinga et al.) is a separate risk.
15. **No execution queue or MEV.** Liquidation is assumed to execute against the book at the average concession; priority fees, MEV, and liquidation competition are not modeled.
16. **TWAP approximation.** The stationary-downcrossing law is not an exact first-passage result; path simulation shows bad-debt frequency 15-40% above approximation in stressed cells. The paper labels it an approximation.
17. **Exponential/VWAP kernels not calibrated.** Appendix A gives formulas for exponential kernels and a VWAP extension, but the empirical work uses uniform TWAP.
18. **No cross-asset correlation or portfolio effects.** Each asset is calibrated separately; correlated liquidations and cross-margin portfolios are not modeled.
19. **No stablecoin depeg or collateral haircut.** Stablecoin/option data are contextual only.
20. **No regulatory/tax treatment.**

### Forecast evaluation

21. **Small number of origins.** 63-81 forecast origins per leverage-horizon cell; realized horizons overlap. Descriptive out-of-sample test, not a large-sample encompassing test.
22. **High-leverage weekly failure.** Historical simulation beats the structural model at 20x/50x one-week. The paper is honest that this rejects uniform dominance.
23. **Only event component scored.** Public data cannot score mark detection, user collateral, liquidation fees, or insurance-fund losses; the conditional severity is validated by mechanism simulation, not by observed liquidations.
24. **Model-consistent target.** `Y_{t,h,L}` is a hypothetical account outcome on public paths, not an exchange liquidation record.
25. **No benchmark against a jump/regime model.** The paper suggests conditionally heteroskedastic/jump-aware densities as future work; the current comparison is against historical simulation only.

### Internal consistency / presentation

26. **Table 5 P_TWAP for BTC 60min = 0.001 while mechanism MC is also 0.001; SOL 60min = 0.139 vs MC 0.164.** The paper explains the 15-40% gap as first-passage/discrete-monitoring error. Fine, but do not treat the approximation as exact.
27. **"Leverage independence" is a headline that can be misread.** It applies only to conditional bad-debt probability under constant `m`, fixed drains, zero execution concession. The paper says this repeatedly; a pitch must too.
28. **The later snapshot is not independent.** It overlaps the primary window; don't call it a holdout.
29. **The depth table's BTC 50bp cell is capped.** The paper flags it; the BTC-ETH ordering at 50bp is unidentified. Use 10bp for cross-asset claims.

### What the paper does not claim

- It does not claim to observe or predict actual exchange customer liquidations.
- It does not claim uniform forecast dominance over historical simulation.
- It does not claim crypto returns are Gaussian.
- It does not claim the TWAP formula is exact.
- It does not claim leverage is irrelevant to liquidation risk; it separates breach frequency from conditional bad-debt severity.
- It does not claim stablecoin supply or options cause liquidation risk.
- It does not claim the model captures insurance-fund or ADL losses.

## Hackathon prep for this paper

### What this paper supports building

1. **A liquidation-tail oracle.** Publish horizon-specific breach probability, conditional overshoot distribution, bad-debt probability, and a mark-quality score for a given venue/asset/leverage/maintenance configuration.
2. **A mark-latency auditor.** Compare fixed-delay, TWAP, exponential, and VWAP marks on manipulation resistance and liquidation-time safety. Show that fixed-time variance is the wrong metric.
3. **A dynamic margin engine.** Use `m_t = m_0 + a_1 sigma + a_2|F| + a_3 sigma sqrt(delta) + a_4 phi'(N)` to recommend maintenance margins that target a bad-debt budget.
4. **A depth-aware liquidation simulator.** Map liquidation size to terminal displacement, average concession, and log-equivalent loss; stress thin depth.
5. **A funding-memory stress tool.** Compare i.i.d., AR(1), and fractional funding; show when persistence materially raises breach probability.
6. **A liquidation-tail vault/insurance product.** Price cover for bad-debt events using the forecast distribution.
7. **A venue disclosure scorecard.** Score venues on mark formula transparency, maximum delay, smoothing window, maintenance schedule, funding cap, liquidation fees, and usable depth.
8. **An AI risk copilot.** Explain why a position's liquidation risk is high: boundary proximity, mark latency, funding drain, or thin depth.

### What this paper does not support building directly

- A general payoff compiler. That is Paper 1.
- A cross-venue funding arbitrage router. That is Paper 5.
- A funding-timing/no-arbitrage testability oracle. That is Paper 6.
- A funding-feedback calibration. That is Paper 4.
- A production venue risk engine without user-level data, mark history, and governance integration.
- A claim that the model predicts actual customer liquidations.

### Data and tech needed

- Hourly and minute prices, native funding histories, mark/oracle histories, maintenance tiers, contract specs.
- Order-book depth snapshots (multiple times, not one), liquidation sizes, insurance-fund data.
- Realized volatility, funding persistence (R/S, DFA, AR1), fractional simulation.
- First-passage Monte Carlo engine.
- Fixed-delay and TWAP crossing simulators.
- Depth/impact calibration.
- Proper scoring (Brier, log) and rolling backtest infrastructure.
- Onchain oracles for `LiquidationTail`, `MarkQuality`, `LatencyMarginRatio`, `FundingMemory`, `DepthConcession`.
- A frontend for scenario analysis and venue comparison.

### Demo script (3 minutes)

**Act 1: The hidden loss.** A 20x SOL position looks healthy on a 60-minute delayed mark. The executable price crosses the maintenance boundary, but the mark has not caught up. When it does, liquidation happens at a worse price. Show the overshoot and the bad debt. Compare 1-minute vs 60-minute delay: `P_bd` rises from negligible to 0.135.

**Act 2: The TWAP trap.** A venue switches to a 60-minute TWAP to reduce manipulation. The naive variance heuristic says risk falls by two thirds. Show the crossing-conditioned calculation: overshoot is 1.6x the fixed delay, and SOL bad debt is 0.164 vs 0.136. The "safer" mark is worse at liquidation time.

**Act 3: The fix.** TailMark recommends a dynamic maintenance margin that targets a bad-debt budget, and a mark design with bounded delay and depth-aware liquidation. Re-run the stress: bad debt falls below the target, and the venue publishes the mark-quality score onchain.

### Judge Q&A prep

**"What is the new idea?"** Mark construction is part of the liquidation stopping rule. The cost of latency is a `sqrt(delta)` overshoot and a bad-debt tail in `sigma sqrt(delta)/m`; smoothing does not remove it, and can make it worse at the crossing.

**"Why does this matter?"** Every perp venue chooses a mark rule. Most evaluate it by fixed-time variance or manipulation cost. TailMark evaluates it by liquidation-time safety, which is the risk that actually matters.

**"Isn't leverage the main driver?"** Leverage drives breach frequency. Conditional bad-debt severity is leverage-independent only in the constant-maintenance benchmark. TailMark separates the two and shows where leverage dependence re-enters.

**"How do you avoid overclaiming?"** We publish model-consistent forecasts with uncertainty, label the TWAP result an approximation, and never claim to observe customer liquidations.

**"What is the moat?"** The calibrated mark-latency dataset, the stopping-time formulas, the depth integration, and the oracle integrations.

**"Why would a venue use it?"** To choose a mark design, set maintenance margins, disclose risk, and avoid a hidden bad-debt tail that only shows up in stress.

### Claims to avoid

- "We predict actual liquidations."
- "Leverage doesn't matter for bad debt." Only conditional severity in the benchmark; frequency still depends on leverage.
- "TWAP is safer because variance is one third." Crossing-conditioned overshoot is 1.6x fixed delay.
- "The stationary-crossing formula is exact." It is an approximation; path simulation differs by 15-40% in stressed cells.
- "Our forecast dominates historical simulation." It loses at one-week 20x/50x.
- "Funding memory always matters." It is second-order at calm funding; material in crowded regimes.
- "Stablecoin supply drives liquidations." Context only.

### Track and sponsor fit

- **DeFi/derivatives track:** mark-quality oracle, dynamic margin engine, liquidation-tail forecast.
- **Oracle track:** `LiquidationTail`, `MarkQuality`, `LatencyMarginRatio`.
- **Risk/security track:** mark latency, depth stress, bad-debt forecasting.
- **Data/analytics track:** venue disclosure scorecard, mark comparison.
- **AI/agent track:** risk copilot explaining liquidation tails.
- **Insurance track:** liquidation-tail cover pricing.
- **Ethereum/L2 track:** oracle/mark design for onchain perps.

## Hackathon-level idea for this paper

### TailMark: the liquidation-tail and mark-quality oracle

**One sentence.** TailMark forecasts the probability and hidden severity of a perp liquidation under delayed or smoothed marks, funding memory, and finite depth, and tells venues how to design a safer mark and margin schedule.

**Why it is stronger than a liquidation calculator.** A calculator shows the liquidation price. TailMark shows the **distribution of loss after detection**, the **mark-quality score**, and the **design fix**. It is a risk oracle plus a protocol-design tool.

**Core modules.**

1. **Position intake.** Venue, asset, leverage, collateral, maintenance tier, position size, horizon.
2. **Mark model.** Fixed delay `delta`, TWAP window, exponential kernel, or VWAP; fallback rules.
3. **Breach engine.** First-passage probability `Pi(T;m,delta,H,Phi)` with funding and depth.
4. **Severity engine.** Fixed-delay `E[O]`, `P_bd`; TWAP stationary-crossing `E[O_TWAP]`, `P_TWAP_bd`; path-level correction.
5. **Funding-memory engine.** i.i.d., AR(1), fractional; `T^{2H+2}` scaling; crowded-regime premium.
6. **Depth engine.** Terminal displacement, average concession, log-equivalent loss; depth stress.
7. **Margin engine.** Dynamic `m_t` recommendation targeting a bad-debt budget.
8. **Mark-quality score.** Combining manipulation resistance, fixed-time variance, crossing-conditioned overshoot, and disclosure.
9. **Oracle.** Publish `LiquidationTail`, `BadDebtProb`, `MarkQuality`, `LatencyMarginRatio`, `FundingMemoryPremium`, `DepthConcession`.

**Onchain architecture.**

- `LiquidationTailOracle.sol`: breach probability and severity distribution parameters.
- `MarkQualityOracle.sol`: mark type, delay, smoothing window, crossing-conditioned overshoot, quality score.
- `MarginAdvisor.sol`: dynamic maintenance recommendation and target bad-debt budget.
- `DepthRiskOracle.sol`: depth snapshot, terminal displacement, average concession.
- `FundingMemoryOracle.sol`: persistence regime and stress premium.
- Off-chain keeper: calibrates volatility, funding, depth; runs first-passage/TWAP simulations; signs reports.
- Consumer adapters: perp DEX risk engine, LiquidMesh, insurance vault, regulator dashboard.

**Demo moment.** A 20x SOL position. At 1-minute delay, `P_bd` is negligible; at 60-minute delay, 0.135 fixed / 0.164 TWAP mechanism. The mark-quality panel shows TWAP is 1.6x worse at the crossing than the naive variance heuristic suggests. TailMark recommends a dynamic maintenance margin and a bounded-delay mark. Re-run: `P_bd` falls below the target. Publish the new `MarkQuality` and `LiquidationTail` onchain.

**Moat.** The calibrated mark-latency dataset, the stopping-time formulas, the depth integration, the funding-memory model, and the oracle integrations.

**Risks.** Public-data limitations, Gaussian benchmark, TWAP approximation, short sample, venue rule changes. Publish uncertainty and label model-consistent forecasts.

**Integration with the existing portfolio.** TailMark is the **mark-quality and loss-severity layer** of the Perp Forge stack:
- **Perp Forge** designs the payoff and funding.
- **Funding DNA** calibrates effective funding feedback.
- **Clockwork** checks funding-timing and no-arbitrage testability.
- **Basis Mesh** anchors the price.
- **LiquidMesh** sizes the hedge against liquidation probability.
- **TailMark** forecasts the loss severity and mark risk.
- **TierBridge** executes the cross-venue trade.

**Score.** Novelty 5, technical 5, demo 5, feasibility 4, judge 5, moat 5. Total 29.

## Connection to the other papers

- **Paper 1 (Angeris et al.):** general funding and replication. TailMark focuses on the mark/oracle observation layer that Paper 1 abstracts away.
- **Paper 2 (He et al.):** no-arbitrage and basis. TailMark adds mark latency and liquidation severity to the basis story.
- **Paper 3 (Alexander et al.):** liquidation-aware hedging. LiquidMesh sizes the hedge; TailMark forecasts the severity distribution and mark-quality component.
- **Paper 4 (Zhang):** funding feedback. TailMark's funding-memory engine is a complement: persistence and direction matter for collateral drain.
- **Paper 5 (Zhivkov):** cross-venue execution. TailMark's depth and bad-debt forecasts inform TierBridge's route and exit decisions.
- **Paper 6 (Erez-Smirnov):** funding timing and no-arbitrage testability. Clockwork asks whether the bound is testable; TailMark asks how bad the loss is when the mark detects the breach.
- **Synthesis:** Paper 1 designs; Paper 2 anchors; Paper 3 survives; Paper 4 calibrates; Paper 5 executes; Paper 6 tests; Paper 7 prices the tail.

**Full ideation:** See [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md), especially the new Wave 5 sections on TailMark, the mark-quality oracle, the liquidation-tail vault, and the Perp Forge 5.0 stack.
