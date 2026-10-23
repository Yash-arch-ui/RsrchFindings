# A Primer on Perpetuals

## Metadata

- **Authors:** Guillermo Angeris (Bain Capital Crypto), Tarun Chitra (Gauntlet Networks), Alex Evans (Bain Capital Crypto), Matthew Lorig (University of Washington)
- **Date:** September 8, 2022
- **Identifier:** arXiv:2209.03307v1 [q-fin.MF]
- **Length:** 13 pages
- **Type:** Pure theory / security design. No empirics, no data, no code.
- **Read status:** Full paper read, including all theorems, proofs, examples, and references.

## One-paragraph summary

The paper defines perpetual contracts in a continuous-time, no-arbitrage, frictionless market. It derives model-free expressions for the funding rate of a perp with funding and the discount rate of a perp with discounting. It shows that the short side can replicate the contract using delta hedging, with the funding rate equal to the payoff's convexity (gamma) minus a financing term. It then extends the results to jump-diffusion markets and gives an explicit replication strategy using European power options when the jump measure is finite and volatility is independent. Examples link the framework to variance swaps, leveraged ETFs, Squeeth-style power perps, and constant-function market maker (CFMM) LP positions.

## Section-by-section findings

### Abstract and Introduction (pp. 1-2)

- Defines two contract types:
  1. **Perpetual with funding:** the long side can terminate at any time `t`, receives `phi(S_t)`, pays `phi(S_0)` at inception, and pays a continuous funding rate `F_t` to the short side until termination.
  2. **Perpetual with discounting:** no funding payments; the payoff at termination is discounted by `exp(-integral_0^t D_s ds)`.
- Assumes no arbitrage, no transaction costs, continuous and strictly positive asset prices.
- Derives model-free funding and discount rates.
- Provides replication strategies for the short side.
- Extends to jumps: funding and discount rates are semi-robust because they do not depend on the dynamics of the volatility process, but they do depend on jump intensity under the pricing measure.
- Under an independent volatility process, derives an explicit jump replication strategy.
- Historical framing:
  - Shiller (1993) proposed perps for illiquid dividend-yielding assets.
  - Only linear-payoff perps were originally considered.
  - Crypto perps became popular for leveraged exposure.
  - Miners used quanto futures, then stablecoins enabled the 2016 crypto perp market.
  - Power perps were later proposed; Squeeth (Opyn) is a squared-ETH perp.
- The authors explicitly aim to correct online misinformation about how funding should be computed.

### Section 2: Market model and assumptions (pp. 2-3)

- Filtered probability space `(Omega, F, F, P)`.
- No arbitrage, no transaction costs.
- Money-market account `M` is continuous, strictly positive, non-decreasing:
  ```
  dM_t = r_t M_t dt
  ```
- `n` risky assets with continuous, strictly positive prices:
  ```
  dS_i = mu_i S_i dt + sum_j sigma_ij S_i dW_j
  ```
- `W` is a `d`-dimensional Brownian motion with independent components.
- All local martingales are assumed to be true martingales.

**Finding:** The model is deliberately frictionless. The paper is about pricing, replication, and security design, not market microstructure or empirical deviations.

### Section 3: Perpetual contracts with funding (pp. 3-5)

**Definition 1:** A perp with funding written on `S` with payoff function `phi: R^n -> R` is an agreement where the long side can terminate at any time `t`, receives `phi(S_t)`, pays `phi(S_0)` at inception, and pays a continuous funding rate `F_t` to the short side.

**Theorem 2 (core result):**
```
F_t = (1/2) * sum_i sum_j (sigma sigma^T)_ij S_i S_j d_i d_j phi(S_t)
      - ( phi(S_t) - sum_i S_i d_i phi(S_t) ) r_t
```

- First term: gamma/convexity P&L of the payoff.
- Second term: financing cost on the residual `(phi - S . grad phi)`.
- Proof: construct a self-financing portfolio for the short side.
  - Matching the `dS_i` terms gives the hedge ratios `Delta_i = d_i phi(S_t)`.
  - Matching the `dt` terms gives the funding rate `F_t`.

**Remark 3:** `F_t` can be positive or negative. If negative, the short side pays the long side at rate `-F_t`.

