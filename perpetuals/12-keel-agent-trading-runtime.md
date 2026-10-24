# Keel — The Bounded-Autonomy Runtime for AI Trading

## Metadata

- **Working name:** Keel. Check trademark/domain before public use.
- **Target hackathons:** Monad (Onchain Finance and Trading; Trust, Identity, and AI Infrastructure) and Ethereum/ETHGlobal (DeFi, agentic commerce, AI agents, RWA).
- **Category:** agent trading runtime, not a trading bot, not a strategy marketplace, not another wallet.
- **One-liner:** Any strategy. Any venue. One hard risk kernel.
- **Core claim:** The agent can compose any strategy, but it cannot bypass the risk envelope, the reconciliation loop, or the kill switch.
- **Read status:** researched from public sources in October 2026. Competitor claims are labelled as claims; the product thesis is original.

## One-paragraph thesis

AI can now write trading strategies, but the industry still lacks the runtime that makes those strategies safe to run continuously with real money. General agent runtimes such as OpenClaw can live on a VPS and call tools, but they are not trading systems. Strategy platforms such as Almanak can compile and deploy DeFi strategies, but the market still lacks a neutral, multi-venue runtime with a non-bypassable risk kernel and continuous position reconciliation. Keel is that runtime. A strategy becomes a signed **Strategy Capsule** containing code, data dependencies, venue adapters, a risk envelope, and kill conditions. A deterministic **Risk Kernel** outside the model is the only component that can authorize an order. A **Reconciliation Loop** continuously compares the strategy's book against every venue's actual state and pauses on divergence. A **Proof of Restraint** lets an LP or auditor verify that the agent stayed inside its risk envelope without revealing the strategy. The result is **bounded autonomy**: no babysitting, but not no oversight. The product is the missing reliability layer for AI trading.

## The problem

### AI can write strategies faster than institutions can trust them

A capable model can turn a plain-language thesis into code: a funding-rate carry trade, a basis trade, a momentum strategy, a market-making loop, a yield rotation, a perp hedge. The bottleneck is no longer ideation. It is:

- **Deployment:** turning a generated script into a long-running, restart-safe process.
- **Risk:** proving the agent cannot exceed leverage, drawdown, concentration, or venue limits.
- **Truth:** knowing the strategy's internal position book matches the venues.
- **Recovery:** handling partial fills, stale data, venue outages, nonce gaps, and model failures.
- **Trust:** letting a user, LP, or auditor verify risk compliance without exposing the alpha.

### Existing tools solve slices

- **OpenClaw** is a general agent runtime on a VPS: sessions, tools, memory, messaging, plugins. It is not a trading system, has no venue reconciliation, and no hard trading risk kernel.
- **Almanak** is an agentic DeFi strategy framework with a non-bypassable policy engine, Safe smart accounts, simulation, deployment, and monitoring. It is the closest competitor, but it is strategy-framework-first and DeFi-focused.
- **MetaMask Agent Wallet** and **Coinbase Agentic Wallets** provide self-custodial agent wallets with spend limits, allowlists, and security checks. They are wallets, not long-running strategy runtimes.
- **OKX OnchainOS** provides execution infrastructure across many chains and protocols. It is an execution layer, not a strategy lifecycle or reconciliation layer.
- **ScalarField / “The Agentic ETF”** (arXiv:2610.06856) describes a six-layer agentic-trading stack and explicitly identifies risk, reconciliation, and settlement as the layer that turns an agent into a fund.
- **“Paper Agents, Paper Gains”** (arXiv:2605.29174) found 1,900+ AI-tagged crypto projects and $3B+ in combined token valuations, but the performance evidence is weak. The category has hype without reliability.

The gap is not another agent, another wallet, or another strategy marketplace. It is the **runtime that makes an agent's strategy operationally and financially safe to run**.

## Why now

