# ZKPerp — Privacy-First Perpetual Futures

## Metadata

- **Author:** Henk-Wim de Boer
- **Document:** ZKPerp technical whitepaper v5.1, protocol version v30
- **Date:** May 2026
- **Network:** Aleo testnet
- **Language:** Leo 4.0
- **Type:** System design / engineering whitepaper. Not peer-reviewed, not audited.
- **Read status:** Full whitepaper read, including architecture, slot model, LP pool, liquidation, orders, commitments, oracle, compliance, dark pool, AMM, OI tradeoff, limitations, roadmap, and security properties.

## One-paragraph summary

ZKPerp is a privacy-first perpetual DEX built natively on Aleo. Position size, entry price, leverage, direction, collateral, PnL, and liquidation level live in **private Aleo records** readable only by the owner's viewing key. The chain stores only BHP256 **position commitments**, aggregate pool state, oracle prices, and liveness flags. Traders open positions through a Leo circuit; close and liquidation paths recompute the commitment and assert it against the public hash, which closes the earlier "inflated payout" hole. Liquidation uses a **1-of-N keeper-auth** pattern: each open position mints one `LiquidationAuth` record to each keeper, and any single keeper can liquidate without touching the trader's private slot. Prices come from an **on-chain 2-of-3 oracle quorum**, compliance from a **BHP256 Merkle allowlist**, and institutional order flow from a companion **ZK dark pool**. The honest tradeoff is that open interest and net PnL cannot be derived atomically from private position sizes without leaking them, so an orchestrator is trusted for aggregate pool accounting. The project is testnet software, unaudited, with no dynamic funding rate and a permissioned liquidation set.

## The privacy problem it targets

The whitepaper lists three concrete attack surfaces of transparent perp DEXs:

- **Front-running:** a large pending long is visible; bots buy before it lands and sell after.
- **Liquidation hunting:** entry, collateral, size, and liquidation price are public, so clusters can be pushed.
- **Strategy copying:** every settled trade is visible, so institutions telegraph their thesis.

ZKPerp's claim is that Aleo's public-state/private-record split lets the contract verify correctness without learning the numbers. The verifier learns that a computation was valid, not the witness.

### What remains public

| Public item | Why |
|---|---|
| Oracle prices | Required for settlement and unavoidable |
| Pool state | Total liquidity, aggregate long OI, aggregate short OI |
| Commitment hashes | `position_commits[id]` is a BHP256 hash |
| Transaction counts | Number of opens/closes per block, not contents |
| Compliance root | Merkle root only, never addresses |
| Pending-order booleans | Existence of an order, not trigger/size/direction |

Everything else — size, entry, collateral, leverage, direction, PnL, liquidation level — is intended to stay in private records.

## Architecture

Six layers:

1. **Frontend** — React/TypeScript, trade, liquidity, dark pool, portfolio, compliance.
2. **Shield Wallet** — signs transactions and unshields private records.
3. **Aleo programs** — per-market core contracts, oracle, compliance, dark pool, AMM.
4. **Private records** — `PositionSlot`, `LiquidationAuth`, `LPSlot`, `ExecTPSLAuth`, `ComplianceRecord`, `OrderAuth`.
5. **Public mappings** — `pool_state`, `oracle_prices`, `position_commits`, `compliance_root`, `active_position_ids`, `pending_orders`.
6. **Infrastructure** — keeper/orchestrator bot, three Chainlink relayers, compliance server.

### Deployed programs in the whitepaper

| Program | Purpose | Status |
|---|---|---|
| `zkperp_core_v30.aleo` | BTC/USDC perps, commit-verified close + keeper-race liquidation | Live |
| `zkperp_core_eth_v29c.aleo` | ETH/USDC perps | Live |
| `zkperp_core_sol_v29c.aleo` | SOL/USDC perps | Live |
| `zkperp_oracle_v4.aleo` | 2-of-3 on-chain quorum | Live |
| `zkperp_compliance_v9.aleo` | KYC gating | Live |
| `zkdarkpool_v9.aleo` | Batch-auction dark pool | Live |
| `zkperp_amm_v6.aleo` | Concentrated liquidity AMM | Prototype |

