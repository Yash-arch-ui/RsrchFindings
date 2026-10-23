# RsrchFindings

Research findings and hackathon ideation for perpetual futures and cryptocurrency funding-rate markets.

## Contents

- [Perpetuals research](perpetuals/README.md) — paper-by-paper findings, equation ledgers, limitations, hackathon preparation, and a full idea lab.
- [Privacy x Perpetuals research](privacy-x-perpetuals/README.md) — private perps, encrypted-mempool funding security, private order matching, leakage taxonomy, and the VeilPerp flagship stack.

## Papers covered

1. *A Primer on Perpetuals* (Angeris, Chitra, Evans, Lorig, 2022)
2. *Fundamentals of Perpetual Futures* (He, Manela, Ross, von Wachter, 2024)
3. *Hedging with Automatic Liquidation and Leverage Selection on Bitcoin Futures* (Alexander, Deng, Zou, 2023)
4. *A Shared Template Without Shared Feedback: Funding Rates in Cryptocurrency Perpetual Futures* (He, Wang, Zhang, 2026)
5. *The Two-Tiered Structure of Cryptocurrency Funding Rate Markets* (Zhivkov, 2026)
6. *Funding Timing and No-Arbitrage Bounds in Decentralized Perpetual Markets* (Erez, Smirnov, 2026)
7. *Delayed Marks, Funding Memory, and Forecasting Liquidation-Tail Risk in Crypto Perpetual Futures* (Pindza, Mashele, 2026)

## Privacy x Perpetuals

Five papers covering the privacy side of perpetuals and private order matching:

1. *Reveal, Correct, Then Pay: Encrypted Mempools and Perpetual Funding Security* (Marsh, 2026)
2. *ZKPerp — Privacy-First Perpetual Futures* (de Boer, 2026)
3. *SoK: Privacy-Preserving Order Matching* (Arote, Saiz, Kiffer, 2026)
4. *Privacy-Preserving Decentralized Exchange Marketplaces* (Govindarajan et al., 2021) — Rialto
5. *P2DEX: Privacy-Preserving Decentralized Cryptocurrency Exchange* (Baum, David, Frederiksen, 2021)

**Flagship direction: VeilPerp** — a privacy-native perp stack with private positions, private batch matching, public risk proofs, decryption-aware funding (CipherClock), threshold liquidation, bonded settlement, and a phase-by-observer leakage passport. See [the privacy synthesis](privacy-x-perpetuals/06-privacy-perp-flagship-ideas.md) for the full slate, workflows, cons, demo script, and judge Q&A.

## Flagship direction

**Perp Forge 5.0** — a compiler and launchpad for new perpetuals, with:

- **Basis Mesh** for the no-arbitrage band and basis signal.
- **Funding DNA** for effective-funding calibration.
- **Clockwork** for funding-timing and no-arbitrage testability.
- **LiquidMesh** for liquidation-aware hedging.
- **TailMark** for mark-quality and liquidation-tail severity.
- **TierBridge** for execution-aware CEX-DEX funding arbitrage.

See [the idea lab](perpetuals/08-hackathon-idea-lab.md) for the full portfolio, scoring, team split, demo script, and judge Q&A. See [clean-sweep flagship ideas](perpetuals/09-flagship-ideas-clean-sweep.md) for the Perp Risk Bureau concept: PerpScore, Perp VIX, Perp Forge, and PerpShield. See [crazy flagship ideas](perpetuals/10-flagship-ideas-crazy-sweep.md) for the expanded Perp Safety Grid slate: Perp Nervous System, Perp Court, Perp OS, Perp CDX, Perp Genome, Perp Markets, Perp Twin, Perp Safe, Perp Constitution, and Perp Passport, with full workflows, a verified existing-product scan, jaw-drop demo moments, and honest cons for each.