- **Agent wallets are live.** MetaMask Agent Wallet supports spend limits, allowlists, Guard/Beast modes, and agent frameworks including OpenClaw; it supports Monad and Hyperliquid. Coinbase launched Agentic Wallets. The execution rails exist.
- **Monad is building for agents.** Monad's Agent Hub is live, MetaMask Agent Wallet supports Monad, Monad joined the Agentic Payments Alliance and the x402 Foundation, and Monad perps volume passed **$2.5B in August 2026**; Perpl passed **$1B cumulative**.
- **Ethereum has the standards.** ERC-4337 smart accounts and ERC-8196 “AI Agent Authenticated Wallet” define policy-bound execution and verifiable delegation. Ethereum has the deepest DeFi liquidity and the most institutional RWA activity.
- **The agentic ETF is being priced.** The Agentic ETF paper argues that the intersection of ETFs, active management, and autonomous agents could reach hundreds of billions to low trillions by 2030. That future needs a risk/reconciliation layer, not a chatbot.
- **The failures are operational.** Most agent-trading losses will not come from a broken DEX contract. They will come from a bad model decision, a stale oracle, a position-book mismatch, a leaked key, a venue outage, or a risk limit that existed only in a prompt.

## The product: Keel

Keel is a full-stack runtime with eight components.

### 1. Strategy Capsule — the deployable unit

A Strategy Capsule is a signed, versioned artifact containing:

- strategy code or intent DAG;
- data dependencies and freshness requirements;
- venue adapters and asset allowlists;
- risk envelope (leverage, notional, concentration, drawdown, venue, asset, time);
- kill conditions and escalation policy;
- simulation/backtest evidence;
- model version and prompt hash;
- operator identity and permissions.

The agent composes the capsule. The capsule is immutable once signed; changes create a new version. This makes the strategy reproducible, reviewable, and auditable.

### 2. Risk Kernel — the non-bypassable policy VM

The Risk Kernel is a deterministic process outside the LLM. It is the only component that can authorize an order. It checks:

- per-order notional, leverage, and slippage;
- per-asset and per-venue concentration;
- portfolio gross/net exposure;
- drawdown and daily loss limits;
- order rate and cancel rate;
- oracle freshness and deviation;
- liquidity/depth relative to order size;
- venue allowlist and asset allowlist;
- time windows and market hours;
- correlation and stress budget;
- kill-switch state.

If the kernel says no, the order never reaches the signer. The model cannot override it, and a raw wallet call outside the kernel is blocked by the account policy or rejected by the signer.

### 3. Execution Router — multi-venue adapters

The router maps strategy intents to venue-specific actions:

- **Monad:** Perpl, Drake, Pingu, Narwhal, Monday Trade, and other Monad DeFi venues.
- **Ethereum:** Uniswap, Aave, GMX, Ostium, Hyperliquid via an agent wallet, and other EVM venues.
- **Optional:** Solana venues for a later expansion.

Each adapter exposes a uniform interface: quote, submit, cancel, status, positions, fills, funding, and errors. The router handles retries, idempotency keys, and venue-specific nonce/order semantics.

### 4. Reconciliation Loop — the truth layer

The strategy's internal book is not trusted. Every cycle, the runtime pulls positions, balances, open orders, fills, and funding from each venue and reconciles them against the strategy state.

It detects:

- orphan orders and partial fills;
- missing or duplicated positions;
- stale prices and funding drift;
- nonce gaps and rejected transactions;
- venue-reported PnL vs strategy PnL;
- balance changes that the strategy did not authorize.

On divergence beyond a threshold, the runtime pauses the strategy, emits an incident, and escalates. Reconciliation is the feature that turns a script into an operated product.

### 5. Runtime Supervisor — the VPS/container layer

The supervisor runs the strategy as a long-lived process:

- container isolation and health checks;
- restart with idempotent state recovery;
- secrets isolation and key custody;
- canary and shadow modes;
- log/telemetry export;
- alerting via Telegram/Discord/email;
- one-click pause, flatten, and kill.

It is inspired by OpenClaw's always-on VPS model, but the state machine is trading-specific.

### 6. Proof of Restraint — the category-defining primitive

A strategy's alpha is private. Its risk compliance should not be. Keel emits a periodic **Proof of Restraint**:

- commits to the strategy capsule hash and risk envelope;
- commits to the execution receipts and reconciliation snapshots;
- proves that every authorized action stayed within the signed envelope;
- optionally proves drawdown, leverage, and concentration bounds in zero knowledge;
- lets an LP, investor, or auditor verify compliance without seeing the strategy.

