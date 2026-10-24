# Privacy x Perpetuals Research

Paper-by-paper findings for privacy-preserving perpetual futures and private order matching. Each paper file contains a full summary, model/architecture, equation or mechanism ledger, limitations, hackathon preparation, and an evolved flagship idea. The synthesis file combines the five papers into a privacy-native perp stack.

## Papers

| # | File | Paper | Type | Key result | Hackathon angle |
|---|---|---|---|---|---|
| 1 | [01-marsh-2026-encrypted-mempools-funding.md](01-marsh-2026-encrypted-mempools-funding.md) | *Reveal, Correct, Then Pay: Encrypted Mempools and Perpetual Funding Security* (Marsh, 2026) | Theory / market design | Commit-reveal creates a reaction gap `delta`; funding distortion is multiplied by `g(delta)`; privacy can raise self-authored manipulation value; fix is reveal-correct-measure-pay | CipherClock: decryption-aware funding security |
| 2 | [02-deboer-2026-zkperp.md](02-deboer-2026-zkperp.md) | *ZKPerp — Privacy-First Perpetual Futures* (de Boer, 2026) | System design / engineering whitepaper | Private position records + BHP256 commitments + 1-of-N keeper auth + 2-of-3 oracle; OI trilemma forces trusted orchestrator; no dynamic funding yet | VeilRisk: private perp risk attestation and leakage passport |
| 3 | [03-arote-2026-sok-private-order-matching.md](03-arote-2026-sok-private-order-matching.md) | *SoK: Privacy-Preserving Order Matching* (Arote, Saiz, Kiffer, 2026) | Systematization | 22 protocol families; privacy is phase/observer/information-specific; no protocol is end-to-end private; existence and settlement leak | VeilMap: lifecycle leakage scanner for perp DEXs |
| 4 | [04-govindarajan-2021-rialto.md](04-govindarajan-2021-rialto.md) | *Privacy-Preserving Decentralized Exchange Marketplaces* (Govindarajan et al., 2021) | Protocol + implementation | Rialto: Pedersen commitments + MPC sorting + maximal fair matching + homomorphic settlement + Waksman shuffle; relaxed order-rate confidentiality; spot only | ShadeBook: private batch-auction perp |
| 5 | [05-baum-2021-p2dex.md](05-baum-2021-p2dex.md) | *P2DEX: Privacy-Preserving Decentralized Cryptocurrency Exchange* (Baum, David, Frederiksen, 2021) | UC-secure protocol + implementation | Insured MPC + threshold-signature burner addresses + publicly verifiable matching; optimistic 2 transactions; actively dishonest majority with one honest server | Bonded Shade: insured MPC clearinghouse for perp intents |

## Quick comparison

| Dimension | Paper 1 | Paper 2 | Paper 3 | Paper 4 | Paper 5 |
|---|---|---|---|---|---|
| Core object | Funding signal under encrypted mempool | Private position + commitment | Order lifecycle privacy | Private order + balance matching | Cross-ledger private exchange |
| Privacy mechanism | Commit-reveal ordering | Aleo private records + ZK | Taxonomy of MPC/HE/TEE/ZKP/DP | Pedersen + MPC + ZK range proofs | Secret sharing + publicly verifiable MPC + threshold signatures |
| Main threat | Self-authored funding manipulation | Liquidation hunting, front-running, copy trading | Cross-phase leakage | Operator leakage, front-running | Front-running, server theft/cheating |
| Main fix | Correction buffer + eligibility lag | Commitments + keeper auth + oracle quorum | Phase/observer-specific claims | MPC sorting + shuffle + aggregate fee | Server bonds + threshold custody + refunds |
| Empirics | None | Testnet system, no audit | 22 protocols | Hyperledger Fabric + MP-SPDZ benchmarks | SPDZ2k/Fresco benchmarks |
| Perp coverage | Funding only | Full perp (no funding) | None (order matching) | Spot only | Spot cross-chain only |
| Hackathon strength | Novel attack + design rule | Working private perp | Disclosure map | Private batch matching | Insured settlement |
| Main weakness | Stylized, no calibration | Trusted orchestrator, no funding | Not a protocol | Not perps | Not perps, one-shot |

