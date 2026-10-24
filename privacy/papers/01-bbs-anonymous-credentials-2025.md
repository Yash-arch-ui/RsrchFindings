# A Specification of an Anonymous Credential System Using BBS+ Signatures with Privacy-Preserving Revocation and Device Binding

## Metadata

- **Authors:** Christoph Graebnitz, Nicolas Buchmann, Martin Seiffert, Marian Margraf (Secure Systems Engineering, Fraunhofer AISEC, Garching).
- **Date:** ePrint received and approved 2025-05-09; indexed PDF dated 2025-05-02.
- **Venue / ID:** Cryptology ePrint Archive, Paper 2025/824; category "Cryptographic protocols"; preprint, CC BY.
- **Length:** 30 pages (main text pp. 1-24, references pp. 26-30).
- **Type:** Specification / system design for an anonymous-credential (AC) system. It gives algorithms, protocol flows, and a security discussion, not a formal-proof paper and not an implementation benchmark.
- **Read status:** full PDF read from an Internet Archive copy of the 2025-05-02 ePrint revision. The live ePrint PDF is Cloudflare-blocked in this environment. The paper is referred to as `Pi_bbs+`.

## One-paragraph summary

This paper specifies `Pi_bbs+`, an anonymous-credential system combining BBS+ signatures, DAA+-style hardware binding, and an LVVC-style status credential. The stated goals are minimality of information, unlinkability, non-duplication, and privacy-preserving revocation. The construction is based on DAA+ by Camenisch et al.: it strips the DAA+ revocation list, adds an LVVC issuance protocol, renames and bundles algorithms for the AC setting, and generalizes beyond TPMs to any carrier medium (CM) with similar security properties. Part of the credential is bound to a device secret in secure hardware; an LVVC reflects revocation status. The holder creates two verifiable presentations, one for the AC and one for the LVVC, and intentionally links them within a session using a shared pseudonym. The result is a deployable design blueprint, not a verified proof-and-benchmark paper.

## Problem and threat model

Standard AC presentations already provide selective disclosure and, with BBS+, unlinkable multi-show. The paper argues that specifications protect privacy during presentation but neglect privacy for non-duplication and revocation.

Threats implied by the design:

- **Curious or colluding verifiers.** They should not learn hidden attributes or link two ordinary presentations. Collusion with an issuer should not reconstruct a tracking identifier.
- **A holder who copies a credential.** BBS+ is bearer-like without a hardware anchor. Non-duplication needs a non-exportable device secret in a CM and a fresh hardware contribution per presentation.
- **Revocation leakage.** Naive checks reveal which credential is used or force per-presentation issuer contact. A status credential avoids both, at the cost of intentional AC-LVVC linking.
- **Replay/context confusion.** Presentations use a basename and session identifier; the holder must not reuse a basename across sessions.

The paper's security discussion explicitly assumes: the issuer is trustworthy; the issuer checks attributes according to the use case; the ideal functionalities `F_crs`, `F_ca`, `F_l_smt`, and `F_auth*` are trustworthy; and the CM manufacturer is trustworthy. It also states that the core/helper design cannot protect against malware on the helper: because `Pi_bbs+` does not specify encryption of attribute data, a malicious helper can learn attributes without interacting with the core.

## Architecture / protocol

Roles are issuer, holder/host device, carrier medium (CM), verifier, and verifiable data registry. The CM holds the device secret `sk` and public device key `Q = h^sk`; the issuer binds the credential to `Q`. The design uses ideal functionalities for the common reference string, certificate authority, secure message transmission, and authenticated channels.

The high-level flows are:

1. **`IssuerSetup`.** Generates BBS+ key material `(x, pk)`, sets the session identifier, registers `(sid, pk)` with the certificate authority, and initializes the issuer's set of CM identifiers.
2. **`Issuing`.** The issuer inspects the requested attributes, issues the AC bound to the device key `Q`, and stores a `JoinRecord`. The `JoinRecord` records the issuance, lets the issuer decide validity, and contains the authentication key `k_auth` used for LVVC issuance.
3. **`LVVCIssuing`.** A subprotocol that issues an LVVC containing revocation information for the AC. It authenticates the host device with the ISO/IEC 9798-2:2019 unilateral two-pass mechanism, using authenticated encryption such as AES-GCM. The issuer deletes the `JoinRecord` to revoke the AC.
4. **`Presentation`.** The holder consents to disclose a set of AC and LVVC attributes. The host device creates two SPK-based verifiable presentations, links them, and the verifier checks both, the link, and the revocation information via `CheckRevocationStatus`.

The LVVC is itself a BBS+ credential carrying only revocation information. A concrete revocation-information choice is the LVVC issuance date or a fixed time window (for example, one hour, day, or week). A verifier can accept only LVVCs within its freshness policy; an AC is effectively revoked if the holder cannot obtain a fresh LVVC. The time window must be shared across holders so that it does not become a unique identifier.

## Section-by-section findings