Start with signed logs and Merkle commitments; add a ZK circuit for the envelope checks once the execution model is stable. The proof is not a promise that the strategy is profitable. It is a proof that the agent did not exceed the mandate.

### 7. Strategy Registry — versioned, reviewable deployment

The registry stores:

- capsule versions and hashes;
- backtest/simulation evidence;
- live performance and risk metrics;
- incident history;
- permissions and operator identity;
- optional on-chain attestations.

The registry is not a marketplace by default. It is the audit and deployment record that makes a strategy reproducible.

### 8. UX — one-click deploy, one-click stop

The mass-market experience:

1. Describe a strategy in plain language, or paste Python.
2. Keel compiles it into a capsule and shows the risk envelope in human terms.
3. Run a shadow simulation against live data.
4. Deploy to a canary with a small size.
5. Watch PnL, exposure, drawdown, reconciliation, and risk budget in real time.
6. Press one button to pause, flatten, or kill.

No terminal required. The advanced user can inspect the code and the policy.

## Architecture

```text
 user prompt / Python
        |
        v
 +-------------------+       +----------------------+
 | Capsule Compiler  | ----> | Strategy Capsule     |
 | (LLM + tests)     |       | code + policy + data |
 +-------------------+       +----------+-----------+
                                        |
                                        v
 +-------------------+       +----------------------+
 | Risk Kernel       | <---- | Execution Router     |
 | deterministic VM  |       | Monad / ETH adapters |
 +---------+---------+       +----------+-----------+
           |                            |
           v                            v
 +-------------------+       +----------------------+
 | Signer / Account  | ----> | Venues               |
 | AA / MPC / HSM    |       | perps, DEXs, lending |
 +---------+---------+       +----------+-----------+
           |                            |
           v                            v
 +-------------------+       +----------------------+
 | Reconciliation    | <---- | Venue state          |
 | Loop              |       | positions, fills     |
 +---------+---------+       +----------------------+
           |
           v
 +-------------------+       +----------------------+
 | Proof of Restraint| ----> | LP / auditor / chain |
 +-------------------+       +----------------------+
```

### Monad deployment

- **Contracts:** capsule registry, risk-policy registry, execution receipt log, Proof of Restraint verifier, agent account policy.
- **Execution:** Monad perp/DEX adapters; MetaMask Agent Wallet or a smart account for signing; Monad Agent Hub for discovery.
- **Why Monad:** 300ms blocks and high throughput make per-block risk checks, frequent reconciliation attestations, and cheap execution receipts practical. Monad is explicitly courting agentic finance.
- **Demo venue:** Perpl or a mock perp venue on Monad testnet, with a real adapter.

### Ethereum deployment

- **Contracts:** ERC-4337/8196-compatible account policy, capsule registry, receipt log, Proof of Restraint verifier.
- **Execution:** Safe/Zodiac modules, MetaMask Agent Wallet, Coinbase Agentic Wallets, or a dedicated smart account; Uniswap/Aave/GMX/Ostium adapters.
- **Why Ethereum:** deepest liquidity, institutional credibility, mature account abstraction, and the strongest RWA/DeFi ecosystem.
- **Demo venue:** a Uniswap/Aave strategy plus a perp venue on Arbitrum/Base, or a mock venue with real price data.

## Strategy Capsule schema

