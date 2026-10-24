# MinMandate: Private Task-Scoped Payment Authorization for Adaptive Agent Workflows

## Metadata

- Authors: Ge Gao, Haining Yu (corresponding), Zhichao Liu, Dongyang Zhan, Yuanxiao Zhu, Zhongyun Hua; Harbin Institute of Technology.
- Date: ePrint received 2026-08-13, approved 2026-08-15.
- Venue/ID: Cryptology ePrint Archive 2026/1674, "Cryptographic protocols", preprint, CC BY-NC-ND.
- Length: 25 pages in the retrieved preprint.
- Type: protocol design, security reductions, implementation, evaluation.
- Read status: local abstract/metadata read; full preprint retrieved from GitHub (`paper/minmandate_preprint.pdf`) and read. Tags: [A] abstract, [P] preprint, [I] inference.

## One-paragraph summary

MinMandate targets a payment-layer gap in agentic commerce. AP2-style mandates make the user name merchants before execution and expose reusable holder identifiers across calls, a poor fit for workflows where the agent discovers the right service only after intermediate results and a source of cross-merchant linkage. MinMandate gives the agent one user-signed, task-scoped mandate over service classes, merchant-selection predicates, a total budget, single-use spend slots, and expiry. Each paid call derives fresh service and spend views disclosing only what the merchant or redemption wallet needs, tied by a fresh per-call consistency binding. BBS credentials, issuer hiding, one-time serials and funding tags, merchant acknowledgements, and atomic redemption complete the construction. On 96 AgentDojo tasks, when 50% of merchants are unavailable, MinMandate improves task success by 32.7 percentage points on average over AP2 preauthorizing one merchant per service class across four planners [A][P]. Reintroducing a reusable public handle in the Stable Handle ablation raises attacker task-recovery success by 27.4 percentage points on average [A][P].

## Problem and threat model

AP2 works when the merchant is known at authorization time. Complex paid workflows violate that assumption: the agent may need to reorder calls, substitute an unavailable merchant, or choose based on a quote. Pre-authorizing every merchant shifts prediction work to the user; binding one merchant per service class causes coverage failures. AP2 also exposes a stable holder identifier, `open_checkout_mandate.cnf.kid`, and a public holder-key thumbprint in the full-wire profile, letting observers join separate paid calls and infer broader intent [P].

For **soundness**, the agent and any subset of policy-compliant merchants are fully malicious and may collude. They adaptively choose requests, quotes, acknowledgements, and redemption inputs, and may replay, interleave, or race calls. User, wallet, and setup are honest. For **external-payment-layer privacy**, user, agent, and wallet are honest; the adversary is colluding policy-compliant merchants plus external observers who pool transcripts. Privacy is defined only after declared leakage: task decomposition, selected merchants, service inputs, prices, timing and ordering, merchant/payee choices, payment metadata, network/public-log data, OAuth/account/workspace/API/session identifiers, and deployment metadata. Under that fixed leakage, the transcript must not add a stable cross-merchant identifier. Honest-but-curious wallet privacy is outside the main model because issuance reveals the complete signed vector and seed coordinates to the wallet; blind issuance is an optional extension, not evaluated [P].

MinMandate does not claim quote fairness, service correctness, response truth, live-rail settlement, or protection against compromised agents/wallets, and it does not hide requests, prices, timing, or merchant identity [P].

## Architecture / protocol / taxonomy

Four phases:

1. **Mandate preparation.** An LLM planner produces a non-binding scope sketch from task, tool catalog, and user policy. A typed mandate `m` is compiled with service classes, merchant predicates, total budget, single-use slots, and expiry. The user signs `UAA = (m, sigma_U)`.
2. **Issuance.** The issuance wallet verifies signature, funding, mandate well-formedness, and the capacity invariant; reserves the budget; and issues a BBS credential over mandate fields plus hidden seeds `z = (s_N, s_F)`. The agent holds `(Cred, z)`.
3. **Adaptive execution.** The planner picks a merchant, obtains a quote, checks quote and slot against the mandate, derives the two views, and proceeds. If a merchant is unavailable or a quote is out of scope, it records a denial and tries another policy-compliant merchant within a bounded replan limit.
4. **Per-call payment.** The merchant verifies the service view and signs a per-call binding. The redemption wallet verifies both views, acknowledgement, freshness, and capacity, atomically consumes spend evidence, and returns a settlement authorization and receipt. The merchant releases only after receiving it.