### Protocol parameters

| Parameter | Value | Enforcement |
|---|---|---|
| Max leverage | 20x | In-circuit: `(size * 100) / collateral <= 2000` |
| Minimum position | `size >= 100` | In-circuit |
| Opening fee | 0.10% | In-circuit, bound into commitment |
| Maintenance margin | 5% of notional | In-circuit equity, asserted on-chain |
| Liquidation reward | 10% of collateral | Deterministic, commit-pinned |
| LP withdrawal buffer | 10% of total liquidity | On-chain |
| Oracle staleness | 150 blocks (~5 min) | On-chain |
| Compliance validity | 7,776,000 blocks (~180 days) | On-chain |
| Price decimals | 8 | Oracle encoding |

## Slot record model

Aleo records are UTXO-like: a transition consumes a record and reissues it. ZKPerp uses two patterns.

### LPSlot: persistent balance accumulator

LP deposits add, withdrawals subtract, and order does not matter. One `LPSlot` per trader per market, refilled in place.

### PositionSlot: post-liquidation UX

Each position is a distinct economic object. A keeper cannot consume the trader's `PositionSlot`, so a naive design would leave a dead record in the trader's wallet after every liquidation. ZKPerp instead uses two slots per market:

- `slot_id = 0` holds the long.
- `slot_id = 1` holds the short.

`open_position` asserts the slot matches direction. A trader can hold at most **one long and one short per market concurrently**. Because BTC, ETH, and SOL are separate programs, a BTC long and an ETH long coexist. This is a deliberate v1 tradeoff: bounded record count and post-liquidation UX in exchange for one position per direction per market.

On liquidation, the winning keeper consumes its own `LiquidationAuth` and mints a fresh empty `PositionSlot` to the trader. The old slot remains as dust but fails at finalize because `active_position_ids` no longer contains the position. Dead records accumulate but do not require user action. Keepers clear stale auths with `burn_liquidation_auth`; traders clear settled order receipts with `burn_order_receipt`.

## LP pool and withdrawal guard

The perp pool is **single-sided USDCx**: LPs are the direct counterparty to traders. There is no impermanent loss from token-price divergence because the pool holds one asset.

```text
available = total_liquidity
          - long_open_interest
          - short_open_interest
          - pnl_liability          // max(net unrealised PnL, 0)
          - safety_buffer          // 10% * total_liquidity
```

Long and short OI are **not netted**. The paper's reason is that positions settle asynchronously at the trader's choosing: a winning position can be paid before the opposing side closes, is liquidated, or returns to breakeven. A losing position's contribution is capped at its collateral, while a winning payout is bounded only by available liquidity. A balanced book is therefore not a hedge from the pool's perspective.

### The OI trust problem

Private sizes cannot reach `finalize` without becoming public finalize arguments. ZKPerp therefore has an orchestrator (`roles[1u8]`) compute OI and net PnL off-chain and write them via `update_pool_state` / `update_net_pnl`. The whitepaper is unusually candid: the orchestrator can overwrite **all** pool accounting — `total_liquidity`, `total_lp_tokens`, `accumulated_fees`, and OI. A malicious orchestrator can distort LP withdrawal limits, let one LP withdraw a disproportionate share, or deny service by deflating liquidity.

What it cannot do:

- Inflate an individual trader's payout: close, TP, SL, and liquidation recompute the position from the on-chain commitment.
- Move more than the pool's real USDCx balance: the token contract enforces that independently.

The honest summary is: **the orchestrator is trusted for all pool accounting, not merely OI; trader payouts and the pool's real balance are not at its mercy.** Planned hardening: restrict `update_pool_state` to OI/net PnL and put those writes behind a k-of-n quorum.

### The three-option trilemma