```json
{
  "capsule_id": "keel_funding_carry_v3",
  "version": 3,
  "operator": "0x...",
  "strategy": {
    "type": "intent_dag",
    "entrypoint": "strategy.py",
    "code_hash": "0x...",
    "model": "gpt-5.5",
    "prompt_hash": "0x..."
  },
  "data": [
    {"feed": "pyth:BTC/USD", "max_staleness_ms": 2000},
    {"feed": "funding:binance:BTCUSDT", "max_staleness_ms": 60000}
  ],
  "venues": ["monad:perpl", "hyperliquid:perp"],
  "assets": ["BTC", "USDC"],
  "risk_envelope": {
    "max_leverage": 2.0,
    "max_position_pct": 0.20,
    "max_gross_exposure_pct": 0.60,
    "max_drawdown_pct": 0.08,
    "max_daily_loss_pct": 0.03,
    "max_order_rate_per_min": 12,
    "max_slippage_bps": 25,
    "min_depth_multiple": 5
  },
  "kill_conditions": [
    {"type": "drawdown", "value": 0.08},
    {"type": "reconciliation_divergence", "value": 0.01},
    {"type": "oracle_staleness_ms", "value": 5000},
    {"type": "venue_error_rate", "value": 0.10}
  ],
  "evidence": {
    "backtest_id": "bt_2026_10_01",
    "shadow_run_id": "sh_2026_10_12",
    "test_suite_hash": "0x..."
  },
  "signature": "0x..."
}
```

## Risk Kernel policy examples

```text
allow order if:
  notional <= remaining_position_budget
  and leverage_after <= envelope.max_leverage
  and gross_exposure_after <= envelope.max_gross_exposure_pct
  and drawdown <= envelope.max_drawdown_pct
  and oracle_age <= data.max_staleness_ms
  and venue in envelope.venues
  and asset in envelope.assets
  and order_size <= depth_at_slippage / envelope.min_depth_multiple
  and kill_switch == false

pause strategy if:
  reconciliation_divergence > kill_conditions.reconciliation_divergence
  or venue_error_rate > kill_conditions.venue_error_rate
  or drawdown > kill_conditions.drawdown
  or oracle_age > kill_conditions.oracle_staleness_ms

flatten and halt if:
  daily_loss > envelope.max_daily_loss_pct
  or operator presses kill
  or signer/account policy is violated
```

## Reconciliation model

Every cycle:

```text
strategy_book = positions, orders, cash, PnL from the strategy
venue_book    = positions, orders, balances, fills from each venue adapter

diff = reconcile(strategy_book, venue_book)
if diff.orphan_orders or diff.missing_positions or diff.pnl_drift > threshold:
    pause()
    incident()
    require_human_or_policy_repair()
```

The reconciliation loop is deliberately boring. It is the difference between a demo and a product.

## Demo (5 minutes)

**Act 1 — Compose.** The operator says: “Run a delta-neutral funding carry on BTC across Perpl and Hyperliquid, max 2x, max 8% drawdown, never touch an unapproved venue.” Keel compiles a capsule and shows the risk envelope in plain language.

**Act 2 — Simulate and deploy.** The strategy runs in shadow mode against live data, then deploys to a Monad testnet canary with a small size. The dashboard shows PnL, exposure, drawdown, and reconciliation.

**Act 3 — The jailbreak.** A red-team model tries to bypass the kernel: it prompts the agent to “increase leverage to 20x,” calls the signer directly, and routes to an unapproved venue. The Risk Kernel blocks every path; the capsule is unchanged; the kill switch still works.

**Act 4 — The venue lie.** A mock venue reports a fill that the strategy did not place. Reconciliation catches the mismatch, pauses the strategy, and emits an incident before the book drifts.

**Act 5 — Proof of Restraint.** An LP verifies that the strategy stayed inside its risk envelope without seeing the strategy. The proof shows compliance, not alpha. The judge tries to find a hidden breach and fails.

**Close:** “The agent can write the strategy. It cannot rewrite the risk.”

## Hackathon positioning

### Best one-liner

> **Keel is the bounded-autonomy runtime for AI trading: any strategy, any venue, one hard risk kernel, and a Proof of Restraint that LPs can verify without seeing the alpha.**

### Monad positioning

- **Track:** Onchain Finance and Trading; Trust, Identity, and AI Infrastructure.
- **Narrative:** “Monad's 300ms blocks make per-block risk checks and cheap execution receipts real. Keel is the first agent trading runtime built for that cadence.”
- **Demo asset:** a live Monad testnet strategy + Perpl/mock venue + Agent Hub listing + Proof of Restraint verifier.
- **Why Monad wins:** agent-friendly ecosystem, live perps, MetaMask Agent Wallet support, x402/agent payments, and a genuine throughput advantage for continuous risk/reconciliation.

