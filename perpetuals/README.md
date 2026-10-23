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
| 6 | [06-erez-2026-funding-timing-bounds.md](06-erez-2026-funding-timing-bounds.md) | *Funding Timing and No-Arbitrage Bounds in Decentralized Perpetual Markets* (Erez, Smirnov, 2026) | Theory plus empirical market microstructure | Funding testability depends on whether funding is known at entry; Drift out-of-band 3-5%, dYdX caps never bind and the bound is vacuous | Clockwork: funding-timing oracle and no-arbitrage band engine |
| 7 | [07-pindza-2026-delayed-marks-liquidation-tail.md](07-pindza-2026-delayed-marks-liquidation-tail.md) | *Delayed Marks, Funding Memory, and Forecasting Liquidation-Tail Risk in Crypto Perpetual Futures* (Pindza, Mashele, 2026) | Theory plus empirical calibration | Fixed-delay overshoot `sigma sqrt(delta/(2 pi))`; bad-debt tail in `sigma sqrt(delta)/m`; TWAP crossing overshoot ~1.6x fixed delay; one-week breach 0.07-0.23 at 10x to 0.76-0.83 at 50x | TailMark: mark-quality and liquidation-tail oracle |
| 8 | [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md) | Idea lab | Ideation | N/A | Perp Forge 5.0 plus Basis Mesh, Funding DNA, Clockwork, LiquidMesh, TailMark, and TierBridge as the full lifecycle stack |

## Quick comparison

| Dimension | Paper 1 | Paper 2 | Paper 3 | Paper 4 | Paper 5 | Paper 6 | Paper 7 |
|---|---|---|---|---|---|---|---|
| Core object | Payoff to funding | Funding to price benchmark | Hedge to liquidation probability | Funding template to effective feedback | Cross-venue spread to executable spread | Funding timing to bound testability | Mark delay to hidden liquidation loss |
| Perspective | Security design | Asset pricing | Risk management | Mechanism design | Empirical microstructure | Market design | Stopping-time forecasting |
| Empirics | None | Binance 2020-2024 | Coinbase spot, 7 perps, 2020-2021 | 200 Binance perps, 2026 | 26 exchanges, 35.7M minute obs, Nov 2025 | Drift 2023-2024, dYdX 2023-2025 | Binance BTC/ETH/SOL, 2026 |
| Main output | Funding and replication | No-arbitrage band | Optimal hedge, survival | Stability band, rule compiler | Lead-lag, executable spread | Funding-timing verdict, cap risk | Breach probability, bad-debt tail, mark quality |
| Hackathon strength | Depth | Demoability | Crisis demo | Mechanism novelty | Execution demo | Testability verdict | Hidden-loss demo and design fix |

## Recommended direction

Build **Perp Forge 5.0** as the core platform, launch one flagship perp generated by Forge (Slashing Perp, Prediction Perp, or Shiller Perp), and use six oracles plus an execution layer:

- **Basis Mesh / Basis Oracle** for the no-arbitrage band and basis signal.
- **Funding DNA** for effective-funding calibration and rule compilation.
- **Clockwork** for funding-timing classification, bound testability, and cap-binding risk.
- **LiquidMesh** for liquidation-aware position sizing and survival probability.
- **TailMark** for mark-quality and liquidation-tail severity.
- **TierBridge / Tier Oracle** for CEX-DEX lead-lag, executable spread, reversal hazard, and routed execution.

This combines the technical depth of Paper 1, the market signal and risk framework of Paper 2, the survival-aware hedging of Paper 3, the mechanism-design insight of Paper 4, the cross-venue execution realism of Paper 5, the testability discipline of Paper 6, and the loss-severity realism of Paper 7. The narrative is: **design the payoff, anchor the price, calibrate the funding, prove the bound is testable, keep the hedge alive, execute the trade, and price the hidden liquidation tail.**

See [08-hackathon-idea-lab.md](08-hackathon-idea-lab.md) for the full idea set, Wave 2/3/4/5 additions, scoring matrix, team split, demo, and judge Q&A. See [09-flagship-ideas-clean-sweep.md](09-flagship-ideas-clean-sweep.md) for the three clean-sweep flagship concepts: PerpScore/Perp VIX, Perp Forge with risk certificates and stress bonds, and PerpShield liquidation-tail insurance. See [10-flagship-ideas-crazy-sweep.md](10-flagship-ideas-crazy-sweep.md) for the expanded Perp Safety Grid slate: ten full flagship ideas with end-to-end workflows, contract architecture, demo scripts, team/agent splits, and honest cons for each, plus a verified existing-product scan, a jaw-drop "wow bar" for every idea, and a recommended Perp Nervous System + Perp OS + Perp CDX clean-sweep portfolio.

## Access note on Paper 4

The full SSRN PDF for Paper 4 could not be retrieved because SSRN's content-protection layer blocked direct, archived, and proxied downloads. The Paper 4 file separates verified abstract and metadata claims from a clearly marked analytical reconstruction. Do not cite the reconstruction as the authors' model.

## Data and reproducibility note on Paper 5

Paper 5's data are openly available at `https://loris.tools/funding/historical` (accessed 15 November 2025). The paper reports several internal inconsistencies, documented in its file: the "zero DEX-to-CEX causality" claim conflicts with Table 6; the abstract's "95% forced exits" conflicts with the 12/20 forced-exit count; and the significant-test counts differ between text and tables. Reproduce before citing the headline numbers in a pitch.

## Data and reproducibility note on Paper 6

Paper 6 uses public Drift and dYdX v4 protocol/indexer data. Replication code is at `https://github.com/ee2625/defi-perps`. Key caveats: `Gamma` is a calibrated 99th percentile, not a model-free support bound; Drift uses a mark-TWAP/oracle-TWAP proxy with `rho = 1`; dYdX uses candle-close and post-sample margin parameters. The reversion test is a diagnostic, not an executable arbitrage backtest.

## Data and reproducibility note on Paper 7

Paper 7 uses public Binance USD-M futures data, DeFiLlama stablecoin data, and Deribit option summaries. Supporting code and data are in Supplementary File S1. Key caveats: public data do not identify user-level collateral or liquidation paths; the breach probabilities are model-consistent hypothetical-account forecasts; the Gaussian benchmark and TWAP stationary-crossing approximation are explicit approximations; depth is a single snapshot, not a historical panel. Do not present the results as observed venue liquidation frequencies.
