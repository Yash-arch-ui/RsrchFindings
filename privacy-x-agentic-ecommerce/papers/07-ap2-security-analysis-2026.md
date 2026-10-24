# Beyond the Mandate: A Systematic Security Analysis of the Agent Payments Protocol (AP2)

## Metadata
- Authors: Avital Aviv, Parth A. Gandhi (Ben-Gurion University of the Negev), Ron Bitton (Intuit), Asaf Shabtai (Ben-Gurion University of the Negev)
- Date: 24 August 2026 (v1)
- Venue/ID: arXiv:2608.23858v1 [cs.CR]; preprint
- Length: 18 pages, 26 references
- Type: systematic threat model, testbed, proof-of-concept attacks, and security scanner
- Read status: full text read

## One-paragraph summary
This paper analyzes AP2 v0.2, Google's mandate-based payment protocol for LLM shopping agents. AP2 secures signed Checkout and Payment Mandates implemented as SD-JWTs, but the authors argue that the context that shapes a mandate before signing, including catalog data, A2A messages, MCP tool results, and discovery artifacts, remains outside the signed object. The paper decomposes AP2 into five lifecycle phases and five deployment architectures, applies the MAESTRO framework to derive 48 threats across five attack families, scores them with AIVSS, and identifies eight High-risk threats. Because no complete public AP2 deployment existed, the authors build a testbed, implement five proof-of-concept attacks covering all eight High-risk threats, and build a deployment-aware scanner. The central finding is that pre-signing context manipulation can make a correctly signed mandate authorize an unintended transaction.

## Problem and threat model
AP2 has five roles: Shopping Agent (SA), Merchant (M), Merchant Agent (MA) when the merchant side is LLM-driven, Credential Provider (CP), Merchant Payment Processor (MPP), and Trusted Surface (TS). The TS renders mandate content to the user, obtains consent, and initiates signing. AP2 has Human-Present (HP) and Human-Not-Present (HNP) modes. In HP, the user approves closed mandates through the TS. In HNP, the user signs open mandates before leaving, and the SA later signs closed mandates with its own key (`agent_sk`) within the open mandate's constraints. The protocol's security goal is non-repudiable cryptographic proof of user authorization.

The threat model separates three baseline assumptions from eight security requirements. BA1: standard primitives are sound when used. BA2: implementations follow AP2's cryptographic and message-format requirements. BA3: the TS is not LLM-driven. SR1: faithful TS rendering and authorization capture. SR2: signer attribution, including HNP binding of `agent_sk` to a user-authorized open mandate. SR3: independent verifier checks. SR4: checkout and mandate-chain binding via `checkout_hash` and `transaction_id` over `checkout_jwt`. SR5: HNP constraint enforcement. SR6: key confidentiality and configuration integrity. SR7: replay prevention and single-use authorization. SR8: evidence retention and later verification.

Four threat actors are TA1 User, TA2 Merchant (including sub-merchants), TA3 Developer/Operator, and TA4 External Attacker. Eleven surfaces are S1 TS, S2 SA, S3 MA, S4 merchant backend, S5 CP, S6 MPP, S7 registry, S8 A2A, S9 MCP, S10 artifacts, and S11 knowledge. Capabilities are 14 access capabilities (ACK keys, ACM mandates/tokens, ACC channels, ACR runtime) plus four knowledge capabilities (AK1-AK4). Six goals are AG1 unauthorized payment, AG2 mandate-content manipulation, AG3 scope inflation, AG4 confidentiality breach, AG5 repudiation, and AG6 denial of authorized payment.

The deployment architectures are A1 single-agent SA and single-agent MA; A2 the same with isolated MCP servers on each side; A3 multi-agent SA and multi-agent MA with specialized sub-agents; A4 marketplace, where one marketplace identity fronts multiple sub-merchants; and A5 shared MCP, where SA and MA use the same MCP server.

## Architecture / protocol / taxonomy
AP2's phases are P1 pre-mandate preparation (identities, keys, credentials, AgentCards, endpoints, trust anchors), P2 mandate context formation (cart, payment options, merchant-signed `checkout_jwt`), P3 mandate authorization (P3-U user signing in HP or open-mandate signing in HNP; P3-A SA signing of HNP closed mandates), P4 mandate verification and consumption (signatures, chain, constraints, checkout binding, expiration, prior consumption, then credential issuance, settlement, receipts), and P5 post-transaction retention. HP is sequential; in HNP, P3-U precedes P2.

Mandate binding: the closed Checkout Mandate carries the digest of `checkout_jwt` in `checkout_hash`, and the closed Payment Mandate carries the same digest in `transaction_id`. In HNP, each closed mandate also commits to its parent open mandate. Receipts are bound to their closed mandates. This is why applying a Payment Mandate to a different checkout fails verification.