| Option | OI on-chain | Privacy | Trust |
|---|---|---|---|
| Store OI in finalize | Atomic | Leaks size/direction | None |
| Orchestrator writes OI | Visible | Fully private | Orchestrator honest |
| Compute off-chain, show frontend | Not on-chain | Fully private | None |

There is no fourth option that gives private trades, on-chain OI, and no trust assumption simultaneously in Aleo's execution model. The whitepaper argues this is fundamental for any ZK execution model where private witnesses cannot flow into public state.

## Liquidation architecture

Liquidation is the hardest problem: the liquidator must act on a private position without the trader being online.

### Keeper-auth pattern

`open_position()` atomically mints:

- One updated `PositionSlot` to the trader.
- Three `LiquidationAuth` records, one to each keeper in `liquidator_set`.

Each auth mirrors `entry_price`, `size_usdc`, `collateral_usdc`, `is_long`, and `position_id`. Any one keeper can liquidate with its own auth. Up to `N-1` keepers can be offline. Keepers race for the reward; withholding merely cedes the fee.

### On-chain gates

Both close and liquidation recompute the BHP256 commitment from supplied private parameters and compare it to `position_commits[position_id]`.

| Gate | Close | Liquidate | Prevents |
|---|---|---|---|
| BHP256 commitment match | Yes | Yes | Forged position parameters |
| Oracle price + freshness | Yes | Yes | Stale/fabricated price |
| Pool solvency | Yes | Yes | Payout/reward exceeding liquidity |
| Equity < 5% maintenance | No | Yes | Liquidating a healthy position |
| Caller in `liquidator_set` | No | Yes | Non-keeper liquidation |
| `active_position_ids` atomic clear | Yes | Yes | Double-close / close+liquidate race |

```text
pnl = is_long ? size * (exit - entry) / PRICE_PRECISION
              : size * (entry - exit) / PRICE_PRECISION
equity = collateral + pnl
maintenance_margin = size * 50_000 / 1_000_000
assert(equity < maintenance_margin)
liquidator_reward = collateral * 100_000 / 1_000_000
```

A malicious keeper can grief with failed liquidations (wasted gas, no state change) but cannot liquidate a healthy position or overpay itself.

### The unsolved part: detection under privacy

The liquidator set is **permissioned** at launch. The whitepaper does not claim this is decentralized. To make liquidation permissionless, any party must be able to spot an underwater position. But private positions mean a keeper can only check if the liquidation price is visible — and once it is, direction, leverage, and approximate entry are inferable. Public perp venues can have permissionless liquidation only because positions are public. ZKPerp chooses privacy and treats decentralized detection as open research.

## Advanced orders

Limit, take-profit, and stop-loss orders are keeper-executed.

- Order trigger, size, and direction are private.
- On-chain footprint is only a boolean in `pending_orders` keyed by a BHP256 commitment.
- The oracle condition is checked in `finalize` against the live `oracle_prices` mapping, not a keeper-supplied price.
- **Caveat:** the trigger direction is asserted at placement, but execution does **not** re-check that the oracle price crossed the trigger. The orchestrator is trusted to fire only when it has.

## Position commitment scheme

### The inflated-payout attack

Pre-v26, `close_position` accepted `expected_payout` as an unverified private input. A trader could claim an inflated payout and drain the pool.

### The fix

At open, the circuit computes:

```text
commit = BHP256::hash_to_field(PositionCommit {
  entry_price,
  size_usdc,
  collateral_usdc,
  is_long,
  position_id,
})
```

Only `commit` is stored on-chain. On close, liquidation, TP, and SL, the parameters are re-supplied privately, the commitment is recomputed, and finalize asserts equality with the stored hash. The source of private inputs differs by path (trader, keeper auth, orchestrator auth), but the commitment check is the same.

**Security result:** no party can terminate a position with parameters different from those committed at open. A trader cannot inflate payout; a keeper cannot inflate reward or liquidate the wrong size.

**Privacy caveat:** a commitment hides only if the committed tuple has sufficient entropy. `position_id` is the main nonce-like field; the whitepaper does not provide a formal hiding analysis or a minimum-entropy argument. A public hash of a low-entropy position could be guessable if `position_id` is predictable.

