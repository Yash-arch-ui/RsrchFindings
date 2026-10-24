# Track Map — Zcash, Monad, ETHGlobal

## Zcash hackathon

**What the judges will reward:** real use of Zcash privacy primitives, not a generic ZK demo with a Zcash logo. Shielded settlement, Orchard, viewing keys, Halo2, PCZT, linkability, and wallet safety are the native surfaces.

**Best ideas:**

| Idea | Why it fits Zcash | Demo asset |
|---|---|---|
| VeilMandate | Shielded agent payments + task-scoped privacy + boundary hygiene | Shielded checkout + unlinkability graph |
| GhostCart | Shielded procurement escrow + sealed bids | Private RFQ + shielded payout |
| Boundary Radar | Directly addresses Zcash linkability | Linkability score before/after mitigation |
| Orchard Sentinel | Directly improves Zcash/Halo2 security | Empty-Sinsemilla exploit + CI catch |
| Halo Mandate | Recursive proof lineage from Halo/Orchard | One proof for 40 payments |
| ShadeScore | Shielded credit + issuer-hidden predicates | Private loan approval |

**Recommended Zcash build:** VeilMandate + Boundary Radar. If the team is stronger in cryptography than product, build Orchard Sentinel + Boundary Radar. If the team wants a pure ZK flex, build Halo Mandate.

**Demo honesty:** state that Zcash hides the pool, not timing, network metadata, or boundary amounts. Show the residual leak and the mitigation.

## Monad hackathon

**What the judges will reward:** high-throughput execution, real EVM integration, agentic commerce, account abstraction, and a use case that needs cheap per-transaction checks. Monad's value is not "privacy" by itself; it is the ability to run policy and verification on every agent action.

**Best ideas:**

| Idea | Why it fits Monad | Demo asset |
|---|---|---|
| IntentGuard | Per-payment context/policy checks; on-chain receipts | Malicious catalog blocked |
| Sovereign Runtime | Account abstraction + deterministic policy + delegation | Jailbroken agent cannot drain |
| ContextCurtain | Per-message flow decisions at high throughput | Privacy-utility curve |
| Veil402 | x402-style API payments + batch settlement + receipts | Private API marketplace |
| VeilMandate | Mandate registry + merchant admission on Monad; Zcash settlement | Cross-chain private checkout |

**Recommended Monad build:** Sovereign Runtime + IntentGuard. It is the strongest security story and uses Monad for cheap policy checks. If the team wants a marketplace, build Veil402. If the team wants a cross-chain flagship, build VeilMandate with a Monad registry and a mocked Zcash settlement adapter.

## ETHGlobal fallback

**Best ideas:** IntentGuard, ContextCurtain, Veil402. They are EVM-native, demoable, and fit agentic-commerce tracks. VeilMandate also works if the team can mock the Zcash leg.

**What to avoid at ETHGlobal:** a pure Zcash circuit demo with no EVM integration; a generic "private payments" app with no agent or commerce workflow; a dashboard with no enforcement.

## Cross-hackathon portfolio

If the team can build one codebase for both:

- **Core:** agent mandate + policy kernel + receipt/evidence chain.
- **Monad deployment:** mandate registry, policy checks, receipts, x402 gateway.
- **Zcash deployment:** shielded settlement, note hygiene, linkability lint.
- **Flagship story:** VeilMandate.
- **Security demo:** IntentGuard.
- **Privacy proof:** Boundary Radar.

This gives one coherent product with two settlement paths, rather than two disconnected hackathon projects.

## 5 humans + 10 coding agents

| Workstream | Humans | Agents | Output |
|---|---:|---:|---|
| ZK / credentials | 1 | 2 | BBS+ presentation, delegation, predicate proofs |
| Zcash settlement | 1 | 2 | Shielded notes, boundary lint, wallet integration |
| Monad contracts | 1 | 2 | Mandate registry, policy kernel, receipts, x402 gateway |
| Agent runtime / LLM | 1 | 2 | Planner, tool pinning, context capture, attack demo |
| Frontend / narrative | 1 | 2 | Checkout, graph view, privacy-utility curve, deck |
| Integration / QA | all | shared | End-to-end demo, failure modes, judge rehearsal |

## Clean-sweep sequencing

1. **Foundation:** mandate schema, policy kernel, receipt format, privacy passport.
2. **Zcash:** shielded settlement + boundary lint.
3. **Monad:** registry + policy checks + x402 gateway.
4. **Attack demo:** valid-but-wrong payment, stable-handle linkage, boundary round trip.
5. **Fix demo:** context commitment, fresh per-call views, note splitting.
6. **Polish:** one narrative, one judge Q&A, one 5-minute demo.

No time barrier is assumed. Scope to the demo, not the production system.