The verifier visibility matrix is the key taxonomy. The merchant sees the raw request and service view, but not the spend view, credential body, or seeds. The redemption wallet sees both views and the acknowledgement, but not the raw request or credential body. The issuance wallet sees the credential body and seeds, but not the views. The merchant can therefore check service authority without learning the total budget or other scopes, while the wallet checks spendability without seeing the request.

## Section-by-section findings

- **Introduction/background.** Names the goal "payment-layer workflow privacy": verifier-local disclosure plus cross-call unlinkability under declared leakage. Contrasts AP2, x402, MPP, and Stripe Shared Payment Tokens; AP2 constraints remain verifier-visible and AP2 defines no per-verifier minimum-disclosure profile.
- **System/security model.** Soundness and privacy are separated. Soundness gives the adversary the agent and corrupted merchants; privacy gives external merchant/payment views but not agent or wallet state. This is narrower than anonymous payments.
- **Primitives.** BBS signatures, Pedersen commitments, Fiat-Shamir NIZKs, collision-resistant hashes, EUF-CMA signatures, and Type-1 policy tags for issuer hiding.
- **Workflow.** The views are independently verifiable but share a fresh anchor and binding. The merchant does not receive the spend proof, slots, serials, funding tags, or budget. The wallet does not receive the raw request.
- **Task-scoped authority.** A scope authorizes service class `gamma_l`, merchant predicate `phi_l`, and capacities `{(k, c_{l,k})}`. Changing class, predicate, budget, capacity, or expiry requires a new signature.
- **Unlinkable views.** Per-call randomness and one-time spend evidence remove the stable handle. The service digest, quote, approval digest, scope link, and view digests are bound into `Bind_i`.
- **Security analysis.** Soundness reduces to signature unforgeability, BBS proof-of-knowledge, auxiliary-relation proof-of-knowledge, issuer-hiding unforgeability, commitment binding, and hash collision resistance. Privacy is a hybrid over BBS/relation zero knowledge, commitment hiding, issuer unlinkability, and hash collisions, plus joint serial/tag pseudorandomness.
- **Implementation.** BLS12-381 and SHA-256, typed serialization, compressed encodings, rejection of identity/non-canonical/out-of-subgroup points. The AP2 adapter converts integer nanos to ISO-4217 USD minor units when exactly representable.
- **Evaluation.** Utility, privacy, overhead, and robustness use separate inputs and metrics; planner inputs, outputs, quotes, and availability are frozen across conditions.
- **Related work.** Positions against anonymous credentials, private payments/e-cash, capability systems, and agent/tool privacy; BBS alone does not specify the service/spend call binding.

## Key mechanisms and equations

Mandate and scopes:

`m = ({Scope_l}_{l=1..L}, B, expiry)`, `Scope_l = (gamma_l, phi_l, {(k, c_{l,k})}_{k in K_l})`. [P]

Capacity invariant: `sum_{l=1..L} sum_{k in K_l} c_{l,k} <= B`, with `c_{l,k} > 0`. [P]

Signed vector: `y = (H_can(UAA), {Scope_l}, B, expiry, s_N, s_F)`, issued as `Cred <- BBS.Issue(isk_iss, y)`. [P]

Call commitments:

`R_i <- Com_vec(d^svc_i, q_i, H_can(UAA), l_i; eta_i)`
`L_i <- Com_link(H_can(UAA), H_can(Scope_{l_i}); rho_i)`. [P]

Serial and funding tag per selected slot:

`N_{l_i,k} = s^N_{l_i} H_grp(Enccan(l_i,k))`
`TF_{l_i,k} = s^F_{l_i} H_grp(Enccan(l_i,k))`. [P]

