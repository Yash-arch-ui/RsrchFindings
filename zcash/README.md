# Zcash Privacy Research

Zcash-only privacy research for the Zcash hackathon. This folder covers the proof system, the historical linkability measurement, and the current Orchard circuit audit.

## Papers

| # | File | Source | Type | Key result | Hackathon use |
|---|---|---|---|---|---|
| 1 | [01-halo-recursive-proofs-2019.md](papers/01-halo-recursive-proofs-2019.md) | Halo: *Recursive Proof Composition without a Trusted Setup* (Bowe, Grigg, Hopwood, 2019) | Construction + implementation | First practical recursive proof composition without a trusted setup; nested amortization; Halo2/Orchard lineage | Recursive agent authority, proof aggregation, light-client proofs |
| 2 | [02-zcash-linkability-2017.md](papers/02-zcash-linkability-2017.md) | *On the linkability of Zcash transactions* (Quesnelle, 2017) | Empirical measurement | 31.5% of coins entering the Sprout pool matched via exact-amount round trips; 96% within two hours | Boundary Radar, privacy lint, shielded payment hygiene |
| 3 | [03-orchard-audit-2026.md](papers/03-orchard-audit-2026.md) | zkSecurity *Audit of Zcash: Orchard* (2026) | Security audit + formal verification | No live consensus bug; four latent gadget/tooling findings; Lean/Clean formalization of Orchard Action circuit | Orchard Sentinel, PCZT guard, Halo2 footgun CI |

## What this folder establishes

- **Halo is the recursive-proof lineage behind Halo2 and Orchard.** It removes the trusted setup but is recursive, not fully succinct: proof size and verification time are independent of recursion depth, while verifier work is linear in circuit size.
- **Shielding is not the same as unlinkability.** The 2017 paper exploits public boundary amounts and timing; modern Orchard changes the format, not the boundary principle.
- **Orchard is currently sound, but the audit found integration footguns.** Empty Sinsemilla messages, unbound fixed-base scalar windows, PCZT ciphertext authentication, and trailing proof bytes are exactly the bugs a Zcash-adjacent team can reintroduce.
- **Formal verification without a source link is not enough.** The Orchard audit's Lean port was hand-translated with AI assistance and could have formalized a different circuit. That gap is a product opportunity.

## Zcash hackathon angles

1. **Wallet and signer safety:** PCZT guard, proof-size enforcement, ciphertext authentication.
2. **Circuit CI:** Halo2 footgun tests, Clean/Lean proofs, regression detection.
3. **Privacy UX:** boundary-amount/timing lint, shielded payment planner.
4. **Recursive proofs for agents:** proof-carrying mandates, delegated authority, light-client verification.
5. **Shielded commerce:** private agent payments, sealed procurement, private credit.

## Access note

The Halo ePrint PDF was Cloudflare-blocked; the file uses the local abstract/metadata and cross-checked public Halo/Halo2 material, with claims tagged. The Orchard audit and linkability paper were read in full locally.

## Ideas that use this folder

- [Boundary Radar](../flagship-ideas/08-boundary-radar.md)
- [Orchard Sentinel](../flagship-ideas/07-orchard-sentinel.md)
- [Halo Mandate](../flagship-ideas/09-halo-mandate.md)
- [VeilMandate](../flagship-ideas/01-veilmandate.md)
- [GhostCart](../flagship-ideas/02-ghostcart.md)