1. **Introduction.** Motivates ACs for data minimization and EU digital identity, then identifies the gap: presentation privacy is studied, non-duplication and revocation privacy are not. States the four requirements.
2. **1.1 Contribution.** Specifies the first AC system using BBS+ that satisfies minimality of information, unlinkability, non-duplication, and privacy-preserving revocation. It is based on DAA+ and uses LVVCs for revocation.
3. **1.2 Organization.** Background and comparison, then results (signature scheme, SPKs, protocol flows, security discussion), then discussion.
4. **2. Materials and Methods.** Reviews BBS+, DAA+, revocation, and structured comparison.
5. **2.1 Minimality of Information and Unlinkability.** Explains selective disclosure, BBS+ re-randomization, and the need to avoid unique attribute combinations.
6. **2.2 Non-duplication.** Explains hardware binding and why a bearer credential can be copied without a CM.
7. **2.3 Privacy-preserving Revocation.** Reviews revocation lists, accumulators, and LVVCs; Section 2.3.1 defines LVVCs as status credentials carrying revocation information.
8. **2.4 Structured Comparison.** Table 1 compares Hanzlik-Slamanig, Hesse et al., Bichsel et al., Batina et al., U-Prove, Idemix, and `Pi_bbs+` on minimality, unlinkability, non-duplication, revocation, and privacy-preserving revocation. The paper argues no prior BBS+ system satisfies all four target requirements.
9. **3. Results.** Specifies `Pi_bbs+`.
10. **3.1 Signature Scheme.** Figure 5 specifies the BBS+ scheme used in `Pi_bbs+`, including the device-binding key `Q = h^sk`.
11. **3.2 Signature Proofs of Knowledge.** Defines `GenSPKQ`, `GenSPKSig`, `VerifyLink`, `VerifySPKQ`, and `VerifySPKSig`.
12. **3.3 Protocol Flows.** Figures 9-12 specify `IssuerSetup`, `Issuing`, `LVVCIssuing`, and `Presentation`.
13. **3.4 Security Discussion.** Discusses the four requirements under the trust assumptions above and states the core/helper malware limitation.
14. **4. Discussion.** Argues that `Pi_bbs+` is the first BBS+-based specification meeting all four requirements, and that implementation with an SE or TPM is plausible because the SPKs follow Hesse et al. and Camenisch et al.
15. **Declarations, Abbreviations, References.** No evaluation section.

## Key mechanisms and equations

**BBS+ background.** In bilinear groups `G1`, `G2`, `GT` with pairing `e`, a standard BBS verification equation for signature `(A, e)` on messages `m_i`, secret `x`, public `X = g2^x`, is:

```text
e(A, X * g2^e) = e(g1 * product_i h_i^{m_i}, g2)
```

The holder proves knowledge of `(A, e, {m_i}_{hidden})` satisfying this equation while revealing chosen messages. Re-randomizing `A` makes proofs unlinkable. This is background; Figure 5 is the paper's exact BBS+ instantiation.

**Device binding `GenSPKQ`.** The CM generates and stores `sk` and returns `Q = h^sk` together with a proof

```text
pi_Q in (Z_p^*)^2 = SPK{ (sk) : Q = h^sk }(m)
```

The authenticated channel `F_auth*` proves that `(Q, pi_Q)` originated from a CM. This is what makes the credential bound to the device.

**Credential possession `GenSPKSig`.** For a set of undisclosed attributes `attrs_barD`, the CM and host return

```text
pi_sigma = (A', A_bar, d, nym, pi)
         in G1^4 x ((Z_p^*)^7 x P(Z_p^*))
```

an SPK for the relation

```text
SPK{ (sk, attrs_barD, e, r2, r3, s') :
      A_bar / d = A'^{-e} * h0^{r2}
      and g1 * product_{i in D} h_i^{a_i}
          = d^{r3} * h0^{-s'} * h^{-sk} * product_{j in barD} h_j^{-a_j}
      and nym = H1(bsn)^sk }(m)
```

where `H1` maps a basename to a `G1` generator. `GenSPKSig` proves possession of a valid BBS+ signature and therefore of the AC.

**AC-LVVC link `VerifyLink`.** The link is established by reusing the same basename `bsn` and device secret `sk`, so `nym = H1(bsn)^sk` is equal in both proofs. `VerifyLink` checks that the two SPKs use the same pseudonym. This is the deliberate linkability exception: the AC and its status credential are linkable within a presentation session, while ordinary AC presentations remain unlinkable.

**Revocation.** The issuer maintains a `JoinRecord` for each valid AC. Deleting it revokes the AC. `LVVCIssuing` authenticates the host device and issues a fresh LVVC with current revocation information. If the holder cannot obtain a fresh LVVC, the AC is revoked in practice. The verifier checks the LVVC's revocation information against its policy.

**Non-duplication.** Copying the AC or LVVC requires copying the CM-held device secret and the ability to produce fresh `GenSPKQ`/`GenSPKSig` proofs. A copied credential without the CM fails the device-binding proof.

**Unlinkability.** BBS+ SPKs hide the credential and disclosed attributes. The AC-LVVC link is intentional and consented. To avoid cross-session linkage, the basename `bsn_v` must be unique per presentation session; reusing it links sessions. Disclosed attributes must not form a unique identifier or a unique combination.