The 48 threats are grouped by corrupted security object. F1 Semantic Manipulation (10, T-1 to T-9 and T-48) corrupts conversation context via context poisoning, tool-path abuse, and mandate-content divergence. F2 Authority Spoofing (9, T-10 to T-17 and T-45) corrupts signing authority via signer-key control and authority binding. F3 Supply-Chain and Trust-Root Subversion (13, T-18 to T-30) corrupts trust roots via software provenance, discovery trust, and channel identity. F4 State-Binding Failures (9, T-31 to T-38 and T-46) corrupts transaction state via execution-state and historical binding. F5 Accountability Failures (7, T-39 to T-44 and T-47) corrupts accountability records via dispute-evidence gaps, marketplace fronting, and multi-tenant isolation.

The eight High-risk threats are T-1 pre-signing context poisoning (AIVSS 7.0), T-4 tool argument manipulation (7.4), T-15 caller role confusion (7.6), T-23 sub-agent prompt drift (7.6), T-27 A2A extension downgrade (7.0), T-29 cross-server tool confusion (7.2), T-34 `risk_data` poisoning (7.4), and T-42 multi-tenant isolation failure (7.1).

## Section-by-section findings
- Section 1 states the gap: AP2 protects signed mandates, not the pre-signing context.
- Section 2 reviews AP2 v0.1 analyses and MCP/A2A security work, noting that no prior study spans AP2 v0.2 phases, trust boundaries, and architectures.
- Section 3 defines roles, mandates, receipts, HP/HNP flows, five phases, BAs, SRs, and five architectures.
- Section 4 defines the MAESTRO threat model, actors, surfaces, capabilities, goals, and AIVSS scoring.
- Section 5 organizes the 48 threats into five families; Section 6 gives AM1-AM5; Section 7 describes the scanner.
- Section 8 evaluates coverage, reproducibility, and scanner ablation; Section 9 discusses pre-signing context binding and protocol asymmetry; Section 10 concludes.

## Key mechanisms and equations
The AIVSS score is
`AARS = sum_{i=1}^{10} f_i`, with `f_i` in {0, 0.5, 1},
and
`AIVSS = round(((CVSS_base + AARS) / 2) * ThM * MF, 1)`.
The paper uses `ThM = 0.97` for PoC maturity and `MF = 1.0` for inherent risk before deployed mitigations, giving
`AIVSS = round(((CVSS_base + AARS) / 2) * 0.97 * 1.0, 1)`.
AARS sums ten 0/0.5/1 factors: autonomy, tool use, memory, dynamic identity, multi-agent interaction, non-determinism, self-modification, planning, context, and opacity. Severity bands are None 0.0, Low 0.1-3.9, Medium 4.0-6.9, High 7.0-8.9, Critical 9.0-10.0.

Worked example: T-1 has CVSS vector `CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:P/VC:N/VI:H/VA:N/SC:N/SI:H/SA:N`, giving `CVSS_base = 8.3`; its AARS tuple is `(0.5, 0.5, 0.5, 0.5, 0.5, 1, 0, 1, 1, 1)`, so `AARS = 6.5`; `AIVSS = round(((8.3 + 6.5) / 2) * 0.97, 1) = 7.2`, High.

The five demonstrations are: AM1 authorization beyond stated intent (T-23 -> T-1 -> T-4), where a drifted consent sub-agent accepts an $80 ceiling and no payee restriction for a $50 camera; AM2 role confusion at a shared tool layer (T-29 -> T-15), where name-colliding `checkout.quote_v2` leaks transaction context and lets a merchant peer call CP tools; AM3 cross-tenant credential theft (T-42), where a shared CP store keyed only by token and transaction reference returns a rival's token; AM4 AP2 extension URI downgrade (T-27) via `registry.ap2-protoco1.org`; and AM5 unsigned `risk_data` poisoning (T-34), where a false low-risk score and step-up-completed flag bypass OTP/3DS2.

The scanner has passive static checks, the Cross-Role Differential Specification Evaluator (CDSE) comparing schemas and mandate fields across SA, MA, CP, and MPP, and an adversarial layer running fixed probes against a test instance. It profiles the deployment, filters the threat matrix, runs applicable checks, and reports evidence mapped to threat ID, family, phase, role, and surface.

## Experiments / evaluation
There was no complete public AP2 deployment, so the authors built a testbed covering all five architectures and used it for the PoCs and scanner evaluation. RQ1 compares the catalog against five frozen STRIDE-GPT v0.18.0 runs (gpt-5.3 generator, one run per architecture, 124 rows: 24 each for A1, A2, A3, A5 and 28 for A4). An LLM judge (gpt-5.5, temperature 0, fixed seed) compared each of the 48 catalog threats; 32 were Full or Partial (66.7%) and 16 were None. Manual review changed one Full to Partial; all 124 STRIDE-GPT rows mapped to at least one catalog item, so none was a candidate addition. Two third-party annotators labeled the 16 None items: 15/16 agreement (93.75%), final labels 16 Distinct, zero Overlap, zero Unsupported.

RQ2 tests AIVSS reproducibility with eight independent raters from academia and industry on 25 threats (five per family, including a common set T-1, T-15, T-21, T-34, T-42). Agreement on derived severity bands was Gwet's AC2 (quadratic) 0.984 [0.963, 1.000], Krippendorff's alpha (quadratic) 0.802 [0.520, 0.978], exact pairwise agreement 0.923 [0.846, 0.980], and 111/120 ratings (92.5%) reproduced the single-rater reference band.