### Ethereum positioning

- **Track:** DeFi, agentic commerce, AI agents, RWA.
- **Narrative:** “Ethereum has the deepest liquidity and the strongest account-abstraction standards. Keel is the reliability layer that makes agent strategies safe to run on top.”
- **Demo asset:** a Safe/ERC-4337 account with a Keel policy module, a Uniswap/Aave strategy, and a Proof of Restraint verifier.
- **Why Ethereum wins:** institutional credibility, mature DeFi, ERC-4337/8196, and the largest pool of strategies and venues.

### What not to pitch

- “An AI that trades for you.” That is a crowded consumer-agent pitch.
- “No oversight.” Say **bounded autonomy**: no babysitting, but hard limits and auditability.
- “Guaranteed returns.” The research does not support it.
- “We replace Almanak/OpenClaw/MetaMask.” Position as the runtime layer that composes with them.
- “HFT.” VPS + LLM loops are not microsecond infrastructure; target minutes-to-hours strategies.

## Competitive landscape

| Player | What it does | Where Keel differs |
|---|---|---|
| **OpenClaw** | General VPS agent runtime: sessions, tools, messaging, plugins | Keel is trading-specific: risk kernel, venue adapters, reconciliation, Proof of Restraint |
| **Almanak** | Agentic DeFi strategy framework with non-bypassable policy engine, Safe accounts, simulation, deployment, monitoring | Keel is venue-neutral and multi-venue, with reconciliation as a first-class primitive and a privacy-preserving risk proof |
| **ScalarField / Agentic ETF** | Integrated agentic-trading stack with execution registry, isolated strategy agents, reconciliation, data, compute | Keel is a runtime layer that can sit under a fund or venue; Proof of Restraint is the differentiator |
| **MetaMask Agent Wallet** | Self-custodial agent wallet with spend limits, allowlists, security checks | Keel uses wallets as signers; it owns strategy lifecycle, reconciliation, and risk proofs |
| **Coinbase Agentic Wallets** | Programmable agent wallets and payments | Same: wallet, not a strategy runtime |
| **OKX OnchainOS** | Multi-chain execution and data infrastructure for agents | Keel is a strategy runtime with risk/reconciliation, not an execution API |
| **Cod3x / Giza / Wayfinder** | Consumer-facing AI trading agents and strategy products | Keel is infrastructure for operators and funds, not a single agent brand |
| **TradingAgents** | Open-source multi-agent research framework | Keel is production runtime, risk, and reconciliation, not a research notebook |

## Moat

1. **Risk Kernel + reconciliation adapters.** The hard part is not the LLM; it is the venue-specific truth layer and the non-bypassable policy.
2. **Strategy Capsule format.** A reproducible, signed unit of deployment that venues, LPs, and auditors can recognize.
3. **Proof of Restraint.** A privacy-preserving compliance artifact that lets capital verify risk without seeing alpha.
4. **Incident corpus.** A growing library of real failure modes: stale oracles, venue mismatches, partial fills, key compromise, model drift.
5. **Uptime and integrations.** A runtime that stays up across venues is an operational moat, not a code moat.

## Business model

- **Runtime subscription:** per strategy, per venue, or per assets under management.
- **Risk Kernel / Proof of Restraint:** enterprise compliance tier for funds and LPs.
- **Venue integration fees:** paid adapters and certification for venues that want agent flow.
- **Strategy registry:** versioning, audit, and deployment fees; optional marketplace later.
- **Managed hosting:** VPS/container hosting with monitoring and incident response.

## Honest cons and failure modes