**Remark 4 (model-free form):**
```
F_t dt = (1/2) * sum_i sum_j S_i S_j d_i d_j phi(S_t) d<log S_i, log S_j>_t
         - ( phi(S_t) - sum_i S_i d_i phi(S_t) ) d log M_t
```

- No need to know `mu`, `sigma`, or `r` separately.
- Only observable quadratic variation/covariation and money-market growth are needed.
- This is the most practical claim in the paper: funding is observable from realized second moments.

**Example 5: Variance swap.**

- A continuously monitored variance swap pays:
  ```
  integral_0^T d<log S>_t - K
  ```
- The swap rate is `K = -2 E log(S_T / S_0)` under the pricing measure.
- A perp with payoff `phi(S) = 2 log(S/S_0)` has:
  ```
  F_t dt = -d<log S>_t
  ```
- The long perp's payoff minus funding is:
  ```
  phi(S_T) - integral_0^T F_t dt
  = 2 log(S_T / S_0) + integral_0^T d<log S>_t
  ```
- This is the same exposure as a long variance swap, but without paying the volatility premium.
- This is a clean, immediately understandable use case.

### Section 4: Perpetual contracts with discounting (pp. 5-7)

**Definition 6:** A perp with discounting has no funding. The long side receives `exp(-integral_0^t D_s ds) phi(S_t)` at termination and pays `phi(S_0)` at inception.

**Theorem 7:**
```
D_t = F_t / phi(S_t)
```
where `F_t` is the Theorem 2 funding rate. The payoff `phi` must be strictly positive or strictly negative.

**Example 8: Leveraged ETF / power perp.**

- LETF value:
  ```
  L_t = L_0 (S_t / S_0)^gamma (M_t / M_0)^(1 - gamma)
        * exp( gamma (1 - gamma) / 2 * <log S>_t )
  ```
- A perp with payoff `phi(S) = L_0 (S / S_0)^gamma` has:
  ```
  D_t dt = gamma (gamma - 1) / 2 * d<log S>_t - (1 - gamma) d log M_t
  ```
- Squeeth is the special case `phi(S) = S^2` (gamma = 2) on Opyn.
- This formally links a DeFi perp to a traditional leveraged ETF.

**Example 9: CFMM LP dominance.**

- Geometric-mean CFMM deposit value:
  ```
  V_t = V_0 (S_1 / S_1,0)^p (S_2 / S_2,0)^q,  p + q = 1
  ```
- A perp with the same payoff has:
  ```
  D_t dt = p(p - 1)/2 * d<log S_1>_t
           + q(q - 1)/2 * d<log S_2>_t
           + p q d<log S_1, log S_2>_t
  ```
- The exponent in the discounted payoff is nonnegative:
  ```
  p(1 - p) dx^2 + q(1 - q) dy^2 - 2 p q dx dy
  = (p dx - q dy)^2 >= 0
  ```
- **Finding:** Ignoring CFMM fees, a perp with the same payoff always dominates depositing into the CFMM. This is a strong and provocative result for DeFi design.

### Section 5: Extension to models with jumps (pp. 8-12)

- Sets `r = 0` and considers a single risky asset.
- Dynamics under the pricing measure:
  ```
  dS_t = sigma_{t-} S_{t-} dW_t
         + integral S_{t-} (e^{gamma_t(z)} - 1) N_tilde(dt, dz)
  ```
- `S` is a martingale under the pricing measure (no arbitrage).

**Theorem 10:**
```
F_t = (1/2) sigma_t^2 S_t^2 phi''(S_t)
      + integral [ phi(S_t e^{gamma_t(z)}) - phi(S_t)
                   - S_t (e^{gamma_t(z)} - 1) phi'(S_t) ] nu(dz)
```
and `D_t = F_t / phi(S_t)`.

- Proof: the long-side value must be a martingale; setting the `dt` drift to zero gives the result.

**Remark 11:** Theorem 10 gives funding and discount rates but says nothing about a replication strategy for the short side.

**Additional assumptions for replication (eqs. 15-17):**

- The Levy measure is a finite Dirac comb:
  ```
  nu(dz) = sum_j lambda_j delta_{z_j}(z) dz
  ```
- The volatility process is independent of the Brownian motion and the Poisson random measure.
- European call and put options with any strike at a fixed maturity `T` are tradable.

