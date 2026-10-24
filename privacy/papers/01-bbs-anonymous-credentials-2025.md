# A Specification of an Anonymous Credential System Using BBS+ Signatures with Privacy-Preserving Revocation and Device Binding

## Metadata

- **Authors:** Christoph Graebnitz, Nicolas Buchmann, Martin Seiffert, Marian Margraf (Fraunhofer AISEC, Secure Systems Engineering, Garching).
- **Date:** ePrint received and approved 2025-05-09; indexed PDF dated 2025-05-02.
- **Venue / ID:** Cryptology ePrint Archive, Paper 2025/824; category "Cryptographic protocols"; preprint, CC BY.
- **Length:** Not verified; the PDF was not readable in full.
- **Type:** Specification / system design for an anonymous-credential (AC) system. Not verified as a formal-proof or implementation paper.
- **Read status:** Local metadata read fully; PDF blocked. `[L]` = local abstract/metadata. `[X]` = search-engine PDF excerpt. `[S]` = secondary source. `[B]` = background. `[I]` = inference.

## One-paragraph summary

This paper specifies an anonymous-credential system combining BBS+ signatures, DAA-inspired hardware binding, and an LVVC-style status credential. The stated goals are minimality of information, unlinkability, non-duplication, and privacy-preserving revocation. Part of the credential lives on secure hardware; a linked status credential reflects revocation state. In the indexed revocation flow, the issuer first issues the AC, then issues an LVVC containing revocation information, and the holder creates deliberately linkable presentations of both so the verifier can check validity, linkage, and status `[X]`. The result is a deployable design blueprint, not a verified proof-and-benchmark paper.

## Problem and threat model

Standard AC presentations already provide selective disclosure and, with BBS+, unlinkable multi-show. The paper argues that specifications protect privacy during presentation but neglect privacy for non-duplication and revocation `[L]`.

Threats implied by the design:

- **Curious or colluding verifiers.** They should not learn hidden attributes or link two ordinary presentations. Collusion with an issuer should not reconstruct a tracking identifier.
- **A holder who copies a credential.** BBS+ is bearer-like without a hardware anchor. Non-duplication needs a non-exportable secure-element/TPM secret and a fresh hardware contribution per presentation.
- **Revocation leakage.** Naive checks reveal which credential is used or force per-presentation issuer contact. A status credential is meant to avoid both `[X]`.
- **Replay/context confusion.** Presentations need a verifier nonce and transcript binding.

Trust assumptions are significant. The issuer is trusted to attest attributes and manage revocation. The status issuer is trusted to refuse renewals for revoked credentials. Secure hardware is trusted for non-duplication and partly for privacy. BBS+ is pairing-based, not post-quantum. The design does not blind the issuer to status-update timing and does not make revocation transparent or trustless.

## Architecture / protocol

Roles are issuer, holder/device, and verifier. The credential is split between ordinary storage and secure hardware. The hardware holds a device key or credential share and produces a fresh proof of possession per presentation. This is the DAA-style part: DAA lets a hardware module prove group membership without revealing which member it is, optionally using a basename to make signatures linkable only in a chosen context `[B]`.

Revocation uses a status credential. An LVVC is itself an AC containing only revocation information for a corresponding AC `[X]`. The indexed three-step lifecycle is `[X]`:

1. The issuer issues the AC.
2. Before use, the issuer issues an LVVC with current revocation information.
3. The holder creates linkable presentations of the AC and LVVC. The verifier accepts only if both are valid, linked, and the LVVC contains proper revocation information.

The LVVC can be time-limited. If the credential is not revoked, the issuer can issue a fresh LVVC with a newer date. This follows Freitag's LVVC pattern: a fixed-size status credential is periodically updated, and the verifier checks signatures, the credential-status link, and freshness `[B][S]`. Verification needs no issuer contact; updates do, so the issuer learns update timing and can deny service.