## Oracle infrastructure

Three relayers read Chainlink feeds independently:

- BTC/USD and ETH/USD on Ethereum mainnet.
- SOL/USD on Arbitrum.

Each submits under its own Aleo key. The Leo program writes `oracle_prices` only when 2-of-3 agree per asset.

| Compromise | Consequence |
|---|---|
| One key | Can delay a round with a divergent price; cannot commit a false price alone |
| Two keys | Can write an arbitrary price |
| All three offline | Existing price is used; staleness check blocks new opens after 150 blocks |

Relayers are staggered and wait for confirmation, avoiding same-block finalize races. The stronger design is a single transaction carrying a co-signed price report verified in-circuit, e.g. FROST threshold Schnorr or direct Chainlink report verification. Leo does not yet expose the needed secp256k1 verification.

**Important:** all relayers read the **same Chainlink feed**, so redundancy is at the signer level, not the data source. The feed remains the price-trust root.

`max_slippage` is a private trader input, not a protocol constant. A trader who passes `u64::MAX` disables their own slippage check; the protocol only enforces the trader's chosen bound.

## KYC compliance layer

A depth-10 BHP256 Merkle tree supports 1,024 users. Only the root is on-chain.

1. User completes KYC; address is added to an allowlist.
2. Backend rebuilds the tree and admin calls `update_root`.
3. User fetches a Merkle proof and calls `issue_compliance`.
4. The circuit proves: "I am a leaf in the tree whose root matches `compliance_root`."
5. User receives a private `ZKPerpComplianceRecord`, valid up to ~180 days.

At trade time, finalize asserts `!revoked[caller]` and `block.height <= expires_at`. It does **not** re-check `issued_under` against the live root, deliberately, so a root rotation does not invalidate every active trader. The record remains valid until expiry or revocation.

Double issuance is prevented by a Poseidon2 nullifier binding the leaf to the root. Revocation is instant via `revoke_user`; reinstatement via `unrevoke_user`. The compliance admin is fixed at deploy time — no `set_admin` — which the whitepaper lists as an open limitation.

**Privacy note:** identity is off-chain; only the root is public. But the compliance server learns the wallet-to-identity mapping, and the audit endpoint proves enforcement without publishing addresses. This is privacy against the public chain, not against the compliance operator.

## ZK dark pool

`zkdarkpool_v9.aleo` implements batch auctions with sealed orders and a uniform clearing price.

- Orders are submitted as encrypted `OrderAuth` records owned by the operator.
- The operator computes the uniform clearing price off-chain.
- `settle_match()` enforces per-settlement constraints in ZK: clearing price within both limits, nonces unused, escrow sufficient, both orders unexpired.
- `partial_fill()` uses the same gates.

### Privacy boundary

| Information | Visible to |
|---|---|
| Order size, limit price, direction, asset, trader address | Operator only |
| Clearing price, fill size | Operator and both parties via `FillReceipt`; not public |
| Pending orders | Nobody else / no MEV surface |
| Fee-vault total, consumed nonces, settlement occurrence | Everyone |

The operator must see orders to match them. It cannot settle outside limit prices, move more than escrow, or fabricate a fill against a consumed/expired order. Its residual power is censorship, mitigated by expiry and cancel/refund.

**Critical caveats:**

- Batch coordination is off-chain. The contract holds no batch/window state. `batch_root` is operator-supplied metadata, not validated on-chain. The chain guarantees per-settlement validity, not batch integrity.
- The fee is computed but **not collected** on-chain: `settle_match` requires only `gross_cost`, pays the seller `gross_cost`, and increments an unbacked `fee_vault` counter.
- Sell-side deposits are bound to seller by address + asset, not by order nonce; the operator chooses which deposit settles which order.

## Concentrated liquidity AMM

A standalone Uniswap v3-style USDCx/ALEO spot AMM, 0.3% fee, tick spacing 60, Q64 fixed-point.