**Theorem 12: Jump replication.**

- Defines European power contracts `P^(p)` paying `S_T^p`.
- Defines:
  ```
  psi(p) = integral [ (e^{p z} - 1) - p(e^z - 1) ] nu(dz)
  ```
- Constructs:
  ```
  dY^(p)_t = e^{psi(1 - p)(T - t)} S^{1 - p}_{t-} dP^(p)_t
             - e^{psi(p)(T - t)} S^p_{t-} dP^(1 - p)_t
  ```
- Solves for `(Gamma^(p1), ..., Gamma^(pn), Delta)` via an `(n + 1) x (n + 1)` matrix `H` (eqs. 19-22) to replicate `phi(S_t)`.
- Requires knowledge of jump sizes and intensities under the pricing measure.
- Does not require assumptions about the dynamics of the volatility process.

**Remark 13:** The replication strategy works up to the option maturity `T`; at `T`, one can roll into new options with a later maturity.

**Remark 14:** `S`, the power option prices, and `sigma` are observable. No volatility-model assumptions are needed, but jump sizes and intensities under the pricing measure are required.

### References (pp. 12-13)

Key lineage:

- Shiller (1993): perpetual futures.
- Carr and Madan (2001): volatility trading.
- Angeris and Chitra (2020): improved price oracles / CFMMs.
- Evans (2020): liquidity provider returns in geometric mean markets.
- Leung and Santoli (2016): leveraged ETFs.
- Opyn (2021): Squeeth.
- Paradigm (2021): power perpetuals.

## Equation ledger

| Result | Equation |
|---|---|
| Funding rate, continuous prices | `F = 1/2 sum_i sum_j (sigma sigma^T)_ij S_i S_j d_i d_j phi - (phi - sum_i S_i d_i phi) r` |
| Model-free funding | `F dt = 1/2 sum_i sum_j S_i S_j d_i d_j phi d<log S_i, log S_j> - (phi - sum_i S_i d_i phi) d log M` |
| Discount rate | `D = F / phi` |
| Variance-swap perp funding | `F dt = -d<log S>` for `phi = 2 log(S/S_0)` |
| Power/LETF discount | `D dt = gamma(gamma - 1)/2 d<log S> - (1 - gamma) d log M` |
| CFMM discount | `D dt = p(p - 1)/2 d<log S_1> + q(q - 1)/2 d<log S_2> + p q d<log S_1, log S_2>` |
| Jump funding | `F = 1/2 sigma^2 S^2 phi'' + integral [phi(S e^gamma) - phi(S) - S(e^gamma - 1) phi'] nu(dz)` |
| Jump power-option factor | `psi(p) = integral [(e^{p z} - 1) - p(e^z - 1)] nu(dz)` |

## Paper-specific findings

1. **Funding is the no-arbitrage price of convexity.** The formula is a gamma term minus financing; nothing else.
2. **Any smooth payoff can be replicated** by the short side with hedge ratios `Delta = grad phi` and funding `F`.
3. **Discounting perps are equivalent** to funding perps with `D = F / phi`.
4. **Variance, power, and LP-like exposures are special cases** of the same primitive.
5. **CFMM LP deposits are dominated** by a corresponding perp, ignoring fees.
6. **Jump risk is priced through a jump integral**, not through volatility dynamics.
7. **Jump replication is possible but operationally heavy:** it needs power options, known jump sizes/intensities, and independent volatility.
8. **The paper is inverse to exchange reality.** It assumes `phi` is chosen and derives `F`; it does not analyze deviations of market prices from the model.
9. **No costs, no margin, no liquidation, no empirics.** It is a benchmark and design paper.

## Limitations to state honestly

- Frictionless, continuous-time, no transaction costs.
- Continuous positive prices for the main results; jumps are treated separately.
- Replication assumes continuous trading and, in the jump case, liquid power options.
- No margin, liquidation, or funding-cap mechanics.
- No empirical validation.
- No analysis of deviations, arbitrage, or market efficiency.
- The CFMM dominance result ignores fees, which can reverse it in practice.

## Hackathon prep for this paper

### What this paper supports building