## The privacy x perp stack

The five papers cover different layers:

1. **Paper 1 — funding layer.** Privacy changes the information schedule; the application must not pay against the first post-reveal state. This is the missing "funding security" chapter in private perp design.
2. **Paper 2 — position layer.** Private positions, commitments, keeper-auth liquidation, on-chain oracle quorum, and the OI trilemma. This is the closest thing to a working private perp.
3. **Paper 3 — disclosure layer.** A way to describe what is private, from whom, in which phase. It prevents "private perp" claims from hiding leakage at settlement, funding, or liquidation.
4. **Paper 4 — matching layer.** Private batch matching with MPC, commitments, and a shuffle. It shows how to hide order rates and balances, and what still leaks (rank, top-K, participation).
5. **Paper 5 — settlement layer.** Insured MPC, threshold custody, public verifiability, and refunds. It shows how to make private matching economically safe and composable.

The synthesis is: **private positions need private matching, private funding, and private liquidation, but each layer leaks into the next.** The product opportunity is not "one more private DEX"; it is the **privacy/risk stack** that makes private perps auditable, solvent, and resistant to funding manipulation.

## Recommended flagship direction

Build **VeilPerp** — a privacy-native perpetual exchange framed as **sealed books with public invariants**:

- **Sealed-Book Solvency Envelope**: prove worst-case solvency over hidden positions under a committed stress set, without revealing any position.
- **Private position notes** and BHP256/Schnorr-style commitments.
- **Private batch order flow** using commit-reveal, threshold encryption, or MPC/FHE matching.
- **CipherClock funding with a manipulation-immunity certificate**: reveal, correct, measure, then pay; publish the bound on concealed-batch extraction.
- **Threshold liquidation**: keeper proofs of underwaterness without public position disclosure.
- **VeilPassport as an adversary-equivalence certificate**: prove or disprove a venue's privacy claim for a declared observer, not just a leakage map.
- **Bonded Shade**: challengeable private clearing with privacy-loss bonds, server/keeper bonds, public verifiability, and refund paths.

See [06-privacy-perp-flagship-ideas.md](06-privacy-perp-flagship-ideas.md) for the full flagship slate, once-a-decade primitives, impossible demos, workflows, cons, demo script, team split, and judge Q&A.

## Honest scope notes

- **Paper 1** is a theory paper. Its numerical examples are dimensionless, not venue-calibrated. Do not present them as observed manipulation.
- **Paper 2** is a testnet whitepaper, not peer-reviewed and not audited. It explicitly documents a trusted orchestrator, a permissioned liquidation set, a dark-pool operator that sees orders, and no dynamic funding rate.
- **Paper 3** is a systematization, not a protocol. It does not validate implementation claims; it records them.
- **Paper 4** is spot-only, unit-quantity, permissioned Hyperledger Fabric. It does not support perps, margin, shorting, funding, or liquidation.
- **Paper 5** is a one-shot cross-chain exchange, not a perpetual. Its matching is constant-amount and price-only. Its strong guarantee assumes at least one honest server.
- **Do not claim** "fully private", "trustless", "permissionless liquidation", or "production-ready" for any of these designs without the corresponding implementation and audit.

## Data and access note

All five documents were retrieved and read from the user-supplied links:

- Paper 1: `https://arxiv.org/pdf/2607.13832`
- Paper 2: `https://hwdeboer1977.github.io/ZKPerp/zkperp-whitepaper-v5.html`
- Paper 3: `https://arxiv.org/pdf/2610.12535`
- Paper 4: `https://arxiv.org/pdf/2111.15259`
- Paper 5: `https://researcher.itu.dk/ws/files/86436636/2021_283.pdf`

Paper 1 and Paper 3 are arXiv preprints. Paper 2 is a project whitepaper. Paper 4 is an arXiv version of a 2021 paper. Paper 5 is the full version of an ACNS 2021 extended abstract.