Binding:

`d^S_i = H_can(Enccan(V^{S,0}_i))`
`d^P_i = H_can(Enccan(V^{P,0}_i))`
`Bind_i = H_bind(C_i, d^svc_i, d^S_i, d^P_i)`. [P]

Merchant acknowledgement: `ack_i <- Sign(sk_{mu_i}, Enccan(R_i, d^svc_i, Bind_i))`. [P]

Aggregate budget: `sum_{i in I_acc} a_i <= sum_i sum_{k in S_i} c_{l_i,k} <= sum_l sum_{k in K_l} c_{l,k} <= B`. [P]

BBS issuance: `C(y) = g_1 + sum_j h_j y_j`, `A = (isk_iss + e)^{-1} C(y)`, credential `(A,e)`. Issuer adaptation: `ipk*_i = alpha_i ipk_iss`, `eT*_i = alpha_i eT_{j,ep}`, `(A*_i,e*_i) = (alpha_i^{-1} A, alpha_i e)`. [P]

Theorem 2: `Adv_ext-priv <= n * epsilon_call + Adv_stpr_Hgrp + q_cross_can Adv_coll_Hcan + q_cross_bind Adv_coll_Hbind + epsilon_rand`, where `epsilon_call` covers BBS zero knowledge, relation zero knowledge, commitment hiding, issuer unlinkability, and hash collisions. [P]

## Experiments / evaluation

**Utility.** 96 pre-frozen AgentDojo tasks; planners Llama 3.1 8B (L8), Qwen 2.5 14B (Q14), Qwen 2.5 32B (Q32), GPT-OSS 120B. Seeds 11, 22, 33. `p_unavail` in {0, 0.1, 0.25, 0.5}. Baselines: AP2-k with top k merchants fixed at authorization (`k` in {1,2,3}), plus Native and Policy-only controls. At `p_unavail = 0.5`, task success AP2-1/AP2-2/AP2-3/MinMandate: L8 5.9/8.3/11.5/11.8; Q14 29.9/51.4/62.5/67.0; Q32 29.2/51.0/63.9/68.8; GPT-OSS 35.1/60.8/77.4/83.3. Average gain over AP2-1 at 50% unavailability is 32.7 pp. MinMandate matches Native in several cells; the full-enumeration AP2 anchor ties MinMandate in the merchant-scale experiment, identifying coverage as the mechanism. [P]

**Privacy.** Separate 96-task Q32 real-wire cohort, 118 merchant records, human review. Local vs joined observer views. AP2 Local recovery: 25.0 human, 14.3 Llama 3.3, 33.9 embedding, 33.9 NLI; AP2 joined: 53.1, 44.6, 50.0, 64.3; uplift +28.1, +30.3, +16.1, +30.4 pp. MinMandate Local: 24.1, 14.3, 33.9, 33.9. Stable Handle ablation: 50.0, 43.8, 43.8, 78.1, averaging +27.4 pp over MinMandate Local. [P]

**Overhead.** Pinned CPU, four-call workflow: AP2 31.5 ms vs MinMandate 629.3 ms; per call 6 vs 139 ms with one selected slot; setup 2 vs 74 ms. Wire: 35.9 vs 71.2 KiB for setup plus four calls; setup 3.2 vs 7.5 KiB; per call 8.2 vs 15.9 KiB. Paper summary: 0.63 s p50, ~100 KiB, excluding planning/execution/network/settlement. [P]

**Security.** Theorem 1 bounds authorization/spend soundness as a sum of seven reduction losses; Theorem 2 bounds unlinkability as above. Parser/state/cryptographic rejection checks are reported, but no independent implementation audit or production deployment. [P]

## Paper-specific findings

