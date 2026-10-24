# Improved Issuer Hiding for BBS-based Anonymous Credentials

## Metadata

- **Authors:** Nesrine Kaaniche (SAMOVAR, Télécom SudParis, Institut Polytechnique de Paris), Seyni Kane (Orange Innovation, Applied Crypto Group, Caen; SAMOVAR), Maryline Laurent (SAMOVAR), Jacques Traoré (Orange Innovation, Applied Crypto Group, Caen).
- **Date:** ePrint received 2026-03-20; revised 2026-07-03.
- **Venue / ID:** Cryptology ePrint Archive, Paper 2026/555; category "Cryptographic protocols"; preprint, CC BY-NC.
- **Length:** Not verified; the PDF was not readable in full.
- **Type:** Cryptographic protocol paper with security model, constructions, proofs, and reported implementation.
- **Read status:** Local metadata read; PDF blocked. `[L]` = local metadata. `[X]` = search-engine PDF excerpt. `[S]` = secondary/related source. `[B]` = background. `[I]` = inference.

## One-paragraph summary

This paper argues that issuer-hiding anonymous credentials rest on an unrealistic assumption: honest issuers. It presents attacks where a malicious user colludes with one authorized issuer to forge credentials on arbitrary attributes and impersonate any trusted issuer, with no way to identify the misbehaving issuer `[L]`. The authors propose a stronger model with malicious issuers, then construct an issuer-hiding AC from BBS signatures using a signed policy. The construction is proved in the Algebraic Group Model (AGM), removes secret policy keys, and permits policy generation to be delegated to a trusted certification authority `[L]`. It also introduces Accountable Issuer Hiding (AIH): each presentation encrypts the issuer public key under an opening authority's key, bound by zero-knowledge proofs to the credential and policy, enabling controlled attribution `[X]`. A pairing-free scheme from algebraic MACs is also introduced `[L]`.

## Problem and threat model

In a normal AC, a presentation reveals the issuer key, or the verifier must know which key to use. That leaks information: a credential from a particular government, employer, or health authority can identify a category or jurisdiction. **Issuer hiding** lets the verifier define a policy `P`, a set of accepted issuer keys, and lets the holder prove its credential came from *some* issuer in `P` without revealing which. The policy is the anonymity set.

Prior constructions, including Katz-Sefranek's BBS scheme, use a policy key: verification is relative to a certified policy, sometimes with a secret policy key. The paper argues these models assume honest issuers, which is inadequate `[L]`. The attack pattern: a malicious user colludes with **one authorized issuer**, who helps issue credentials for **arbitrary attributes** outside its mandate; the user then presents as if the credential came from **any trusted issuer** in the policy. Because the presentation hides which issuer participated, the misbehaving issuer cannot be identified `[L][X]`. The indexed text says the attacks target the presentation policy: colluding issuers enable credentials for arbitrary attributes using self-generated issuing keys, so the user effectively assumes an authorized issuer's role `[X]`. Malformed policy keys and missing well-formedness proofs are also mentioned in related work `[S]`.

The stronger model must capture:

- **Unforgeability with malicious issuers.** A user colluding with corrupt issuers must not present attributes no honest policy member attested, nor usurp an issuer identity.
- **Policy unforgeability.** An adversary must not add its key to a verifier's policy. The paper says this reduces to forging a valid BBS signature `[X]`.
- **Issuer-hiding anonymity.** The presentation hides which policy member issued it, ideally "everlasting."
- **Accountability.** A fraudulent presentation can be opened to identify the responsible issuer.

Trust assumptions matter. The verifier's policy is authentic; policy generation may be delegated to a trusted CA `[L]`. The AIH opening authority is trusted to decrypt only under a controlled procedure and can deanonymize if malicious or coerced. Issuers may be malicious but are registered with public keys. Proofs are in the AGM. BBS is pairing-based and not post-quantum. Revocation, status, and holder device binding are not the verified focus.

## Architecture / protocol

The base is BBS. Indexed text confirms the construction reuses standard BBS AC algorithms `Setup`, `Issue`, `VerifyCred`, `Show`, and `Verify`, changing only the presentation/policy layer `[X]`. BBS signs an attribute vector; the holder proves possession while disclosing a subset; re-randomization gives unlinkability.

The issuer-hiding layer works schematically:

1. Verifier defines `P = {ipk_1, ..., ipk_n}`.
2. The policy is signed by the verifier or a delegated CA; the signed policy is public and independently verifiable.
3. The holder proves: there exists `ipk` in `P`; it holds a valid BBS credential under `ipk` for the disclosed attributes; the policy signature is valid; and the proof is bound to the verifier challenge.
4. The verifier checks the presentation and policy without learning which `ipk` was used.

The key choice is the **signed-policy approach**. Because the policy is authenticated by a signature rather than a secret policy key, verification is public and uses no secret values `[L]`. This improves on constructions requiring per-verifier policy keys and secret-key verification. The indexed text confirms only the policy layer changes; `Setup`, `Issue`, and `VerifyCred` stay identical `[X]`.

**Accountable Issuer Hiding (AIH)** adds controlled opening. Every presentation includes an encryption of the issuer public key under the opening authority's key:

```text
ct = Enc(opk, ipk; r)
```

with a zero-knowledge proof that the encrypted key is the one that generated the credential and belongs to the accepted policy `[X]`. Normal verifiers cannot decrypt. Under a defined opening procedure, the authority decrypts and identifies the issuer. This addresses a malicious issuer that gives users opaque devices generating valid presentations on demand: the presentation remains attributable `[L]`.

The paper also introduces a **pairing-free issuer-hiding scheme from algebraic MACs**, claimed as the first pairing-free issuer-hiding AC `[L]`. The motivation is deployment: pairing-friendly curves and BBS are not supported by all secure hardware. The exact MAC construction, assumptions, and performance are unverified.

The paper reports proofs of unforgeability, everlasting issuer-hiding anonymity, and accountable issuer hiding `[L]`. It claims to resolve three Katz-Sefranek open challenges: AGM rather than GGM security, no secret policy keys, and delegated policy generation `[L]`.

## Section-by-section findings

Numbering is partly inferred.

1. **Introduction.** Defines issuer hiding, reviews prior work, and argues existing models miss issuer collusion `[L]`.
2. **Preliminaries.** BBS (Eurocrypt 2023), AC syntax, bilinear groups, hardness assumptions `[L][X]`.
3. **Security model.** Malicious issuers, policy unforgeability, presentation unforgeability, issuer-hiding anonymity, accountability `[L]`.
4. **Katz-Sefranek BBS scheme.** Revisits the prior construction and outlines attacks on it and related schemes `[X]`.
5. **Improved BBS construction.** Section 5.1 uses a verifier-signed policy; `Setup`, `Issue`, and `VerifyCred` are omitted as identical to base BBS `[X]`.
6. **Accountable Issuer Hiding.** Defines AIH, the encryption-plus-ZK construction, and controlled opening `[L]`.
7. **Pairing-free construction.** Algebraic-MAC-based issuer hiding `[L]`.
8. **Proofs and implementation.** AGM proofs and reported results; no numbers verified.

## Key mechanisms and equations

**BBS background `[B]`.** In bilinear groups `G1`, `G2`, `GT` with pairing `e`, a standard BBS verification equation for signature `(A, e)` on messages `m_i`, secret `x`, public `X = g2^x`, is:

```text
e(A, X * g2^e) = e(g1 * product_i h_i^{m_i}, g2)
```

The holder proves knowledge of `(A, e, {m_i}_{hidden})` and reveals chosen messages. Re-randomization gives unlinkability. The exact variant used here is unverified.

**Issuer-hiding statement `[I]`.** The holder proves:

```text
exists ipk in P, exists sigma:
    BBS.Verify(ipk, attributes, sigma) = 1
    and disclosed attributes match the request
    and the proof is bound to nonce/context
```

The witness includes `ipk` and `sigma`; the verifier learns only that some policy member satisfies it. A naive OR-proof scales with `|P|`. The paper's exact proof-size scaling is unverified.

**Signed policy `[X][I]`.**

```text
tau = Sign(sk_policy, P)
verify: Vf(pk_policy, P, tau) = 1
```

The signer may be the verifier or a delegated CA. Indexed text confirms a verifier-signed policy and CA delegation, and states policy unforgeability is equivalent to forging a valid BBS signature `[X]`. Exact encoding is unverified.

**AIH `[X]`.**

```text
ct = Enc(opk, ipk; r)
pi_AIH = NIZK{
  (ipk, r, sigma):
    ct = Enc(opk, ipk; r)
    and BBS.Verify(ipk, attributes, sigma) = 1
    and ipk in P and policy signature valid
}
opening: ipk = Dec(osk, ct)
```

