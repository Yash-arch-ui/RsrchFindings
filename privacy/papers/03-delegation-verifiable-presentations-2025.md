# On Delegation of Verifiable Presentations from mdoc and BBS Credentials

## Metadata

- **Authors:** Andrea Flamini (Hasso Plattner Institute in the ePrint version; the workshop version lists University of Trento and Politecnico di Torino), Andrea Gangemi (Politecnico di Torino), Enrico Guglielmino (Politecnico di Torino), Vincenzo Orabona (Eustema S.p.A.).
- **Date:** ePrint received and approved 2025-12-22; workshop version presented at TDI 2025 on 2025-02-03 in Bologna.
- **Venue / ID:** Cryptology ePrint Archive, Paper 2025/2306; preprint. Earlier version: CEUR Workshop Proceedings Vol-3968, pp. 26-38, TDI 2025.
- **Length:** ePrint length not verified; CEUR version: 13 pages.
- **Type:** Formalization, two constructions, security analysis. The ePrint adds BBS anonymous credentials and unlinkability.
- **Read status:** ePrint PDF blocked. `[L]` = local abstract/metadata. `[W]` = full CEUR workshop version. `[X]` = ePrint PDF excerpt. `[B]` = background. `[I]` = inference.

## One-paragraph summary

This paper formalizes **delegation of verifiable presentations**: a credential holder (delegator) authorizes another party (delegatee) to present a credential on their behalf for a specific operation. It defines four algorithms — delegation issuance, delegation verification, delegated presentation, and delegated presentation verification — and defines correctness, unforgeability, and, for anonymous credentials, unlinkability `[L]`. A delegation binds a scope (validity, verifier, operation), a delegatee identity statement, and a delegator payload into a proof that the delegator holds a credential satisfying that payload. The delegatee proves, using its own credential, that it satisfies the delegatee identity statement, and binds that proof to the delegation and a verifier nonce `[W]`. Two instantiations are given: mdoc/ARF-style credentials using hash commitments and signatures, and BBS anonymous credentials adding unlinkability `[L][X]`.

## Problem and threat model

eIDAS 2.0 requires EU digital identity wallets that store credentials and support privacy-preserving presentation. The EUDI Wallet Reference Implementation Roadmap includes delegation of attestation `[L][W]`. This paper addresses delegation of *presentation*, not issuance: a holder can let a family member pick up a prescription, an adult child represent an elderly parent, or an intermediary perform a financial operation `[W]`.

