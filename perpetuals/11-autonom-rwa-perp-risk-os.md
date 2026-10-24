# Autonom Risk OS — Corporate-Action-Aware RWA Perps for Monad and ETH

## Metadata

- **Origin:** a Solana-hackathon idea for an RWA oracle, ported and expanded for Monad and ETH.
- **Base product:** Autonom (autonom.cc) already ships a managed RWA price API, corporate-action normalization, funding-as-a-service, and an EVM oracle suite with EIP-712 updates and Merkle-quorum verification. The project also describes a risk module that recommends deleveraging or returning to baseline leverage around risk windows. This document does **not** claim those primitives are new.
- **New thesis:** the missing full stack is **event-credit funding + market-calendar-aware risk + a risk autopilot + a market-integrity certificate**, packaged into a perp-specific risk OS for venues on Monad and ETH.
- **Target hackathons:** Monad (Onchain Finance and Trading track) and Ethereum/ETHGlobal (RWA, DeFi, oracle, and agentic-commerce tracks). Zcash is not a fit.
- **Read status:** researched from public sources in October 2026. Numbers are sourced and labelled; vendor claims are not treated as audited facts.

## One-paragraph thesis

RWA perps became a real market in 2026: equities, indices, commodities, FX, and pre-IPO names now trade as cash-settled perpetuals on-chain. The contracts mostly work. The failures are in the **context around the price**: corporate actions, dividends, exchange calendars, off-hours liquidity, stale or compromised feeds, and leverage that is not sized to reference depth. Autonom already normalizes corporate actions and publishes funding. The hackathon-worthy extension is a **risk operating system** that turns those inputs into a live, verifiable risk budget for each market: discrete event credits for dividends, session-aware marks, event-window leverage caps, deleverage recommendations, and a **Market Integrity Certificate** that a perp venue or LP can verify on-chain. The product is not "another oracle." It is the layer that makes an RWA perp safe to list.

## Why now

### The market is large and accident-prone

- CMC Research reports **$3.16T cumulative RWA perp volume** from 29 December 2025 to 31 August 2026, with **$799.5B in August 2026** alone and **stocks at 62.3% of August volume**.
- QuillAudits, citing a16z crypto, reports **$117.3B in RWA perp volume in August 2026, 44x year-over-year**, with equities at 48% of that volume.
- These datasets use different venue scopes and definitions; treat the figures as directional, not additive.
- More than **$50M was lost across five RWA-perp incidents in under a year**, and in none of them was the root cause a broken Solidity line.

### The failures are context failures

- **Ostium, 15 July 2026:** a compromised oracle signer key let an attacker submit future-dated price reports through a registered forwarder. Roughly 20 looped trades drained **$23.75M USDC** from the OLP vault. The contract checked the signer, not freshness, plausibility, or cross-feed consistency.
- **AFX Trade, 22 July 2026:** about **$24.15M** left the venue after validator signing keys behind an application-level USDC bridge were compromised. The trading engine was not touched.
- **Papertrade, 10 October 2026:** a small move in a thin reference market, combined with up to 1,000x leverage, pulled about **$1.28M** from the pool on launch day. Open interest peaked near **$10.1B** against roughly **$54M** of cover.
- **XYZ100, December 2025:** a six-hour TWAP short of roughly **$10M** on a Nasdaq-100 perp while the underlying was closed pushed the perp more than 3.5% and liquidated about **$13M** of longs before recovery.
- **Ventuals, 28 May 2026:** incorrect off-chain data crashed a SpaceX pre-IPO perp 45% in 30 minutes and liquidated hundreds of accounts. The venue later shut down.
- **18 August 2026:** a single bad Korean pre-market print produced **$57M of liquidations across 960 accounts** in the first marquee equity-oracle incident.

### Corporate actions are still an open gap

CMC Research's May 2026 RWA perp report states that most current protocols **ignore dividends**, creating structural premia/discounts, and that **splits, mergers, and spinoffs need synchronized oracle, position, and funding adjustments** that have not been stress-tested at scale. SNDK, MU, and INTC all pay dividends. A 3-for-1 split without an adjustment would show every long as down roughly 67%.

