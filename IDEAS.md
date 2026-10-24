# Flagship Ideas — Zcash + Monad

Ten hackathon ideas built from the 16-source research set. Each file contains the deep problem, the solution, the source mapping, an intuitive workflow, a demo, hackathon fit, cons, judge Q&A, and claims to avoid. Each idea file lives directly in its primary topic folder; this page is the cross-topic index.

**Primary targets:** Zcash hackathon and Monad hackathon. ETHGlobal is the fallback. See [TRACKS.md](TRACKS.md) for the track-by-track build map.

## The slate

| # | Idea | Target | One-liner | Papers used |
|---|---|---|---|---|
| 1 | [VeilMandate](privacy-x-agentic-ecommerce/01-veilmandate.md) | Zcash + Monad | One human approval, many merchant choices, zero cross-merchant linkage | MinMandate, AP2, BBS+, issuer hiding, Zcash linkability, Orchard audit |
| 2 | [GhostCart](privacy-x-agentic-ecommerce/02-ghostcart.md) | Zcash + Monad | Private sealed-bid procurement with shielded settlement | MinMandate, BBS+, issuer hiding, Zcash linkability, AP2, A2A SoK |
| 3 | [IntentGuard](privacy-x-agentic-ecommerce/03-intentguard.md) | Monad + Zcash | A signed mandate is not intent; bind the context before signing | AP2, SoK LLM agents, AESP, TIVA, MinMandate, Cloaked |
| 4 | [Sovereign Runtime](privacy-x-agentic-ecommerce/04-sovereign-runtime.md) | Monad + Zcash | An agent wallet where the model can reason but cannot move money outside policy | AESP, TIVA, Cloaked, BBS+, delegation, MinMandate |
| 5 | [ContextCurtain](privacy-x-agentic-ecommerce/05-contextcurtain.md) | Monad + Zcash | Multi-agent commerce without leaking the secrets that close the deal | MAGPIE, ConfAIde, PrivacyChecker, SoK LLM agents, A2A SoK |
| 6 | [ShadeScore](privacy/06-shadescore.md) | Zcash + Monad | Prove creditworthiness without revealing history, counterparties, or human | BBS+, issuer hiding, delegation, Zcash linkability, A2A SoK, TIVA |
| 7 | [Orchard Sentinel](zcash/07-orchard-sentinel.md) | Zcash | Verifiable circuit CI that catches the Orchard audit's gadget footguns | Orchard audit, Halo, Zcash linkability |
| 8 | [Boundary Radar](zcash/08-boundary-radar.md) | Zcash | Local privacy lint for shield/unshield linkability | Zcash linkability, Orchard audit, Halo |
| 9 | [Halo Mandate](zcash/09-halo-mandate.md) | Zcash + Monad | One recursive proof of an agent's entire authority and payment history | Halo, Orchard audit, MinMandate, BBS+, issuer hiding, Zcash linkability |
| 10 | [Veil402](privacy-x-agentic-ecommerce/10-veil402.md) | Monad + Zcash | Private pay-per-use APIs with sealed quotes, shielded batches, and delivery receipts | A2A SoK, MinMandate, Cloaked, AESP, TIVA, AP2, Zcash linkability |

## Recommended portfolio

**Clean-sweep play:** build **VeilMandate** as the flagship, with **IntentGuard** as the visible security layer and **Boundary Radar** as the privacy proof. Add **Orchard Sentinel** if the team wants a Zcash-native developer-tool track. Add **ContextCurtain** if the demo needs a multi-agent "wow" moment.

**Why VeilMandate:** it combines the strongest research findings (adaptive merchant selection, stable-handle leakage, context binding, shielded settlement) into one product with a clear attacker and a clear fix. It fits both Zcash and Monad.

**Why IntentGuard:** it turns the AP2 pre-signing gap into a live exploit demo. Judges remember a valid signature paying the wrong merchant, then being blocked by a context hash.

**Why Boundary Radar:** it is the most Zcash-specific, locally runnable, and immediately useful tool. It also supplies a measurable privacy result.

**Why Orchard Sentinel:** it is the strongest Zcash ecosystem contribution. It is less consumer-flashy but high-signal for a Zcash judge.

**Best two-track split:**

- **Zcash track:** VeilMandate + Boundary Radar + Orchard Sentinel.
- **Monad track:** IntentGuard + Sovereign Runtime + Veil402.
- **Cross-track flagship:** VeilMandate with a Monad mandate registry and Zcash settlement.

## Scoring

| Idea | Novelty | Technical depth | Demo | Feasibility | Judge appeal | Moat | Total |
|---|---:|---:|---:|---:|---:|---:|---:|
| VeilMandate | 5 | 5 | 5 | 3 | 5 | 5 | 28 |
| IntentGuard | 5 | 4 | 5 | 4 | 5 | 4 | 27 |
| Boundary Radar | 4 | 4 | 5 | 5 | 4 | 4 | 26 |
| ContextCurtain | 5 | 4 | 5 | 3 | 5 | 4 | 26 |
| Sovereign Runtime | 4 | 4 | 5 | 4 | 4 | 4 | 25 |
| ShadeScore | 4 | 5 | 4 | 3 | 4 | 5 | 25 |
| Orchard Sentinel | 4 | 5 | 4 | 3 | 4 | 5 | 25 |
| Veil402 | 4 | 4 | 4 | 4 | 4 | 4 | 24 |
| GhostCart | 4 | 4 | 4 | 3 | 4 | 4 | 23 |
| Halo Mandate | 5 | 5 | 3 | 2 | 5 | 5 | 25 |

Scores are design-stage judgments, not evidence. Feasibility assumes a hackathon team with strong ZK/MPC support and no strict time limit.

## What makes an idea "out of this world"

1. **It hides a market, not just a transaction.** VeilMandate, GhostCart, Veil402, and ShadeScore hide the workflow or the strategy, not only the payment.
2. **It turns an attack into a demo.** IntentGuard and Boundary Radar show a concrete leak, then close it.
3. **It creates a new primitive.** Halo Mandate composes authority recursively; Orchard Sentinel makes circuit proofs source-linked.
4. **It has a measurable result.** Boundary Radar and ContextCurtain produce privacy-utility curves; MinMandate produces a task-success delta.
5. **It has an honest trust model.** Every idea states what still leaks and who is trusted.

## What to avoid

- Claiming "fully private" or "trustless" for any of these.
- Claiming a Zcash payment hides timing, network metadata, or boundary amounts.
- Claiming AP2/BBS+/Halo are production-ready in the demo.
- Claiming a bridge is trustless.
- Claiming a heuristic link score is proof of ownership.
- Hiding the access limitations of the ePrint papers.

## Source map

- **Zcash audit and linkability:** `zcash/papers/`
- **Credentials and delegation:** `privacy/papers/`
- **Agentic commerce and LLM privacy:** `privacy-x-agentic-ecommerce/papers/`
- **Paper 12 and 13 in the user's list are the same arXiv ID** (`2608.23858`); analyzed once.
- **Paper 8 has two ePrint links** (`2025/824` and `2026/555`); both are analyzed separately (BBS+ AC spec and issuer hiding).