The delegation must specify **scope** (operation, verifier, validity), **delegatee identity** (a statement the delegatee's credential must satisfy), **delegator payload** (the information the verifier requires), and a **proof** that the delegator authorized it and satisfies the payload `[W]`.

Adversaries include a delegatee presenting beyond scope, a user forging a delegation from a credential it does not hold, a delegatee using a delegation not issued to its identity, and colluding verifiers trying to link presentations. The experiment gives the adversary the issuer key and public parameters and lets it corrupt users, obtain delegations, and obtain delegated presentations before forging `[W]`.

The two unforgeability notions are `[W]`: (1) **delegation issuance** — cannot forge a delegation from a credential one lacks; (2) **delegated presentation** — cannot present a delegation not issued to one's identity. For anonymous credentials the paper adds **unlinkability of the delegated presentation**: beyond disclosed attributes and the issuer, an adversary cannot tell whether presentations came from the same delegator/delegatee pair or different ones `[X]`.

Trust assumptions are significant. The issuer is trusted to attest honestly. The verifier enforces scope and checks the nonce. EBSI relies on a Trusted Issuer Registry (TIR), where only legal persons may issue credentials, complicating physical-person delegators `[W]`. mdoc stores the credential secret in an HSM or TEE for device binding `[W]`. Unique session identifiers and nonces are assumed; detailed session management is left to deployment, with Tamarin/ProVerif suggested `[W]`. Delegation revocation is not addressed.

## Architecture / protocol

The four algorithms are `[W]`:

```text
DelegIssuance(cred_D, DP, scope, delegatee_ID) -> del
DelegVer(del) -> {0,1}
DelegPres(del, cred_delta, nonce) -> pres
DelegPresVer(pres) -> {0,1}
```

The delegation is:

```text
del = (delegatee_ID, scope, DP, pi_DP)
```

where `pi_DP` proves the delegator knows a credential `cred_D` satisfying `DP`, bound to `scope` and `delegatee_ID` `[W]`.

The flow `[W]`:

1. Delegatee sends `delegatee_ID` to the delegator.
2. Delegator runs `DelegIssuance` and sends `del`.
3. Verifier sends a fresh nonce.
4. Delegatee runs `DelegPres(del, cred_delta, nonce)` and sends `pres = (del, pi_del)`.
5. Verifier checks `DelegVer(del)`, that `pi_del` proves the delegatee's credential satisfies `delegatee_ID`, and that scope is satisfied.

**mdoc / ARF instantiation `[W]`.** ARF credentials use commitments `com_i = H(a_i || salt_i)`. The issuer generates `(sk_cred, pk_cred)` and signs `({com_i}, pk_cred)`; a presentation reveals chosen attributes/salts, includes the issuer signature, and signs the transcript with `sk_cred`. The verifier checks both signatures and recomputes revealed commitments. Device binding comes from an HSM/TEE `[W]`. For delegation, `pi_DP` is bound to `DP`, `scope`, and `delegatee_ID` `[W]`:

```text
pres' = ((sigma, {com_i}, pk_cred_D), {salt_i}_{DP}, DP, scope, delegatee_ID)
sigma' = Sign(pres', sk_cred_D)
pi_DP = (pres', sigma')
```

The delegated presentation is `[W]`:

```text
pres'' = ((sigma, {com_i}, pk_cred_delta), {salt_i}_{delegatee_ID}, del, nonce)
sigma'' = Sign(pres'', sk_cred_delta)
pi_del = (pres'', sigma'')
```

The verifier checks `DelegVer(del)`, verifies `sigma''` under `pk_cred_delta`, recomputes commitments for the delegatee's revealed attributes, verifies the issuer signature on the delegatee's credential, and checks scope `[W]`.

**BBS instantiation `[X]`.** The ePrint defines a BBS AC scheme and a BBS VP delegation scheme (Definitions 9, 10, 16). The structure parallels mdoc, but proofs are BBS proofs of knowledge hiding the credential and disclosing only attributes for `delegatee_ID`. Indexed text mentions commitments reducing communication cost and a verifier reconstructing `U_D` from `A_D`, `B_D`, `c_1`, and `z_1`, suggesting a commitment to the delegator presentation with a consistency proof. Exact equations are unverified. Unlinkability is proved for BBS because the presentation hides the credential and leaks only the issuer key and disclosed attributes `[X]`.

**EBSI / EUDI integration `[W]`.** The delegation can be a VC issued by the delegator with attributes `scope`, `delegatee_ID`, `DP`, and `pi_DP`. The delegatee presents it in full and creates a VP from its own credential for `delegatee_ID`; the verifier checks both. No new data structures are needed. In EBSI, only TIR-registered legal persons may issue credentials: a legal-person delegatee can sign the delegation VC for compatibility, while two physical persons need a TIR-registered third party. The delegation VC is fully disclosed and needs no device-binding key pair `[W]`.

## Section-by-section findings

1. **Introduction `[W]`.** Explains eIDAS 2.0, EUDI, and the difference between delegating issuance and presentation; use cases are prescription pickup, representation, and financial operations.
2. **Verifiable credential schemes `[W]`.** Defines issuer setup, issuance, presentation, and verification for ARF-compliant credentials using hash commitments and a UF-CMA signature scheme (mdoc, SD-JWT, W3C VCDM).
3. **VP delegation scheme `[W]`.** Defines the four algorithms and the fields `scope`, `delegatee_ID`, `DP`, `pi_DP`.
4. **Security `[W]`.** Defines correctness and unforgeability; Experiment 1 models issuing, delegation, and presentation oracles. The proof uses v-UF-CMA, the random oracle model, and binding commitments, with session-proportional loss.
5. **ePrint additions `[X]`.** Section 2.5 covers commitments; Definitions 9 and 10 define BBS AC and VP delegation; Definitions 11-14 define correctness, unforgeability, and unlinkability; Section 5 gives Definitions 15 and 16.

## Key mechanisms and equations

**Delegation issuance `[W]`.**

```text
pres' = ((sigma, {com_i}, pk_cred_D), {salt_i}_{DP}, DP, scope, delegatee_ID)
sigma' = Sign(pres', sk_cred_D)
del = (delegatee_ID, scope, DP, (pres', sigma'))
```

**Delegated presentation `[W]`.**

```text
pres'' = ((sigma, {com_i}, pk_cred_delta), {salt_i}_{delegatee_ID}, del, nonce)
sigma'' = Sign(pres'', sk_cred_delta)
pres = (del, (pres'', sigma''))
```

**BBS background `[B]`.** In bilinear groups `G1`, `G2`, `GT` with pairing `e`, a standard BBS verification equation for signature `(A, e)` on messages `m_i`, secret `x`, public `X = g2^x`, is:

```text
e(A, X * g2^e) = e(g1 * product_i h_i^{m_i}, g2)
```

The holder proves knowledge of `(A, e, {m_i}_{hidden})` and reveals chosen messages. Re-randomization gives unlinkability. The paper's exact BBS equations and commitment layout are unverified.

**v-UF-CMA `[W]`.** A multi-session UF-CMA variant: the reduction opens several public keys under the same parameters and wins by forging one; security reduces to UF-CMA with session-proportional loss. For BBS, unlinkability follows because the proof hides the credential and leaks only the issuer key and disclosed attributes `[X]`.

## Experiments / evaluation

None verified. The workshop version has no implementation, benchmarks, or performance tables. Do not cite proof sizes, proving times, verification times, or gas costs.

## Paper-specific findings

1. Introduces and formalizes verifiable presentation delegation `[L][W]`.
2. Defines four algorithms: issuance, delegation verification, delegated presentation, presentation verification `[W]`.
3. Defines correctness, unforgeability, and, for ACs, unlinkability `[L][W]`.
4. Separates forging a delegation from presenting one not issued to one's identity `[W]`.
5. Gives an mdoc/ARF instantiation using hash commitments and signatures `[W]`.
6. Gives a BBS instantiation adding unlinkability `[L][X]`.
7. Binds scope, delegatee identity, and delegator payload into one proof `[W]`.
8. Encodes the delegation as a VC; only verification procedures change `[W]`.
9. In EBSI, physical-person delegators may need a TIR-registered third party to sign `[W]`.
10. mdoc device binding uses an HSM/TEE; the delegation VC needs no key pair `[W]`.
11. BBS unforgeability is simpler than mdoc; unlinkability is proved for BBS `[X]`.

## Limitations and caveats

**Access.** The ePrint was not read in full; the workshop version predates BBS and unlinkability. BBS details come from search excerpts and may omit definitions, equations, and caveats.

**No evaluation.** No verified implementation or benchmark; deployment claims are architectural.

**Random oracle and non-standard assumption.** The mdoc proof uses the random oracle model and v-UF-CMA with multiple sessions; it is not standard-model, and the reduction loss matters.

**Linkability of mdoc.** mdoc is not unlinkable; hash commitments and the credential public key can be correlated. Unlinkability is claimed only for BBS.

**Explicit, not anonymous, delegation.** The verifier sees the delegation; delegatee identity and delegator payload may reveal who is involved. It is not a Chase-Lysyanskaya-style delegatable anonymous credential and does not hide a chain.

**No delegation revocation.** No early revocation mechanism is specified. The verifier can enforce expiry and scope, but there is no status mechanism.

**Scope enforcement and schemas.** Scope must be encoded with a dictionary/schema, and detailed analysis is left to future work `[W]`; cryptography does not enforce semantics.

**Sessions and replay.** Unique session identifiers and nonces are assumed. Replay, nonce reuse, and transport binding are deployment responsibilities.

**TIR and hardware trust.** EBSI restricts issuance to legal persons, forcing third-party signatures and revealing delegation content to that party. mdoc device binding depends on an HSM/TEE; a compromised device key breaks non-transferability.

**Cryptography.** BBS is pairing-based and not post-quantum; mdoc privacy is weaker by design.

## Hackathon hooks

**Enables.** Scoped delegation for AI agents: a human delegates a credential to an agent for one verifier, operation, and expiry; the agent proves the delegatee policy and presents the human's payload without holding the full credential. On-chain delegated access on Monad: a contract verifies delegation, delegatee proof, scope, and nonce. Zcash-gated delegated payments and privacy-preserving representation.

**Does not enable.** Revocation before expiry; anonymous delegation chains; hiding the delegator; unlinkable mdoc; semantic scope enforcement without a policy engine; protection if the verifier ignores scope; post-quantum security; a safely forwardable bearer delegation.

**Demo ideas.**

1. **Scoped agent pass.** A human BBS credential delegates to an AI agent with scope "one Monad contract, one operation, one hour" and delegatee identity "registered and over 18." The contract checks delegation, agent credential, nonce, and scope. Add a Zcash testnet payout for private payment with scoped authorization.
2. **Prescription pickup.** Use mdoc: a parent delegates to a child the right to pick up a prescription at a specific pharmacy; the child's credential satisfies the delegatee statement and the parent's payload is bound to pharmacy and operation.
3. **Scope escalation failure.** Reuse the delegation at another verifier or after expiry; show rejection because scope and nonce are bound into `pi_DP` and `pi_del`.
4. **Linkability comparison.** Present the same delegation twice with mdoc and BBS; mdoc correlates, BBS does not.

## Claims to avoid

- "Delegations are revocable." No revocation mechanism was verified.
- "Supports delegation chains." The verified construction is a single delegator-to-delegatee delegation.
- "mdoc presentations are unlinkable." They are not.
- "Proof is in the standard model." The mdoc proof uses the random oracle model and v-UF-CMA.
- "Scope is enforced by cryptography alone." The verifier must parse and enforce semantics.
- "Post-quantum." BBS and the signature-based mdoc construction are not.
- Any specific proof size, latency, gas cost, or benchmark number.