There is now a formal result behind the intuition: *Basis-Only Funding Is Not Dividend Fair* (arXiv:2610.22839, MARBLE 2026) shows that a predictable dividend is an **atom**, and no absolutely continuous funding path can replicate an atom over holding intervals. Fairness requires matching the atom with an atom — an **event credit**. A premium-index funding formula alone transfers the dividend across venues.

### The oracle market is concentrated, but the risk layer is not

- Pyth is reported to have priced **94.4% of Q3 2026 RWA perp volume**, including **98.3% of equities**, with Chainlink, Stork, and RedStone combined at roughly 3%.
- Stork has a 24/7 RWA perp oracle methodology for equities and commodities, explicitly covering off-hours price discovery.
- Autonom's differentiation is not raw price share; it is **corporate-action normalization** plus a funding service. The missing piece is the risk/verification layer that consumes both.

### Monad and ETH are the right deployment surfaces

- **Monad:** 300ms blocks, 600ms finality, high throughput; about **$1.02B DeFi TVL**, 170+ apps, **$400M+ distributed RWA value**, and perp venues such as Perpl (fully on-chain CLOB), Drake, Pingu, Narwhal, and Monday Trade (spot, perps, tokenized equities). Chainlink and Pyth are live.
- **Ethereum:** the deepest RWA and perp ecosystem. Ostium (Arbitrum) passed **$50B cumulative volume** by May 2026 and launched Nasdaq-powered equity perps; Ondo Perps supports tokenized-stock collateral; Dinari and Backed tokenize equities; the SEC's five-year innovation exemption for tokenized stocks (September 2026) improves the regulatory path.

## The gap in one sentence

RWA perps have price feeds, but they do not have a **context-aware risk budget** that tells the venue, the LP, and the trader what a corporate action, a market session, a halt, a dividend, or a thin reference book does to leverage, funding, and liquidation.

## The product: Autonom Risk OS

Autonom Risk OS is a full-stack extension of the existing Autonom oracle/funding product. It has seven layers.

### 1. Autonom Core — price and corporate-action normalization (existing base)

The existing Autonom surface already includes:

- signed multi-asset price quotes with cross-provider discrepancy checks;
- corporate-action cumulative normalization (splits, dividends, M&A adjustments);
- funding-as-a-service with policy-driven rates and on-chain sinks;
- an EVM oracle suite with EIP-712 updates and M-of-N Merkle quorum;
- market-hours intelligence and staleness controls;
- admin killswitches, rate limits, and monitoring.

The new work builds on these rather than replacing them.

### 2. Event-Credit Funding — the research-backed primitive

A continuous basis funding rate cannot reproduce a discrete dividend. Autonom Risk OS adds an **Event Credit** lane:

```text
funding = continuous_basis_funding
        + carry_component
        + event_credit
```

where `event_credit` is a discrete, pre-committed cash adjustment for:

- cash dividends,
- special dividends,
- spin-offs,
- merger consideration,
- rights issues,
- symbol changes with economic effect.

The event credit is committed at the ex-date and settled atomically for positions held through the event. It is not a rate that tries to approximate an atom. The venue can publish the event-credit schedule in advance, and traders can see whether a position is eligible.

**Why it matters:** it removes the cross-venue dividend transfer and the structural premium/discount that appear when one venue pays the dividend and another does not.

### 3. Market Calendar Engine — sessions, halts, and time zones

The calendar engine models:

- exchange open/close, early closes, holidays, and time zones;
- trading halts and circuit breakers;
- pre-market and after-hours sessions;
- settlement and record dates;
- symbol changes and corporate-action effective times.

The output is a **session state** per symbol per timestamp:

```text
OPEN | PRE | POST | CLOSED | HALTED | AUCTION | UNKNOWN
```

The perp venue consumes the session state to choose the mark mode, funding mode, leverage cap, and liquidation behavior.

### 4. Off-Hours Risk Bands — no liquidation on a closed market

When the reference market is closed or thin:

- the mark uses a bounded band around the last valid reference, not an unbounded order-book mid;
- new position leverage is reduced;
- liquidation buffers widen;
- funding does not drift on a stale index;
- reopen gaps are handled by a defined auction or smoothing window.