1. The central leak is not just "the merchant learns the price"; it is a reusable holder identifier joining separate paid calls across merchants, enabling intent reconstruction. [P]
2. Task-scoped authority is representation-level: a signed predicate over service classes and merchant attributes, not a longer merchant allowlist, enabling runtime substitution without a new user signature. [P]
3. Unlinkability is across calls, not within a call. The fresh per-call binding is deliberately retained to prevent splicing authorization evidence onto another request or quote. [P]
4. Role separation is explicit: merchant sees service authority but not budget/spend proof; wallet sees both views but not the raw request. [P]
5. Issuer hiding uses Type-1 policy tags plus per-call randomization of issuer key, policy tag, and BBS signature. [P]
6. One-time serials reject double spending; funding tags link a serial to a funded slot. Their privacy is argued jointly, preserving shared per-scope seed correlations until a final hybrid. [P]
7. The utility gain is a coverage gain, not a planning improvement. The AP2-1 gap at 50% outage is 5.9 pp (L8), 37.1 pp (Q14), 39.6 pp (Q32), and 48.2 pp (GPT-OSS). [P]
8. The Stable Handle ablation is the cleanest privacy result: adding one reusable public handle restores a large cross-merchant recovery advantage, isolating its contribution. [P]
9. Cryptographic cost is significant versus AP2 but small in absolute local terms, paid only on paid calls with no extra planner step or user roundtrip. [P]
10. The model assumes an honest user, wallet, setup, and policy/admission authority; a malicious wallet or compromised agent is outside the privacy claim. [P]

## Limitations and caveats

Privacy is conditional on declared leakage and an honest wallet/agent. It does not hide request content, prices, timing, merchant identity, OAuth/account identity, network metadata, or externally visible slot shape. The wallet can join issuance and redemption; blind issuance is optional and unevaluated. Policy and merchant-admission authorities are trusted setup. Admission attests only identity, key, service, class, maximum amount, and expiry, not honesty, quote fairness, or service quality. A malicious merchant can still overcharge within the cap, deliver bad data, or collude using declared metadata.

The evaluation is offline replay on 96 AgentDojo tasks with four planners, not a live marketplace. The privacy cohort is 96 Q32 tasks and 118 records, one lexicographically selected workflow per task. Human scoring is semantic; automatic judges use frozen prompts and thresholds (embedding cosine 0.8473754227161407; NLI margin -0.007030873151961714) from a 24-item synthetic development set. Those thresholds are not universal. The repository had 7 commits and 0 stars at read time, with a proprietary research snapshot rather than a broad open-source license; code availability is not auditability.

## Hackathon hooks

**What this enables.** A credible design for "one approval, many merchant choices" in agentic checkout: a service/spend split, per-call binding, double-spend rejection, and a reproducible privacy ablation. It fits marketplaces where an agent buys enrichment, shipping quotes, or API access from substitute providers.

**What it does NOT enable.** It is not a Zcash, Monad, or Solana implementation. It is a protocol plus a BLS12-381/Rust research prototype. It does not provide end-to-end anonymity, hide on-chain amounts, prevent prompt injection, guarantee merchant honesty, or provide production custody. It does not remove the wallet, issuer, admission authority, or live rail.

**Demo ideas.** (1) "Merchant outage shuffle": AP2-1 vs MinMandate at 50% outage with denial/replan trace. (2) "Join-the-dots": a mock observer links AP2 calls by the holder handle; the Stable Handle ablation re-enables it. (3) "Two-view checkout": merchant and wallet views side by side, neither seeing the full task. (4) "Budget burn": replay, cross-call splicing, and over-budget redemption, with atomic rejection. Mapping the spend view to a Zcash shielded note is new work, not in the paper.

## Claims to avoid

Do not claim anonymous payments, full metadata hiding, or trustlessness. Do not claim Zcash/Monad support, audit, production deployment, or independent replication. The 32.7-point gain is versus AP2-1 at 50% unavailability, not all AP2 variants. The 27.4-point result is the Stable Handle ablation, not an attack that MinMandate already prevents. Do not claim wallet privacy, malicious-agent privacy, quote fairness, service correctness, or live-rail settlement. The 0.63 s and 100 KiB figures exclude planning, execution, and settlement; do not call them end-to-end. Test keys are not production-ready.
