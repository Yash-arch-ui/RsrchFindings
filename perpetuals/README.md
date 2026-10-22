# Perpetuals Research

Paper-wise findings for perpetual futures research. Each paper file contains a full section-by-section summary, equation ledger, limitations, hackathon preparation, and an evolved hackathon idea. The idea lab contains deep dives on new primitives, evolved ideas, moonshots, scoring, and a recommended build portfolio.

## Papers and idea lab

| # | File | Paper | Type | Key result | Hackathon angle |
|---|---|---|---|---|---|
| 1 | [01-angeris-2022-primer-on-perpetuals.md](01-angeris-2022-primer-on-perpetuals.md) | *A Primer on Perpetuals* (Angeris, Chitra, Evans, Lorig, 2022) | Pure theory / security design | Model-free funding `F = 1/2 sum (sigma sigma^T)_ij S_i S_j d_i d_j phi - (phi - S . grad phi) r`; any smooth payoff is replicable with `Delta = grad phi` | Perp Forge: compiler and launchpad for new perpetuals |
| 2 | [02-he-2024-fundamentals-of-perpetual-futures.md](02-he-2024-fundamentals-of-perpetual-futures.md) | *Fundamentals of Perpetual Futures* (He, Manela, Ross, von Wachter, 2024) | Theory plus empirical asset pricing | Random-maturity no-arbitrage price `F = (1 - Phi^{-1}(r))^{-1} S`; clamp-aware Binance benchmark; deviations 52-90% per year | Basis Mesh: cross-venue basis intelligence, oracle, and copilot |
| 3 | [03-alexander-2023-hedging-liquidation-leverage.md](03-alexander-2023-hedging-liquidation-leverage.md) | *Hedging with Automatic Liquidation and Leverage Selection on Bitcoin Futures* (Alexander, Deng, Zou, 2023) | Applied theory plus empirical microstructure | Dual-objective optimal hedge `theta* = omega theta_0`; liquidation probability cut to 0.1-3%; optimal leverage mostly below 5x; inverse perps slightly better at longer horizons | LiquidMesh: liquidation-aware hedge autopilot and margin oracle |
| 4 | [04-zhang-2026-shared-template-funding.md](04-zhang-2026-shared-template-funding.md) | *A Shared Template Without Shared Feedback: Funding Rates in Cryptocurrency Perpetual Futures* (He, Wang, Zhang, 2026) | Theory plus empirical asset pricing | Capital-dependent stability band; weak feedback leaves gaps persistent, intermediate stabilises, strong feedback generates expanding reversals; 200 Binance USDT-margined perps | Funding DNA: effective-feedback oracle and funding-rule compiler |
| 5 | [05-zhivkov-2026-two-tiered-funding-markets.md](05-zhivkov-2026-two-tiered-funding-markets.md) | *The Two-Tiered Structure of Cryptocurrency Funding Rate Markets* (Zhivkov, 2026) | Empirical market microstructure / statistical arbitrage | CEX leads DEX; 61% higher CEX integration; 17% of observations show >=20 bps spreads; only 8/20 top trades profitable after costs; average Sharpe -7.40 | TierBridge: execution-aware CEX-DEX funding arbitrage router and two-tier oracle |
| 6 | [06-hackathon-idea-lab.md](06-hackathon-idea-lab.md) | Idea lab | Ideation | N/A | Perp Forge 3.0 plus Basis Mesh, Funding DNA, LiquidMesh, and TierBridge as the full risk-and-execution stack |

## Quick comparison

| Dimension | Paper 1 | Paper 2 | Paper 3 | Paper 4 | Paper 5 |
|---|---|---|---|---|---|
| Core object | Payoff `phi(S)` to funding `F` | Fixed exchange funding `Phi` to price benchmark | Hedge ratio `theta` to liquidation probability | Nominal funding template to effective feedback | Cross-venue funding spread to executable net spread |
| Perspective | Security design (inverse problem) | Asset pricing and arbitrage | Risk management and market microstructure | Mechanism design and control | Empirical market microstructure and execution |
| Empirics | None | Binance, 2020-2024, BTC/ETH/BNB/DOGE/ADA | Coinbase spot, 7 perps, 5 exchanges, minute data, 2020-2021 | 200 Binance USDT-margined perps, 2026 | 26 exchanges (11 CEX, 15 DEX), 749 symbols, 35.7M one-minute observations, 8 days, Nov 2025 |
| Trading costs | Ignored | Explicit bounds `+/- C` | No fees; funding small; liquidation explicit | Caps, flat bands, interval averaging, pre-settlement trading | Explicit fees, slippage, gas, latency; cost-adjusted P&L |
| Jumps | Theorems 10 and 12 | Not modeled | Right-tail GEV; liquidation jumps | Not the focus; liquidation and basis reversals | Not modeled; spread reversals and forced exits |
| Margin/liquidation | Not modeled | Liquidation-risk bounds | Central: automatic liquidation, leverage choice, loss aversion | Central: liquidation reduces corrective capacity | Mentioned as risk, not modeled in the portfolio |
| Main practical output | Funding and replication for any payoff | No-arbitrage band, deviation `rho`, strategy | Optimal hedge, survival probability, implied leverage | Stability band, effective-funding diagnostics, rule compiler | CEX-DEX lead-lag, executable spread, reversal hazard, routed trades |
| Hackathon strength | Technical depth and originality | Demoability and product clarity | Crisis demo and risk credibility | Mechanism-design novelty and oracle moat | Live execution demo and a memorable negative result |

## Recommended direction

Build **Perp Forge 3.0** as the core platform, launch one flagship perp generated by Forge (Slashing Perp, Prediction Perp, or Shiller Perp), and use four oracles plus an execution layer:

- **Basis Mesh / Basis Oracle** for the no-arbitrage band and basis signal.
- **Funding DNA** for effective-funding calibration and funding-rule compilation.
- **LiquidMesh** for liquidation-aware position sizing and survival probability.
- **TierBridge / Tier Oracle** for CEX-DEX lead-lag, executable spread, reversal hazard, and routed execution.

This combines the technical depth of Paper 1, the market signal and risk framework of Paper 2, the survival-aware hedging of Paper 3, the mechanism-design insight of Paper 4, and the cross-venue execution realism of Paper 5. The narrative is: **design the payoff, anchor the price, calibrate the funding, keep the hedge alive, and execute the trade.**

See [06-hackathon-idea-lab.md](06-hackathon-idea-lab.md) for the full idea set, Wave 2 and Wave 3 additions, scoring matrix, team split for five humans and ten coding agents, three-act demo, judge Q&A, and kill criteria.

## Access note on Paper 4

The full SSRN PDF for Paper 4 could not be retrieved because SSRN's content-protection layer blocked direct, archived, and proxied downloads. The Paper 4 file separates verified abstract and metadata claims from a clearly marked analytical reconstruction. Do not cite the reconstruction as the authors' model.

## Data and reproducibility note on Paper 5

Paper 5's data are openly available at `https://loris.tools/funding/historical` (accessed 15 November 2025). The paper reports several internal inconsistencies, documented in its file: the "zero DEX-to-CEX causality" claim conflicts with Table 6; the abstract's "95% forced exits" conflicts with the 12/20 forced-exit count; and the significant-test counts differ between text and tables. Reproduce before citing the headline numbers in a pitch.