- LP positions are private records; pool state is public.
- Both legs move real assets: USDCx private token transfers, ALEO via `credits.aleo`.
- Swap math is evaluated client-side, but the contract verifies output capped and input floored against on-chain liquidity, pins the final tick, and accrues fees via `fee_growth_global`.
- Demo-grade, not validated on-chain. Inline `u128` math limits ticks to ±88,440 and per-position liquidity to ≤255. Full range needs a verified 256-bit `mul_div`.
- Swap amounts are public finalize arguments, so large private trades should use the dark pool.

## Findings

1. **Position privacy is real but bounded by aggregates.** The trader's size, entry, and PnL are private; aggregate OI, transaction counts, oracle prices, and the timing of opens/closes remain public.
2. **The OI trilemma is the central architectural constraint.** Private trades + on-chain OI + no trusted aggregator cannot all hold in this execution model. ZKPerp chooses privacy and accepts an orchestrator for pool accounting.
3. **Commitments fix payout integrity, not economic privacy.** The BHP256 commitment closes the inflated-payout attack and binds liquidation parameters, but the public hash and the public oracle price still leak information over time.
4. **Keeper-auth solves liveness, not detection or decentralization.** It lets any of N keepers act without the trader's slot, but the set is permissioned and private detection remains open.
5. **The oracle is on-chain but not source-diverse.** 2-of-3 quorum prevents a single relayer from writing a false price; two colluding keys can. All relayers read the same Chainlink feed.
6. **The dark pool protects against the public, not the operator.** The operator sees full order contents and controls batch composition. Per-settlement validity is ZK-enforced; batch integrity is not.
7. **No funding rate means this is not yet a full perpetual.** The current model is a flat borrow rate. Dynamic funding, partial close, and cross-margin are roadmap items.
8. **Testnet, unaudited, and honest about it.** The whitepaper explicitly says not to use it with real funds and lists the trusted components.

## Limitations and caveats

- **No formal audit.** Circuits and contracts are testnet software.
- **Trusted orchestrator for pool accounting.** It can distort LP withdrawal limits and total liquidity; it cannot inflate individual trader payouts or move the pool's real token balance.
- **Permissioned liquidation set.** Censorship/liveness risk; decentralized detection under privacy is unsolved.
- **Dark-pool operator sees orders and controls batches.** Censorship risk; no in-circuit matching.
- **Dark-pool fee is not actually collected.** The accounting counter is unbacked.
- **Compliance admin is fixed at deploy time.** No multisig/governance rotation.
- **No dynamic funding rate.** Longs/shorts do not pay each other based on OI; the model is a flat borrow rate.
- **No partial close / no cross-margin / no portfolio margin.**
- **One position per direction per market.** A trader cannot hold two BTC longs concurrently.
- **20x max leverage, 5% maintenance margin.** Not comparable to venues with higher leverage and risk tiers.
- **Record dust.** Liquidations and settled orders leave dead records; UX handles it, but wallet state grows.
- **Leo language constraints.** Both ternary branches evaluate; finalize arguments are public; 32-call limit; 2M variable limit forced contract splitting.
- **Commitment hiding is not formally analyzed.** Low-entropy tuples could be guessable if `position_id` is predictable.
- **Public transaction counts and timing.** Open/close activity is visible even when contents are not.
- **AMM is demo-grade.** Bounded tick/liquidity envelope; not audited; swap amounts public.
- **Aleo-specific.** The design does not directly port to EVM without a private-note system or a different proof stack.

## Hackathon preparation

### What this paper supports building

- A **private perp risk attestation layer**: prove aggregate OI, solvency, and insurance coverage without revealing individual positions.
- A **leakage passport** for private perp venues: enumerate what is public (aggregates, timing, oracle, commitments) and attach a privacy claim per observer.
- A **threshold keeper/liquidation design**: 1-of-N auth records plus a proof that the position is underwater, with a decentralized detection mechanism.
- A **commitment scheme auditor**: check binding, domain separation, entropy, and replay protection in position commitments.
- A **privacy-preserving funding module**: add the missing dynamic funding rate while keeping individual positions private.
- A **dark-pool batch-integrity proof**: prove the batch root, ordering policy, and clearing price without revealing orders.