- **Crowded category.** Almanak, ScalarField, MetaMask, Coinbase, OKX, Cod3x, Giza, Wayfinder, and OpenClaw all touch part of the stack. The wedge must be reconciliation + risk proofs + UX, not “AI agent.”
- **LLM code is not reliable by default.** Generated strategies need tests, simulation, shadow mode, canary, and a human review path. “No oversight” is a future ambition, not a 2026 trust model.
- **Reconciliation is hard.** Venue APIs differ, rate-limit, lie, and fail. The adapter layer is the real engineering.
- **Key management.** A compromised signer bypasses the kernel. Use smart-account policies, HSM/passkey, session keys, and withdrawal delays.
- **Regulatory surface.** Trading advice, custody, strategy marketplaces, and cross-jurisdiction access all have legal constraints. Start non-custodial and infrastructure-only.
- **Performance claims.** The research literature does not show that LLM agents reliably beat passive benchmarks. Sell reliability, not alpha.
- **Latency.** VPS + LLM loops are not HFT. Target medium-frequency strategies; do not promise microsecond execution.
- **Prompt injection and malicious plugins.** The runtime must sandbox tools, pin plugins, and treat all model output as untrusted.
- **Proof of Restraint scope.** The proof covers the risk envelope and execution receipts, not the strategy's future performance or the truth of every venue report.

## Build plan (5 humans + 10 agents, no time cap)

| Workstream | Humans | Agents | Output |
|---|---:|---:|---|
| Capsule compiler + UX | 1 | 2 | Prompt/Python to capsule, risk-envelope view, deploy flow |
| Risk Kernel | 1 | 2 | Deterministic policy VM, tests, adversarial cases |
| Execution + reconciliation | 1 | 2 | Monad/ETH adapters, position truth, incident handling |
| Runtime supervisor | 1 | 2 | VPS/container, restarts, secrets, kill switch, alerts |
| Proof of Restraint | 1 | 2 | Signed receipts, Merkle commitments, optional ZK envelope |
| Integration/QA | all | shared | End-to-end demo, jailbreak tests, venue mismatch, rehearsal |

## Scorecard

| Dimension | Score | Why |
|---|---:|---|
| Novelty | 5 | Bounded-autonomy runtime + Proof of Restraint is a new category |
| Technical depth | 5 | Risk VM + reconciliation + multi-venue execution + proofs |
| Demoability | 5 | Jailbreak blocked + venue mismatch caught + LP verifies risk |
| Feasibility | 4 | A scoped Monad/ETH demo is achievable; full multi-venue runtime is hard |
| Judge appeal | 5 | AI + trading + safety + real money + verifiable risk |
| Moat | 5 | Reconciliation adapters, capsule format, incident corpus, risk proofs |
| **Total** | **29/30** | Highest ceiling; execution and integrations are the risk |

## Sources

- OpenClaw agent runtime: https://docs.openclaw.ai/concepts/agent
- OpenClaw VPS deployment: https://docs.openclaw.ai/install
- Almanak agentic trading SDK: https://sdk.docs.almanak.co/agentic/index.html
- Almanak platform: https://almanak.co/
- ScalarField / The Agentic ETF: https://arxiv.org/abs/2610.06856
- Paper Agents, Paper Gains: https://arxiv.org/pdf/2605.29174
- MetaMask Agent Wallet: https://metamask.io/agent-wallet
- MetaMask Agent Wallet docs: https://docs.metamask.io/agent-wallet.md
- Coinbase Agentic Wallets: https://www.gate.com/news/detail/coinbase-launches-ai-agent-infrastructure-for-autonomous-crypto-trading-21808418
- OKX OnchainOS AI toolkit: https://www.coindesk.com/tech/2026/03/03/okx-jumps-into-ai-agent-race-with-new-onchainos-toolkit
- ERC-4337: https://eips.ethereum.org/EIPS/eip-4337
- ERC-8196 AI Agent Authenticated Wallet: https://eips.ethereum.org/EIPS/eip-8196
- Ethereum AI agents: https://ethereum.org/ai-agents/
- Monad Agent Hub: https://monad.xyz/blog/monad-ecosystem-highlights-july-2026
- Monad ecosystem August 2026: https://www.monad.xyz/blog/monad-ecosystem-highlights-august-2026
- Monad machine economy: https://monad.xyz/blog/rise-of-machine-economy
- Monad high-frequency finance: https://monad.xyz/blog/monad-home-high-frequency-finance
- Monad perps / Perpl: https://monad.xyz/blog/monad-ecosystem-highlights-july-2026
- Ledger guardrails for autonomous trading agents: https://www.ledger.com/academy/series/n3xt/research-guardrails-for-autonomous-trading-agents