Indexed text exposes Figure 2 (BBS+ specification), Figure 3 (revocation flow), and Figure 9 (`IssuerSetup`, generating BBS+ key material with an issuer CA) `[X]`. The paper uses signature-proof-of-knowledge notation over a message, witness, group basis, and public value `[X]`. Exact messages, games, and parameters are unavailable.

## Section-by-section findings

Numbering is partly reconstructed.

1. **Introduction.** Motivates ACs for data minimization, then identifies the gap: presentation privacy is studied, non-duplication and revocation privacy are not. States the four requirements `[L]`.
2. **Background.** Covers BBS+ (Figure 2), DAA hardware binding, and revocation. Subsection 2.3.1, "Linked Validity Verifiable Credentials (LVVCs)," defines LVVCs as ACs carrying only revocation information `[X]`.
3. **Requirements / model.** The design must keep ordinary presentations unlinkable while allowing a controlled AC-LVVC link. That is a deliberate, scoped linkability exception `[I]`.
4. **Specification.** `IssuerSetup` generates BBS+ and issuer-CA material; issuance produces the AC; status issuance produces the LVVC; presentation produces the linked pair. The three-step flow is confirmed `[X]`.
5. **Device binding.** Adapts DAA hardware binding to BBS+ short group signatures associated with Camenisch et al. `[L]`. The aim is clone resistance and fresh hardware involvement. Exact binding equations are unverified.
6. **Security discussion / conclusion.** Claims the four requirements. A Freie Universität Berlin thesis listing describes the paper as leaving a rigorous formal proof and practical evaluation open `[S]`.

## Key mechanisms and equations

**BBS+ background `[B]`.** In bilinear groups `G1`, `G2`, `GT` with pairing `e`, a standard BBS verification equation for signature `(A, e)` on messages `m_i`, secret `x`, public `X = g2^x`, is:

```text
e(A, X * g2^e) = e(g1 * product_i h_i^{m_i}, g2)
```

The holder proves knowledge of `(A, e, {m_i}_{hidden})` satisfying this equation while revealing chosen messages. Re-randomizing `A` makes proofs unlinkable. BBS+ variants add commitments or scalars; the exact variant is unverified. This is standard BBS, not a transcription from the paper.

**Schnorr SPK `[B]`.** For `y = g^alpha`, prove `t = g^r`, `c = H(g, y, message, t)`, `z = r + c*alpha`; verify `g^z = y^c * t`. The paper uses SPK notation of this general form `[X]`; the challenge encoding is unverified.

**LVVC steps `[X]`.**

```text
1. issue AC
2. issue LVVC with revocation information
3. holder creates linkable VPs for AC and LVVC
   accept iff VP_AC valid, VP_LVVC valid,
           VPs linked, and LVVC revocation info proper
```

**Device binding `[B][I]`.** An SE/TPM generates non-exportable `sk_dev`. The credential is bound to `pk_dev`, by a signed attribute or a shared credential secret. At presentation:

```text
sigma_dev = Sign(sk_dev, transcript || nonce || verifier_id)
```

The verifier checks `sigma_dev` under a certified `pk_dev`. A copied credential lacks `sk_dev`. DAA basenames can give controlled per-context linkability. The exact adaptation is unverified.

**Scoped linkability `[I]`.** The AC and LVVC must be linkable, otherwise a holder could pair a valid AC with an unrelated status credential. This does not make all presentations linkable, but it creates a correlation channel: any verifier seeing the pair learns they belong together. Reusing the same status presentation can create cross-verifier linkability.

## Experiments / evaluation

None verified. No benchmarks, implementation results, or performance tables are in the local metadata. The FU Berlin listing proposes future prototyping and measurement with SEs/TPMs and a separate formal analysis `[S]`. Do not cite latency, throughput, signature size, or proof size.

## Paper-specific findings

