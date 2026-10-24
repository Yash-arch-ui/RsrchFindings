# RsrchFindings

Research findings and hackathon ideation for perpetual futures, privacy, Zcash, Monad, and agentic e-commerce.

## Contents

- [Perpetuals research](perpetuals/README.md) — paper-by-paper findings, equation ledgers, limitations, hackathon preparation, and a full idea lab.
- [Privacy x Perpetuals research](privacy-x-perpetuals/README.md) — private perps, encrypted-mempool funding security, private order matching, leakage taxonomy, and the VeilPerp flagship stack.
- [Privacy research](privacy/README.md) — BBS+ anonymous credentials, issuer hiding, revocation, and delegated verifiable presentations.
- [Privacy x Agentic E-Commerce research](privacy-x-agentic-ecommerce/README.md) — agent payments, AP2 security, MCP spending controls, LLM privacy leakage, and multi-agent commerce.
- [Stablecoin payments](stablecoin-payments/README.md) — Passage Corridor: compliant stablecoin-to-local-rails settlement for Monad and ETH.
- [Zcash privacy research](zcash/README.md) — Halo recursive proofs, Zcash transaction linkability, and the Orchard circuit audit.
- [Zcash + Monad flagship ideas](IDEAS.md) — ten category-defining product ideas with once-a-decade primitives, impossible-to-fake demos, research sources, workflows, cons, and judge Q&A.
- [Source map](SOURCE-MAP.md) — all 16 unique sources, access status, and where each is analyzed.

## Zcash + Monad Hackathons

Primary focus: **Zcash** and **Monad**. ETHGlobal is the fallback.

The research set covers 16 unique sources across three folders:

- **Zcash:** Halo recursion, 2017 Zcash linkability measurement, and the 2026 Orchard audit.
- **Privacy:** BBS+ credentials with revocation and device binding, issuer hiding, and delegated verifiable presentations.
- **Privacy x Agentic E-Commerce:** MinMandate, AESP, TIVA, Cloaked MCP, AP2 security, two agentic-commerce SoKs, ConfAIde, PrivacyChecker, and MAGPIE.

**Recommended flagship:** **VeilMandate** — one private, task-scoped mandate lets an agent shop across merchants with fresh per-call credentials and Zcash-shielded settlement, so the agent can adapt without exposing the human's intent. Pair it with **IntentGuard** (context-binding payment firewall) and **Boundary Radar** (Zcash linkability lint). See [the flagship slate](IDEAS.md).

## Perpetuals papers covered

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

**Flagship direction: VeilPerp** — a privacy-native perp stack framed as **sealed books with public invariants**: a Sealed-Book Solvency Envelope, private batch matching, decryption-aware funding with a manipulation-immunity certificate (CipherClock), threshold liquidation, challengeable clearing with privacy-loss bonds (Bonded Shade), and an adversary-equivalence leakage passport. See [the privacy synthesis](privacy-x-perpetuals/06-privacy-perp-flagship-ideas.md) for the full slate, once-a-decade primitives, impossible demos, workflows, cons, demo script, and judge Q&A.

## Perpetuals flagship direction

**Perp Forge 5.0** — a compiler and launchpad for new perpetuals, with:

- **Basis Mesh** for the no-arbitrage band and basis signal.
- **Funding DNA** for effective-funding calibration.
- **Clockwork** for funding-timing and no-arbitrage testability.
- **LiquidMesh** for liquidation-aware hedging.
- **TailMark** for mark-quality and liquidation-tail severity.
- **TierBridge** for execution-aware CEX-DEX funding arbitrage.

See [the idea lab](perpetuals/08-hackathon-idea-lab.md) for the full portfolio, scoring, team split, demo script, and judge Q&A. See [clean-sweep flagship ideas](perpetuals/09-flagship-ideas-clean-sweep.md) for the Perp Risk Bureau concept: PerpScore, Perp VIX, Perp Forge, and PerpShield. See [crazy flagship ideas](perpetuals/10-flagship-ideas-crazy-sweep.md) for the expanded Perp Safety Grid slate: Perp Nervous System, Perp Court, Perp OS, Perp CDX, Perp Genome, Perp Markets, Perp Twin, Perp Safe, Perp Constitution, and Perp Passport, with full workflows, a verified existing-product scan, jaw-drop demo moments, and honest cons for each.

**RWA perps:** [Autonom Risk OS](perpetuals/11-autonom-rwa-perp-risk-os.md) — a Monad/ETH full-stack thesis for corporate-action-aware RWA perps: event-credit funding, market-calendar risk, a risk autopilot, and a market-integrity certificate.

## Monad + ETH agentic-finance shortlist

- [Autonom Risk OS](perpetuals/11-autonom-rwa-perp-risk-os.md) — corporate-action-aware RWA perps: event-credit funding, risk autopilot, and a Market Integrity Certificate.
- [Keel](perpetuals/12-keel-agent-trading-runtime.md) — bounded-autonomy runtime for AI trading: Strategy Capsule, Risk Kernel, reconciliation, and Proof of Restraint.
- [Passage Corridor](stablecoin-payments/01-passage-cross-border-corridor.md) — programmable stablecoin payouts: multilateral netting, purpose-bound transfers, Compliance Passport, and Proof of Payout.
- **Parked: x402/MPP API analytics.** The revenue base is still too small and the analytics layer is crowded; revisit if machine-payment volume grows.