The proof binds the ciphertext to the credential and policy, preventing substitution. The encryption scheme, proof system, and opening protocol are unverified.

**AGM vs GGM `[B]`.** In the GGM, adversaries access group operations only through an oracle; in the AGM, they output group elements with algebraic representations. AGM proofs are often considered closer to real attacks but remain idealized. The paper claims AGM rather than GGM security `[L]`. Its pairing-free variant uses algebraic MACs over conventional elliptic curves; the exact tag equation and assumptions are unverified `[B][I]`.

## Experiments / evaluation

The abstract says implementation results confirm practicality `[L]`. No benchmark values, proof sizes, timings, or hardware targets were verified. It reports an implementation and targets EUDI; magnitude unverified.

## Paper-specific findings

1. Prior issuer-hiding security models assume honest issuers `[L]`.
2. One authorized issuer plus a malicious user can yield credentials on arbitrary attributes `[L]`.
3. The user can impersonate any trusted issuer without identifying the misbehaving issuer `[L]`.
4. The attacks target the presentation policy and can use self-generated issuing keys `[X]`.
5. A stronger model explicitly accounts for malicious issuers `[L]`.
6. A BBS-based issuer-hiding AC uses a signed policy `[L][X]`.
7. The construction is proved in the AGM rather than the GGM `[L]`.
8. It eliminates secret policy keys, so verification uses no secret values `[L]`.
9. It supports delegating policy generation to a trusted CA `[L]`.
10. AIH encrypts the issuer public key and proves consistency with the credential and policy `[X]`.
11. A pairing-free issuer-hiding AC from algebraic MACs is introduced as a first `[L]`.
12. It targets EUDI and claims unforgeability, everlasting issuer-hiding anonymity, and accountable issuer hiding `[L]`.

## Limitations and caveats

**Access.** The full PDF was not read (HTTP 403); games, constructions, proofs, parameters, numbers are unverified.

**Opening authority trust.** AIH replaces one trust problem with another. The authority can deanonymize; if compromised, coerced, or colluding with a verifier, issuer privacy is lost. If too constrained, accountability fails. Governance assumptions are unverified.

**Issuer registration and policy authority.** Accountability requires registered keys bound to real identities; unregistered keys may escape attribution. A delegated CA can expand or shrink the policy, including making it a singleton and weakening anonymity.

**Privacy-accountability tension.** Every presentation carries an encrypted issuer key. Flawed encryption/proofs or decryption logs degrade privacy. The proof may hide the issuer statistically, but the ciphertext and opening capability exist.

**Revocation/status.** The verified abstract does not address revocation, status, or non-transferability. Status lists can reintroduce linkability.

**Model idealization and pairing-free variant.** AGM remains idealized; proofs do not cover implementation bugs or side channels. The pairing-free variant's assumptions and efficiency are unverified, and "pairing-free" is not post-quantum.

## Hackathon hooks

**Enables.** Privacy-preserving allowlists where a user proves "a credential from one of these approved issuers" without revealing which; accountable compliance where normal users stay issuer-anonymous but fraud can be escalated to an opening authority; a Monad policy registry with signed issuer sets; a Zcash-adjacent gate where payment stays shielded while the credential gate supports attribution.

**Does not enable.** Trustless accountability; revocation/status; hiding the issuer from the opening authority; hiding attributes beyond BBS selective disclosure; hiding the policy itself; post-quantum security.

**Demo ideas.**

1. **Hidden issuer, accountable fraud.** Three mock issuers, a signed policy on Monad, and a BBS presentation proving "one of three." Then simulate a colluding issuer and open the AIH ciphertext with a mock authority to attribute the fraud. One flow demonstrates privacy and accountability.
2. **Policy delegation.** A verifier delegates policy signing to a mock CA, rotates the issuer set without a secret policy key, and rejects a forged policy update.
3. **Anonymity meter.** Shrink the policy from many issuers to one to show what the verifier learns.

## Claims to avoid

- "Trustless." It relies on a policy authority, issuer registration, and an opening authority.
- "Fully anonymous." Hiding is within a policy and the opening authority can deanonymize.
- "Post-quantum." BBS and elliptic-curve MACs are not.
- "Constant-size" or "scales to thousands of issuers" unless exact complexity is verified.
- "EUDI-certified." It targets EUDI; that is not certification.
- "Breaks all issuer-hiding schemes." The claim targets specific constructions assuming honest issuers.