The design target is the XYZ100 failure mode: a perp should not let a closed underlying's thin book become the price that liquidates the market.

### 5. Risk Autopilot — leverage and margin as a function of context

The autopilot computes a live risk recommendation per market:

```text
max_leverage = f(reference_depth, event_proximity, session_state,
                 corporate_action_pending, volatility, open_interest,
                 insurance_coverage)
margin_addon = f(event_window, halt_risk, concentration)
deleverage_target = f(current_OI, depth, stress_scenario)
```

Recommendations include:

- reduce max leverage before earnings, ex-dividend dates, M&A votes, and index rebalances;
- raise margin add-ons during event windows;
- cap open interest against executable reference depth;
- recommend deleveraging when the book is thin or a halt is likely;
- return to baseline leverage after the event window clears;
- pause new positions during a halt or an unresolved corporate action.

The autopilot is advisory first, then enforceable: a venue can consume a signed recommendation and apply it as a policy update, or a risk module can enforce it on-chain.

### 6. Market Integrity Certificate — the verifiable artifact

For each market and block, Autonom can emit a **Market Integrity Certificate** binding:

- the signed price and its freshness;
- the corporate-action factor and effective event;
- the session state and calendar version;
- the funding mode and event-credit schedule;
- the leverage cap, margin add-on, and depth assumption;
- the oracle quorum and signer set;
- the risk-model version.

A venue, LP, or auditor can verify that the market's price, funding, calendar, and risk parameters are mutually consistent. This is the product primitive that turns a dashboard into an on-chain trust object.

The certificate does **not** prove the price is true. It proves that the venue is using a coherent, current, corporate-action-aware configuration. The distinction matters: a valid signature is not a true price; a valid certificate is not a guarantee of solvency.

### 7. Venue Adapters and Risk Console

Adapters for Monad and ETH perp venues:

- consume the price, funding, calendar, and risk recommendation;
- enforce the venue's chosen policy;
- emit a certificate hash and a risk event log;
- expose a risk console for LPs and traders.

## Architecture

### Shared off-chain core

```text
market data providers
  -> corporate-action parser and event store
  -> normalization factor engine
  -> calendar/session engine
  -> funding engine (continuous + event credit)
  -> risk engine (leverage, margin, depth, stress)
  -> signing/quorum service
  -> chain poster (Monad or ETH)
```

### On-chain contracts

```text
AutonomOracle.sol
  - EIP-712 price, funding, and normalization-factor updates
  - single-signer and M-of-N Merkle-quorum verification
  - freshness, monotonic timestamp, and deviation checks

NormalizationRegistry.sol
  - cumulative adjustment factor per symbol
  - corporate-action event id and effective time
  - monotonic sequence and source hash

MarketCalendar.sol
  - session state, halt state, calendar version per symbol
  - venue-specific holiday and early-close rules

FundingSink.sol
  - continuous funding state
  - event-credit schedule and eligibility
  - settlement event for held-through positions

RiskConfig.sol
  - per-market max leverage, margin add-on, OI cap
  - event-window parameters and depth assumptions
  - governance timelock and emergency pause

RiskAutopilot.sol
  - consumes signed risk recommendations
  - enforces or queues policy updates
  - emits deleverage/return-to-baseline events

MarketIntegrityCertificate.sol
  - binds price/factor/calendar/funding/risk hashes
  - verifies the oracle quorum and model version
  - exposes a certificate id for venue/LP checks
```

### Monad deployment shape

- Solidity contracts on Monad mainnet/testnet, Foundry tests, deploy scripts.
- Pyth and Chainlink adapters; Autonom oracle as the corporate-action-aware layer.
- Integration target: a Monad perp DEX such as Perpl, Drake, Pingu, Narwhal, or Monday Trade; for a hackathon, a mock venue plus one real integration is enough.
- Monad-specific story: per-block risk recomputation and event-window updates are cheap; 300ms blocks make the risk budget responsive rather than daily.

### ETH deployment shape