RQ3 ablates the scanner's three layers over seven configurations (Full, three leave-one-out, three isolated) with five runs per architecture. Evaluated threats were five for A2, five for A3, two for A4, and six for A5. Full recall was A2 0.80, A3 0.60, A4 0.50, A5 1.00. No-Passive: A2 0.36 +/- 0.08, A3 0.28 +/- 0.10, A4 0.50, A5 0.93 +/- 0.08. No-CDSE: A2 0.80, A3 0.40, A4 0.00, A5 0.83. No-Adv: A2 0.60, A3 0.60, A4 0.50, A5 1.00. Only-Passive: A2 0.60, A3 0.40, A4 0.00, A5 0.83. Only-CDSE: A2 0.00, A3 0.20, A4 0.50, A5 0.87 +/- 0.16. Only-Adversarial: A2 0.36 +/- 0.08, A3 0.08 +/- 0.10, A4 0.00, A5 0.17. Passive uniquely detected T-4, T-8, and T-24; CDSE uniquely detected T-14, T-1 on A4, and T-29; adversarial uniquely detected T-5. All three layers missed T-2 on A2, T-1 and T-9 on A3, and T-3 on A4.

## Paper-specific findings
1. Valid mandate signatures do not imply user intent. The pre-signing path, including discovery, cart state, tool outputs, and verifier-facing disclosures, is an attack surface that AP2's signatures do not cover.
2. The attack surface is architecture-dependent. Shared MCP (A5) amplifies the largest number of F1 semantic threats, while A1 is dominated by F3 trust-root and F4 state-binding threats.
3. AP2's dependency on A2A and MCP is asymmetric: AP2 can validate the final mandate but cannot retroactively secure the flawed exchange that produced it, so some mitigations must live in the supporting protocols.
4. HNP is especially exposed to pre-signing manipulation because the SA signs closed mandates autonomously against open-mandate constraints; if the context or constraint mapping is manipulated, the closed mandate can be valid but over-broad.
5. The eight High-risk threats span all five families, not just prompt injection: semantic manipulation, authority spoofing, supply-chain, state binding, and accountability all produce High-risk findings.
6. The scanner's layers are complementary: each isolated layer uniquely detected at least one seeded threat, so no single validation method covers the space.
7. STRIDE-GPT elicited 66.7% of the catalog, which the authors use to argue that agent-specific MAESTRO analysis adds coverage beyond component/data-flow threat modeling.
8. AIVSS severity bands reproduced well under the rubric, but the authors caution that this measures rubric reproducibility, not ground-truth risk.

## Limitations and caveats
Its limitations: no complete public AP2 deployment existed, so all PoCs and scanner results come from a custom testbed; the PoCs do not measure effects on card networks, issuers, acquirers, or other payment rails; STRIDE-GPT was run once per architecture; the comparison shares lifecycle and security-property definitions with the catalog, which may inflate agreement; and the reproducibility study covers 25 of 48 threats. The scanner evaluation uses small threat sets per architecture (two to six) and an LLM-dependent adversarial layer (gpt-5.5).

Additional caveats: the 48 threats are a catalog, not 48 demonstrated exploits; only five demonstrations cover the eight High-risk threats. AIVSS scores are expert judgments, not measurements, and a threat may be Low in other architectures. The testbed's fidelity and the scanner's LLM-dependent layers may not generalize. No cost-benefit analysis is provided, and its recommendations (signed `risk_data`, pinned tool names, per-tenant keys) are not AP2 mandates.

## Hackathon hooks
What it enables: a precise checklist for attacking or hardening an AP2-style agent checkout. The pre-signing context binding idea is directly implementable: hash the discovery result, cart, tool outputs, and rendered mandate fields into a context commitment that the signed mandate covers.

What it does not enable: it does not provide a production AP2 implementation, a complete exploit suite, or a privacy design. It does not evaluate Zcash or shielded settlement, and it does not solve the metadata leakage of mandate chains. It does not prove that the proposed mitigations eliminate the threats; they are targeted fixes.

Demo ideas: (1) a testbed AP2 checkout with a malicious catalog that changes the payee, plus a context-commitment check that blocks it; (2) an HNP flow where a drifted sub-agent broadens the mandate, with a deterministic policy outside the model enforcing the user's limit; (3) a shared-MCP tool-name collision demo and a pinned-server fix; (4) an unsigned `risk_data` bypass of step-up, then a signed-attestation fix.

## Claims to avoid
Do not claim AP2 is broken or that all 48 threats are exploitable; the paper demonstrates five chains covering eight High-risk threats on a testbed. Do not claim the High-risk scores are empirical measurements; they are AIVSS judgments. Do not claim the scanner is production-ready or that its recall generalizes beyond the testbed. Do not claim AP2 mandates are useless; they do bind checkout and payment and prevent some replay, but they do not cover pre-signing context. Do not claim the paper tested a real Google deployment or card-network effects. Do not claim the mitigations are proven complete.