### What this paper does not support building directly

- A production mainnet perp with real funds.
- A trustless OI oracle from private positions.
- A permissionless private liquidation network.
- A fully private dark pool that hides orders from the operator.
- An audited cross-margin engine.

### Demo script (3 minutes)

1. Open a private BTC long. Show the explorer: only a commitment and aggregate OI, no size/entry/direction.
2. Show the public oracle price and the commitment; explain what an observer can and cannot infer.
3. Liquidate via a keeper auth. Show the commitment check and the 5% margin gate.
4. Try an inflated close payout. Show the circuit reject it.
5. Show the OI trilemma: turn on atomic OI and watch size/direction leak in finalize; turn it off and show the orchestrator trust.
6. Show the dark pool: operator sees the order, public sees only a settlement; then show the fee-not-collected bug.
7. Export a "privacy passport" with the leakage surface and trust assumptions.

### Judge Q&A prep

- **"Is this really private?"** Individual positions are private records; aggregates, timing, oracle prices, and commitments are public. It is not end-to-end privacy.
- **"Who can liquidate?"** A permissioned 1-of-N keeper set. Correctness is on-chain; detection/decentralization is open.
- **"What if the orchestrator lies?"** It can distort pool accounting and LP limits, but cannot inflate a trader payout or move the pool's real balance. Hardening is planned.
- **"Why no funding rate?"** It is a roadmap item. The current model is a flat borrow rate, so it is not yet a full perp.
- **"Why Aleo?"** Private records and in-circuit verification are native. Porting to EVM requires a different privacy stack.
- **"Is it audited?"** No. Testnet only.

### Claims to avoid

- "Fully private perpetuals."
- "Trustless liquidation."
- "Permissionless liquidation."
- "The dark pool hides orders from everyone."
- "Production-ready."
- "Dynamic funding is implemented."

## Hackathon-level idea: VeilRisk

**One-liner:** A privacy-preserving risk attestation and leakage passport for private perp DEXs.

**User:** Private perp venues, LPs, insurers, auditors, and institutional traders who need proof that a venue is solvent without exposing positions.

**Core flow:**

1. Ingest a private perp venue's public state: commitments, aggregate OI, oracle prices, pool balances, transaction timing.
2. Compute a **leakage graph**: which observer learns which information at which lifecycle phase (open, fund, liquidate, close).
3. Generate a **risk attestation** in ZK: aggregate OI within bounds, insurance fund covers a stress scenario, no single position exceeds a concentration limit — without revealing individual positions.
4. Publish a **Privacy Passport**: protected information, exposed aggregates, trust assumptions, and open leakage channels.
5. Run a **liquidation detection simulator**: show how much position information a keeper needs to detect an underwater position, and the privacy cost of each detection design.
6. Optionally deploy a threshold-keeper module and a dynamic funding module that consumes the attestation.

**Demo moment:** A private perp looks fully opaque on the explorer. VeilRisk's passport shows that the last three liquidations leaked direction and approximate size through timing and oracle moves. Then the ZK attestation proves the venue is solvent without revealing a single position.

**Moat:** The leakage taxonomy, the attestation circuits, and the cross-venue privacy/risk dataset.

**Cons:**

- ZK attestations of aggregate risk are hard to compute from private state; the venue may need to reveal more than it wants.
- The passport is only as good as the venue's cooperation.
- Liquidation detection under privacy is genuinely unsolved; the simulator may expose uncomfortable tradeoffs.
- The product is infrastructure/audit, not a consumer trading app.
- It needs a real private perp venue to be compelling; a mock venue weakens the demo.

**Integration with the existing portfolio:** VeilRisk is the privacy/risk audit layer for Perp Forge. It consumes CipherClock's funding spec, TailMark's liquidation-tail estimates, and the private-perp architecture patterns from ZKPerp.