- Same contracts on Arbitrum/Base/ETH, with Chainlink/Pyth/RedStone adapters.
- Integration target: Ostium (Arbitrum) or Ondo Perps for a live RWA-perp surface; a mock venue is the fallback.
- ETH-specific story: the deepest RWA liquidity, institutional custody/tokenization, and an RWA/oracle track fit.

## Data model

### Corporate-action event

```json
{
  "event_id": "ca_2026_10_orcl_div",
  "symbol": "ORCL",
  "type": "cash_dividend",
  "ex_date": "2026-10-09",
  "record_date": "2026-10-09",
  "pay_date": "2026-10-23",
  "amount": 0.50,
  "currency": "USD",
  "ratio": null,
  "adjustment_factor": 1.0,
  "status": "confirmed",
  "source": "issuer_filing",
  "effective_at": "2026-10-09T13:30:00Z"
}
```

### Price report

```json
{
  "symbol": "ORCL",
  "price": 158.78,
  "normalization_factor": 1.0,
  "session_state": "OPEN",
  "reference_time": "2026-10-09T14:00:00Z",
  "source_set": ["consolidated_tape", "secondary_provider"],
  "confidence": 0.99,
  "signature": "0x..."
}
```

### Risk recommendation

```json
{
  "symbol": "ORCL",
  "event_id": "ca_2026_10_orcl_div",
  "max_leverage": 5,
  "margin_addon_bps": 200,
  "oi_cap_usd": 25000000,
  "deleverage_target_pct": 20,
  "event_window": ["2026-10-07T00:00:00Z", "2026-10-10T23:59:59Z"],
  "session_policy": "open_only_new_positions",
  "model_version": "risk-0.3"
}
```

### Market Integrity Certificate

```json
{
  "certificate_id": "mic_monad_orcl_12345678",
  "venue": "0x...",
  "symbol": "ORCL",
  "block": 12345678,
  "price_hash": "0x...",
  "normalization_hash": "0x...",
  "calendar_hash": "0x...",
  "funding_hash": "0x...",
  "risk_hash": "0x...",
  "oracle_quorum": "3-of-5",
  "model_version": "risk-0.3",
  "valid_until": "2026-10-09T14:05:00Z"
}
```

## Demo (5 minutes)

**Act 1 — The corporate-action failure.** A mock perp venue lists ORCL. A 0.50 dividend is paid. The naive venue uses a raw price feed and a basis-only funding rate: the ex-dividend drop shows up as a loss, the long does not receive the dividend, and the venue's funding transfers value to the short. The judge sees the unfairness.

**Act 2 — Event credit.** Autonom applies the normalization factor and an event credit equal to the dividend. The long's PnL is economically equivalent to holding the stock through the ex-date. The certificate shows the event id, factor, and credit.

**Act 3 — The split.** A 3-for-1 split is announced. The naive venue would show a 67% drop. Autonom updates the normalization factor, the risk engine adjusts the mark and the leverage cap, and the venue does not liquidate every long.

**Act 4 — The closed market.** The underlying is closed; a thin order book tries to push the perp. Autonom's session state switches to `CLOSED`, the mark uses a bounded band, new leverage is reduced, and the liquidation buffer widens. The XYZ100-style attack fails.

**Act 5 — The certificate.** The venue and an LP verify the Market Integrity Certificate: price fresh, factor current, calendar correct, funding event-aware, leverage within depth. The judge cannot find a mismatch. Then toggle one input (stale price or wrong session) and the certificate fails.

**Close:** "RWA perps do not fail because the contract is wrong. They fail because the context is missing. Autonom makes the context verifiable."

## Hackathon positioning

### Best one-liner

> **Autonom Risk OS: the missing risk layer for RWA perps — corporate-action-aware pricing, event-credit funding, and a live risk autopilot, verifiable on-chain.**

### Monad positioning

