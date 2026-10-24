# Boundary Radar — A Privacy Lint for Zcash Wallets and Agents

## Metadata

- **Target hackathons:** Zcash (primary), ETHGlobal (privacy tooling)
- **Tracks:** Zcash, privacy, wallets, analytics, linkability
- **Papers used:** On the linkability of Zcash transactions (1712.01210), Orchard audit (2026), Halo (2019/1021)
- **One-liner:** A local privacy lint that shows exactly how a wallet's shield/unshield behavior can be linked, then plans a less linkable path.

## The problem

Zcash's shielded pool hides sender, receiver, and amount inside the pool. It does not hide the **boundary**: when coins enter or leave the pool, the public amount (`vpub_old` / `vpub_new`) is visible. The 2017 study exploited this with a simple heuristic: find a shielding transaction and a later de-shielding transaction with exactly equal public amounts, where the amount pair is globally unique. It matched 10,075 pairs moving 919,220 coins, or 31.5% of the 2.9M coins that entered the Sprout pool. 96% of matches occurred within two hours.

A modern wallet can still leak:

- a round-trip of an exact amount,
- a shield followed by a de-shield to a known exchange deposit address,
- a withdrawal amount that matches a known invoice,
- timing correlation between a shielded receive and a transparent spend,
- note consolidation that reveals a balance range,
- a single large note that is later split into recognizable payments.

Users are told "use a shielded address" and assume they are private. They are not. The leak is at the boundary, and it is visible to chain analysts, exchanges, and counterparties.

## The solution

Boundary Radar is a **local, privacy-preserving wallet lint** for Zcash and Zcash-integrated agents.

1. **Local graph.** The wallet builds a graph of its own shielding, de-shielding, transfers, and note consolidations. No data leaves the device.
2. **Linkability lens.** It runs a modernized version of the 2017 heuristic:
   - exact-amount round trips,
   - fee-adjusted matches,
   - timing proximity,
   - unique-amount matches,
   - known-address clustering (exchange deposits, merchant addresses),
   - consolidation fingerprints.
3. **Privacy score.** It reports a score per transaction and per wallet: "this withdrawal is 82% linkable to your shielded payroll"; "this consolidation reveals a 4.2 ZEC balance range."
4. **Counterfactual planner.** It proposes alternatives:
   - split a withdrawal into non-round amounts,
   - add a fully shielded hop,
   - batch multiple inflows/outflows,
   - delay a de-shield,
   - avoid exact invoice amounts,
   - use a fresh address and avoid exchange deposit clustering,
   - keep a remainder in the pool.
5. **Agent integration.** An AI agent that pays in ZEC runs the lint **before** signing. If the proposed payment creates a boundary link, the agent can split, delay, or ask for a different settlement path.
6. **Privacy receipt.** The wallet produces a local receipt of the linkability analysis and the chosen mitigation. It does not publish the analysis.
7. **Exchange/merchant mode.** A merchant can run the lint on its own incoming payments to avoid creating a linkable pattern for its customers.

## Why this is out of this world

Most privacy wallets show a balance and a "shielded" badge. Boundary Radar shows the **attack surface of the user's own behavior**. It turns a 2017 research heuristic into a live, local, actionable product, and it extends it to AI agents that otherwise automate the same mistakes at scale.

The demo: a user withdraws exactly 1.0 ZEC to an exchange. Boundary Radar flags an 87% link to a shielded payroll, shows the matching deposit, and proposes a split into 0.63 + 0.37 with a 40-minute delay. The link disappears.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| Zcash linkability | Round-trip heuristic, exact amounts, timing, unique-amount matches, mining-pool payouts |
| Orchard audit | Current Zcash circuit and wallet safety; PCZT signer flow; why "shielded" is not "private" |
| Halo | The proof system lineage behind Orchard; context for why the pool is private but the boundary is not |

## Intuitive workflow

1. **The user receives shielded ZEC.** The wallet notes the incoming note.
2. **The user wants to withdraw to an exchange.** It enters the amount and address.
3. **Boundary Radar simulates the withdrawal.** It compares the amount and timing to the user's own history and known address clusters.
4. **It flags a link.** "This exact amount matches your payroll note; the exchange deposit is a known cluster."
5. **It proposes alternatives.** Split, delay, add a hop, use a different amount.
6. **The user picks one.** The wallet builds the transaction and re-runs the lint.
7. **The link score drops.** The user signs.
8. **The agent does the same.** An agent paying a merchant runs the lint before every ZEC payment.

## Demo (5 minutes)

**Act 1 — The leak.** Show a 1.0 ZEC shield and a 1.0 ZEC de-shield two hours later. The 2017 heuristic links them.

**Act 2 — The lint.** Boundary Radar flags the exact match, the timing, and the exchange cluster.

**Act 3 — The fix.** Split the withdrawal, add a delay, and keep a remainder. The link score falls from 87% to 12%.

**Act 4 — The agent.** An AI agent tries to pay an invoice with an exact amount. The lint blocks it and proposes a split. The agent adapts.

**Act 5 — The receipt.** The wallet shows a private privacy receipt. No data leaves the device.

**Close:** "Shielding hides the pool. Your behavior can still open the door."

## Hackathon fit

- **Zcash:** directly addresses a real Zcash privacy failure with a usable wallet tool.
- **ETHGlobal:** privacy analytics + UX; strong visual demo.
- **Judging:** grounded in a peer-reviewed-style measurement, actionable, local-first, no surveillance.
- **Adoption:** Zcash wallets, custodians, merchants, agent payment SDKs.

## Cons and risks

- **Heuristic, not proof.** A link score is a candidate ranking, not proof of ownership. False positives can scare users.
- **Local data limits.** The wallet sees only its own history; chain-wide clusters require external data, which raises privacy concerns.
- **Behavioral burden.** Splitting and delaying payments is annoying; users may ignore the lint.
- **Adversary adaptation.** An analyst with more data can beat the heuristic; the score is not a guarantee.
- **Regulatory pressure.** Exchanges may require exact amounts and destination addresses, reducing mitigation options.
- **No formal privacy model.** The tool improves hygiene, not cryptographic privacy.
- **Zcash protocol changes.** The heuristic is based on 2017 Sprout; modern Orchard behavior differs. The tool must be recalibrated.

## Judge Q&A

**"Doesn't Zcash already hide transactions?"** It hides the inside of the pool, not the boundary amounts or timing. The 2017 study linked 31.5% of entering coins with a simple heuristic.

**"Is this surveillance?"** No. It runs locally on the user's own wallet and never uploads the graph. It is a privacy lint, not a chain analytics product.

**"Can it guarantee privacy?"** No. It reduces a specific linkability pattern. The demo states the residual leaks.

**"Why would an agent need this?"** Agents automate payments at scale; without a lint, they will create the same round-trip patterns faster and more consistently.

**"What about Orchard?"** The boundary problem remains; the exact heuristic needs recalibration for Orchard-era wallets.

## Claims to avoid

- "Makes Zcash untraceable."
- "Guarantees unlinkability."
- "Detects all links."
- "Replaces a privacy audit."
- "Production-ready."

## One-sentence pitch

**Boundary Radar is a local privacy lint that shows Zcash users and agents how their shield/unshield behavior can be linked, and plans a less linkable path before they sign.**