## Experiments / evaluation

There is no implementation, benchmark, latency, throughput, or proof-size measurement. Table 1 is a qualitative comparison of AC specifications; it is not an experimental evaluation. The paper explicitly says that technical implementation details are beyond its scope. Claims about SE/TPM feasibility are architectural, based on prior work showing BBS+ can be implemented with an SE or TPM.

## Paper-specific findings

1. `Pi_bbs+` is a complete AC specification, not just a signature scheme.
2. It targets four properties together: minimality of information, unlinkability, non-duplication, and privacy-preserving revocation.
3. It is based on DAA+ and adapts BBS+ short group signatures with a CM-held device secret `Q = h^sk`.
4. It uses an LVVC-style status credential for revocation.
5. The lifecycle is `IssuerSetup`, `Issuing`, `LVVCIssuing`, then `Presentation`.
6. Presentation acceptance requires both the AC and LVVC presentations to verify, their pseudonyms to link, and the revocation information to satisfy the verifier's policy.
7. LVVCs are time-limited and refreshed by the issuer, making revocation a renewal decision.
8. The AC-status link is intentional; ordinary AC presentations remain unlinkable if basenames are fresh and attributes are not unique.
9. Partial secure-hardware storage and the CM-held `sk` provide non-duplication.
10. The design uses ideal functionalities `F_crs`, `F_ca`, `F_l_smt`, and `F_auth*`, and ISO/IEC 9798-2:2019 authentication for LVVC issuance.
11. Section 3.4 is a security discussion, not a formal proof; the paper does not provide theorem statements or a reduction.
12. The core/helper design does not protect against malware on the helper, and attribute data is not encrypted by the specification.

## Limitations and caveats

**Trusted parties.** The issuer is trusted to attest attributes and manage revocation. The certificate authority, common reference string, secure-message, and authenticated-channel functionalities are trusted. The CM manufacturer is trusted. These are explicit assumptions, not consequences of the cryptography.

**Core/helper malware.** A malicious helper can learn attributes because the specification does not encrypt attribute data, and it can attack privacy or credential usage when it interacts with the core. Whether this is acceptable is use-case dependent.

**No formal proof or benchmark.** The paper gives a security discussion, not formal unforgeability/privacy theorems, and no implementation or performance evaluation. Do not cite latency, throughput, signature size, or proof size from this paper.

**Scoped linkability.** The AC-LVVC link is deliberate. Repeated linked presentations, a reused basename, or a stable status window can enable correlation. "Unlinkable" does not mean "no linkability anywhere."

**Revocation freshness and issuer power.** The issuer can refuse renewal, learn update timing, and correlate updates with a known holder. Revocation is not transparent. Verifiers must enforce an LVVC freshness window or an old status credential can be replayed.

**Cryptography and hardware.** BBS+ is pairing-based and not post-quantum. Many secure elements do not support pairing-friendly curves, so "any phone" is too strong. Device binding is only as strong as the CM and its firmware; a compromised device key, debug interface, or relay can break it.

**Attribute leakage.** Even with selective disclosure, unique attributes or unique combinations can link presentations. The holder must manage this; the cryptography does not.

## Hackathon hooks

**Enables.** Privacy-preserving allowlists and access passes; non-transferable faucet/airdrop/event credentials anchored in passkeys or an SE; revocable privacy credentials where status is refreshed off-chain; a design pattern for a shielded-value gate where payment privacy and attribute privacy are separate.

**Does not enable.** Trustless/decentralized revocation; non-duplication without trusted hardware; issuer blindness to updates; post-quantum security; universal unlinkability; a verified formal guarantee or benchmark.

**Demo ideas.**

1. **Shielded status gate.** A BBS+ wallet proves age/residency, a mock issuer refreshes an LVVC, and a Monad contract verifies the linked pair before releasing a Zcash testnet payout. Show the verifier learns only the predicate and freshness.
2. **Clone-resistant faucet.** Bind to a passkey or software TPM; show copying the credential file to another device fails the fresh PoP.
3. **Revocation without a list.** A revoked user cannot obtain a fresh LVVC; an honest user can; the verifier never calls the issuer during presentation.
4. **Linkability scope.** Show ordinary presentations are unlinkable, the AC-LVVC pair is linkable within a session, and reusing the same basename across verifiers leaks a correlation.

## Claims to avoid

- "The paper formally proves unforgeability, unlinkability, non-duplication, and revocation privacy." It gives a security discussion, not formal theorems.
- "Non-duplication works without trusted hardware."
- "Revocation is fully private." The issuer sees updates and the AC-status link is intentional.
- "The system is trustless." Issuer, status issuer, ideal functionalities, and CM manufacturer are trusted differently.
- "Compatible with all phones/secure elements." BBS+ pairing support is not universal.
- "Post-quantum." BBS+ is pairing-based.
- "Implemented and benchmarked." No evaluation was found; implementation details are explicitly out of scope.
- Any specific proof size, latency, throughput, or page count beyond the verified 30-page length.