- A **payoff-to-funding calculator**: input `phi`, get `F_t`, `D_t`, hedge ratios, and simulated PnL.
- A **structured perp designer**: templates for variance, power/Squeeth, CFMM-LP replacement, and custom payoffs.
- A **funding oracle prototype**: compute model-free funding from realized covariance; optionally deploy a Solidity view function.
- A **research/education demo**: compare a variance perp vs a variance swap, or a power perp vs an LETF.

### What this paper does not support building directly

- A live perp exchange with liquidity.
- A production jump-replication desk (needs power-option liquidity and jump calibration).
- A consumer trading app with real funds.

### Data and tech needed

- Price data: Binance, Coinbase, Chainlink, or Pyth.
- Realized covariance from high-frequency returns.
- Frontend: React/Next.js with Recharts or Plotly.
- Backend: Python (numpy/pandas) or TypeScript.
- Optional onchain: Solidity funding calculator plus an oracle; deploy on Base, Arbitrum, or Sepolia.
- No database is required unless historical covariance is cached.

### Demo script (3 minutes)

1. Pick "Variance perp": show `phi = 2 log(S/S_0)`, `F = -d<log S>`, and the payoff vs variance swap.
2. Pick "Power perp / Squeeth": show `phi = S^2` and `D = gamma(gamma - 1)/2 d<log S>`.
3. Pick "CFMM LP": show the perp's discounted payoff dominates the LP by `(p dx - q dy)^2`.
4. Move a jump slider: show the funding jump integral and how it changes the hedge.
5. Show the exported spec: payoff, funding formula, hedge ratios, recommended oracle inputs.

### Judge Q&A prep

- **"Isn't this just Squeeth?"** Squeeth is one special case (`phi = S^2`). The contribution is the generic `phi -> F` mapping and jump-aware funding.
- **"How do you compute covariance onchain?"** Use a TWAP/realized-variance oracle; update periodically; acknowledge latency and manipulation risks.
- **"Where is the revenue?"** Protocol fees on structured perps, design/analytics SaaS, or licensing the funding oracle.
- **"Why would anyone prefer this to options?"** No premium, capital efficiency, no expiry, but path-dependent funding and liquidation risk.
- **"Is this safe/regulatory?"** Testnet/demo only; derivatives require jurisdiction-specific compliance.

### Claims to avoid

- "Risk-free yield."
- "Works with any asset at any size."
- "Production-ready jump replication."
- "Beats Squeeth" without a liquidity and cost analysis.

### Track and sponsor fit

- DeFi/derivatives infrastructure, onchain perps, L2 deployment, oracle/data sponsors.
- Strong for a **technical depth / innovation** prize; weaker for a mass-consumer prize.

## Hackathon-level idea for this paper

### Perp Forge: a compiler and launchpad for new perpetuals

The original "Payoff Forge" was a no-code designer. The evolved idea is a compiler and launchpad.

**One-liner:** Write a payoff, and Forge produces the funding curve, hedge ratios, jump premium, oracle specification, contract code, test suite, and risk report.

**User:** Quant desks, DeFi protocol designers, researchers, structured-product teams, and hackathon teams who want to launch a novel perp.

**Core flow:**

1. Choose a template (variance, power/Squeeth, CFMM-LP, correlation, binary/jump, or custom `phi`).
2. Forge symbolically computes `F_t`, `D_t`, `Delta = grad phi`, and the jump integral.
3. It generates an oracle specification and a Solidity/Rust skeleton with tests.
4. It runs a Monte Carlo and historical risk report.
5. It deploys the perp to a testnet in one click.

**Differentiator:** This is a compiler, not a calculator. It turns the paper's math into a repeatable instrument factory. It can generate Squeeth, a variance perp, an LP perp, a slashing perp, or a payoff that has never existed.

**Build scope:**

- Payoff DSL plus symbolic differentiator.
- Funding, discount, and jump engines.
- Code generation plus invariant and fuzz tests.
- Oracle-spec generator.
- Historical and Monte Carlo simulator.
- One-click testnet deployment.
- A flagship perp generated by Forge (see the idea lab).

**Judging alignment:** High technical depth, new category, and a demo that ends with a live novel perp.

**Full ideation:** See [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md), especially the Perp Forge, Slashing Perp, Prediction Perp, Shiller Perp, LP Perp, and Correlation Perp sections, plus the Wave 2 Perp Forge 2.0 stack.