- **Track:** Onchain Finance and Trading (Metropolis-style; the 2026 Metropolis window has closed, but the track logic carries to the next Monad hackathon).
- **Narrative:** "Monad's 300ms blocks and high throughput make event-aware risk a per-block primitive, not a daily batch. Autonom is the first RWA perp risk OS built for that cadence."
- **Demo target:** a Monad perp venue or a mock venue on Monad testnet, with a real Pyth/Chainlink adapter and the Autonom oracle.
- **Why Monad wins:** RWA is a stated Monad priority ($400M+ distributed RWA value), perp DEXs are live, and the chain's throughput is a genuine technical fit for per-block risk recomputation.

### ETH positioning

- **Track:** RWA, DeFi, oracle, or institutional finance at ETHGlobal/ETH conferences.
- **Narrative:** "The corporate-action-aware risk oracle for tokenized-equity perps on Arbitrum/Base. Integrates with Ostium and Ondo Perps instead of competing with Pyth."
- **Demo target:** Ostium or Ondo Perps as the venue, with a mock fallback; a real dividend/split scenario on a live RWA perp.
- **Why ETH wins:** the deepest RWA/perps ecosystem, institutional tokenization, and a clear regulatory path after the SEC innovation exemption.

### What not to pitch

- "Another RWA oracle." Pyth already has the volume; raw price is not the wedge.
- "We will replace Pyth/Chainlink/Stork." Position as a risk layer on top, with adapters.
- "Fully trustless." The oracle signer, data provider, and risk model are trust assumptions; the certificate makes them explicit.
- "This prevents all liquidations." It prevents specific context failures; it does not eliminate market risk.
- "Ostium is on Solana." Ostium is Arbitrum-based; Solana dominates tokenized-equity spot, but its RWA-perp depth is still thin.

## Competitive landscape

| Player | What it does | What it does not solve |
|---|---|---|
| Pyth | Dominant RWA perp price feed; broad asset coverage | Corporate-action event credits, venue-specific risk budgets |
| Stork | 24/7 RWA perp oracle methodology, off-hours pricing | Full corporate-action normalization and risk autopilot |
| Chainlink | General oracle infrastructure, data feeds, CCIP | RWA-specific event-credit funding and perp risk policy |
| RedStone | Modular oracle/data feeds, RWA coverage | Same: price, not the full risk stack |
| Autonom | Corporate-action normalization, funding-as-a-service, EVM oracle suite | The risk autopilot and market-integrity certificate as a packaged perp product |
| Ostium | Leading RWA perp venue (Arbitrum) | Its own oracle/risk stack is venue-specific, not a neutral risk layer |
| Ondo Perps | Tokenized-stock collateral and RWA perps | Neutral corporate-action/risk infrastructure for other venues |

## Moat

1. **Corporate-action event corpus.** A versioned, sourced history of splits, dividends, M&A, spin-offs, halts, and symbol changes, with adjustment factors and dispute records.
2. **Event-credit funding standard.** A published, testable settlement convention that venues can adopt; the formal dividend-atom result gives it intellectual grounding.
3. **Risk autopilot calibration.** Depth-aware leverage, event-window margin, and deleverage policies calibrated on public RWA-perp incidents.
4. **Market Integrity Certificate.** A verifiable artifact that LPs and venues can require; the certificate format and registry become the standard.
5. **Integration surface.** Monad and ETH venue adapters, oracle adapters, and a risk console; the integrations are harder to copy than the contracts.

## Business model

- **Oracle subscription:** per-symbol, per-venue, or enterprise data fees.
- **Funding-as-a-service:** per-market policy and settlement fees.
- **Risk autopilot:** SaaS for venues, LPs, and risk desks; tiered by markets and assets under risk.
- **Market Integrity Certificate:** issuance/verification fees; LP-facing risk attestations.
- **Data licensing:** corporate-action and calendar data to venues, wallets, and auditors.
- **Integration/implementation:** paid adapters and custom risk models.

## Honest cons and failure modes