1. The paper specifies a complete AC system, not just a signature scheme `[L]`.
2. It targets four properties together: minimality, unlinkability, non-duplication, privacy-preserving revocation `[L]`.
3. It combines DAA-style hardware binding with BBS+ short group signatures `[L]`.
4. It uses an LVVC-style status credential for revocation `[X]`.
5. The flow is AC issuance, LVVC issuance, then linked presentations `[X]`.
6. Acceptance requires both presentations valid, linked, and proper revocation information `[X]`.
7. LVVCs are time-limited and refreshed by the issuer, making revocation a renewal decision `[X][B]`.
8. The AC-status link is intentional while ordinary AC presentations remain unlinkable `[I]`.
9. Partial secure-hardware storage provides non-duplication `[L]`.
10. The design uses SPK proofs and an `IssuerSetup` with BBS+ keys and an issuer CA `[X]`.
11. No formal proof or evaluation is verified; a secondary thesis listing calls both open `[S]`.
12. It is positioned against Verifiable Credentials Data Model formats, but exact standards claims are unverified `[X]`.

## Limitations and caveats

**Access.** The full PDF was not read (HTTP 403). Exact algorithms, security definitions, page count, parameters, proofs, and implementation claims are unconfirmed. Search excerpts are selective.

**Trusted hardware.** Non-duplication is only as strong as the SE/TPM and firmware. A compromised device key, debug interface, or relay can break it. DAA-style binding does not necessarily stop real-time challenge relaying. Privacy may depend on hardware honesty.

**Issuer/status authority.** The issuer can refuse renewal, learn update timing, and correlate updates with a known holder. Revocation is not transparent.

**Scoped linkability.** The AC-LVVC link is deliberate. Repeated linked presentations or a stable status identifier can enable correlation. "Unlinkable" does not mean "no linkability anywhere."

**No proof/evaluation verified.** The design may be sound, but this note cannot claim formal unforgeability, formal privacy, or performance. The FU Berlin listing suggests exactly that gap `[S]`.

**Cryptography.** BBS+ is pairing-based and not post-quantum. Many secure elements do not support pairing-friendly curves, so "any phone" is too strong `[B]`.

**Freshness.** Verifiers must enforce an LVVC freshness window or an old status credential can be replayed. Exact rules are unverified.

## Hackathon hooks

**Enables.** Privacy-preserving allowlists and access passes; non-transferable faucet/airdrop/event credentials anchored in passkeys or an SE; revocable privacy credentials where status is refreshed off-chain; a design pattern for a shielded-value gate where payment privacy and attribute privacy are separate.

**Does not enable.** Trustless/decentralized revocation; non-duplication without trusted hardware; issuer blindness to updates; post-quantum security; universal unlinkability; a verified formal guarantee or benchmark.

**Demo ideas.**

1. **Shielded status gate.** A BBS+ wallet proves age/residency, a mock issuer refreshes an LVVC, and a Monad contract verifies the linked pair before releasing a Zcash testnet payout. Show the verifier learns only the predicate and freshness.
2. **Clone-resistant faucet.** Bind to a passkey or software TPM; show copying the credential file to another device fails the fresh PoP.
3. **Revocation without a list.** A revoked user cannot obtain a fresh LVVC; an honest user can; the verifier never calls the issuer during presentation.
4. **Linkability scope.** Show ordinary presentations are unlinkable, the AC-LVVC pair is linkable within a session, and replaying the same status presentation across verifiers leaks a correlation.

## Claims to avoid

- "The paper formally proves unforgeability, unlinkability, non-duplication, and revocation privacy." No proof was verified; a secondary source calls it future work.
- "Non-duplication works without trusted hardware."
- "Revocation is fully private." The issuer sees updates and the AC-status link is intentional.
- "The system is trustless." Issuer, status issuer, and hardware are trusted differently.
- "Compatible with all phones/secure elements." BBS+ pairing support is not universal.
- "Post-quantum." BBS+ is pairing-based.
- "Implemented and benchmarked." No evaluation was verified.
- Any specific proof size, latency, throughput, or page count.