- **Autonom exists.** The base oracle/funding product is real; the hackathon must sell the risk layer and the Monad/ETH integration, not the existence of an RWA oracle.
- **Data licensing is expensive.** Consolidated equity data, corporate actions, and exchange calendars are not free; the moat is also a cost.
- **Corporate-action errors are catastrophic.** A wrong factor or missed event can misprice every position. The system needs multi-source reconciliation, a dispute window, and a killswitch.
- **Oracle signer compromise.** The Ostium incident shows that a valid signer is not a true price. The certificate must include freshness, deviation, cross-feed checks, and quorum, not just a signature.
- **Off-hours manipulation.** Bounded bands reduce but do not eliminate manipulation; the venue still needs depth limits and position caps.
- **Regulatory and securities risk.** Tokenized equities and equity perps face jurisdictional constraints; the product must be infrastructure, not a venue.
- **Incumbent competition.** Pyth, Stork, Chainlink, and RedStone can add corporate-action features; the risk autopilot and certificate are the differentiation.
- **False confidence.** A Market Integrity Certificate proves coherence, not solvency or price truth. The UI and docs must say so.
- **Adoption dependency.** The product needs at least one perp venue to integrate; a mock-only demo is weaker.

## Build plan (5 humans + 10 agents, no time cap)

| Workstream | Humans | Agents | Output |
|---|---:|---:|---|
| Data + corporate actions | 1 | 2 | Event store, normalization factors, calendar engine |
| Oracle + contracts | 1 | 2 | AutonomOracle, registry, funding sink, certificate |
| Risk engine | 1 | 2 | Leverage/margin/depth model, autopilot, stress tests |
| Monad integration | 1 | 2 | Adapter, testnet venue, per-block risk demo |
| ETH integration + narrative | 1 | 2 | Ostium/Ondo adapter or mock, deck, judge Q&A |
| Integration/QA | all | shared | End-to-end demo, failure modes, rehearsal |

## Scorecard

| Chain | Fit | Why |
|---|---:|---|
| **Monad** | 9/10 | EVM, high throughput, live perp DEXs, RWA priority, per-block risk story |
| **ETH** | 8/10 | Deep RWA/perps, Ostium/Ondo, institutional credibility, crowded oracle market |
| **Solana** | 8/10 | Origin of Autonom; tokenized-equity spot dominance; RWA-perp depth still thin |
| **Zcash** | 2/10 | No native smart-contract perp/oracle surface; only a private-settlement reframing fits |

## Sources

- CMC Research, *RWA Perpetuals — State of the Market, May 2026*: https://s3.coinmarketcap.com/uploads/RWA%20Perpetuals%20%E2%80%94%20State%20of%20the%20Market%2C%20May%202026%20%C2%B7%20CoinMarketCap%20Research.pdf
- CMC Research, *RWA Perpetuals: State of the Market, August 2026*: https://s3.coinmarketcap.com/uploads/CMC_RWA_Perps_Quarterly_Report_Aug_2026.pdf
- QuillAudits, *$50M+ Lost in RWA Perps With Zero Contract Bugs*: https://www.quillaudits.com/blog/rwa/rwa-perps-exploits
- Seo et al., *Basis-Only Funding Is Not Dividend Fair*, arXiv:2610.22839: https://arxiv.org/abs/2610.22839
- Autonom product page: https://www.autonom.cc/products
- Autonom documentation: https://docs.autonom.cc/
- Stork, 24/7 RWA perp oracle methodology: https://www.stork.network/blog/247-price-feed-announcement
- Ostium, RWA perps guide: https://www.ostium.com/blog/perps-on-rwas-best-dex-for-rwa-perpetuals-2026-guide
- Ondo, tokenized stock collateral for equity perps: https://ondo.finance/blog/tokenized-stock-collateral-for-equity-perps
- Monad ecosystem highlights, August 2026: https://www.monad.xyz/blog/monad-ecosystem-highlights-august-2026
- Monad, high-frequency finance: https://monad.xyz/blog/monad-home-high-frequency-finance
- Monad Metropolis hackathon: https://monad.xyz/developers/hackathons
- Solana ecosystem roundup, September 2026: https://solana.com/news/solana-ecosystem-roundup-september-2026
- CoinDesk, Dinari tokenized U.S. stocks: https://www.coindesk.com/business/2026/08/04/dinari-brings-tokenized-u-s-stocks-to-american-investors-as-equity-race-heats-up
- CNBC, tokenized stocks and corporate pushback: https://www.cnbc.com/2026/10/21/tokenized-stocks-us-companies-object.html
