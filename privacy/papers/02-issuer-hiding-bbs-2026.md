# Improved Issuer Hiding for BBS-based Anonymous Credentials

## Metadata

- **Authors:** Nesrine Kaaniche (SAMOVAR, Télécom SudParis, Institut Polytechnique de Paris), Seyni Kane (Orange Innovation, Applied Crypto Group, Caen; SAMOVAR), Maryline Laurent (SAMOVAR), Jacques Traoré (Orange Innovation, Applied Crypto Group, Caen).
- **Date:** ePrint received 2026-03-20; revised 2026-07-03.
- **Venue / ID:** Cryptology ePrint Archive, Paper 2026/555; category "Cryptographic protocols"; preprint, CC BY-NC.
- **Length:** 43 pages for the 2026-03-20 version (main text pp. 1-29, bibliography pp. 30-32, appendices pp. 33-43).
- **Type:** Cryptographic protocol paper with attacks, security model, constructions, proofs, and a complexity evaluation. The paper claims implementation results, but the performance section reports element counts and operation counts, not measured wall-clock benchmarks.
- **Read status:** full PDF read for the 2026-03-20 version from an Internet Archive copy of the ePrint PDF. The current 2026-07-03 revision was not retrievable because the live ePrint PDF is Cloudflare-blocked. Accountable Issuer Hiding (AIH) appears in later-revision excerpts, not in the 2026-03-20 full text; it is marked separately below.

## One-paragraph summary

This paper argues that issuer-hiding anonymous credentials rest on an unrealistic assumption: honest issuers. It presents attacks where a malicious user colludes with one authorized issuer to forge credentials on arbitrary attributes and impersonate any trusted issuer, with no way to identify the misbehaving issuer. The authors propose a stronger model with malicious issuers, then construct an issuer-hiding AC from BBS signatures using a verifier-signed policy. The construction is proved unforgeable in the Algebraic Group Model (AGM), removes secret policy keys, and permits policy generation to be delegated to a trusted certification authority. It also introduces a pairing-free issuer-hiding scheme from algebraic MACs, claimed as the first pairing-free issuer-hiding AC. A later revision adds Accountable Issuer Hiding (AIH), an encrypted issuer-key opening mechanism; that addition is not part of the 2026-03-20 full text read here.

## Problem and threat model

In a normal AC, a presentation reveals the issuer key, or the verifier must know which key to use. That leaks information: a credential from a particular government, employer, or health authority can identify a category or jurisdiction. **Issuer hiding** lets the verifier define a policy `P`, a set of accepted issuer keys, and lets the holder prove its credential came from *some* issuer in `P` without revealing which. The policy is the anonymity set.

Prior constructions, including Katz-Sefranek's BBS scheme and the Sanders-Traoré approach, use a policy key: verification is relative to a certified policy, sometimes with a secret policy key. The paper argues these models assume honest issuers, which is inadequate. The attack pattern: a malicious user colludes with **one authorized issuer**, who helps issue credentials for **arbitrary attributes** outside its mandate; the user then presents as if the credential came from **any trusted issuer** in the policy. Because the presentation hides which issuer participated, the misbehaving issuer cannot be identified. The paper says the attacks target the presentation policy and can use self-generated issuing keys, so the user effectively assumes an authorized issuer's role. Appendix A gives the detailed attack scenarios.

The stronger model must capture:

- **Unforgeability with malicious issuers.** A user colluding with corrupt issuers must not present attributes no honest policy member attested, nor usurp an issuer identity.
- **Policy unforgeability.** An adversary must not add its key to a verifier's policy. The paper says this reduces to forging a valid BBS signature.
- **Issuer-hiding anonymity.** The presentation hides which policy member issued it. The paper proves an "everlasting" notion: unconditional if the setup and user zero-knowledge proofs are sound.
- **Accountability (later revision).** A fraudulent presentation can be opened to identify the responsible issuer. This is the AIH addition, not verified in the 2026-03-20 full text.

Trust assumptions matter. The verifier's policy is authentic; policy generation may be delegated to a trusted certification authority. The policy authority can add or remove issuers, including shrinking the policy to a singleton. Issuers may be malicious but are registered with public keys. Proofs are in the AGM. BBS is pairing-based and not post-quantum. Revocation, status, and holder device binding are not the verified focus.

## Architecture / protocol

The base is BBS. The construction reuses standard BBS AC algorithms `Setup`, `Issue`, `VerifyCred`, `Show`, and `Verify`, changing the presentation/policy layer. The paper specifies the remaining algorithms in Section 5.1:

- **`IKeyGen(pp)`.** Select `y` and return `(ipk, ipk_tilde, isk) = (g^y, g_tilde^y, y)`. The issuer also computes `pi_key = PoK{ alpha : ipk = g^alpha and ipk_tilde = g_tilde^alpha }`.
- **`KeyGen(pp)`.** Select `x` and return the verifier policy key `(vpk, vpk_tilde, vsk) = (h^x, h_tilde^x, x)`.
- **`GenPolicy(P)`.** Parse `P = {(ipk_j, ipk_tilde_j)}_{j in [ell]}`. Verify each `pi_key`; if valid, certify issuer `j` with `sigma_j = (A_j, e_j)`, where `A_j = (g0 * ipk_j)^{1/(x + e_j)}` and `e_j` is chosen from `Z_p \ {-x}`. Output `pol = (vpk, {(ipk_j, ipk_tilde_j, sigma_j)}_{j in [ell]})`.
- **`VerifyPolicy(P, pol)`.** For each `j`, set `B_j = g0 * ipk_j * A_j^{-e_j} = A_j^x`. Accept if `ell = |P|` and, for all `j`, `e(B_j, h_tilde) = e(A_j, vpk_tilde)`.
- **`Show(...)`.** Randomize the issuer key, certificate, and credential. Select `t, r, r1, r2`; compute `ipk'_j = g^{t y_j}`, `ipk_tilde'_j = g_tilde^{t y_j}`, randomized certificate terms, `A_bar = A^l`, `B_bar = C_m^{t_hat} A_bar^{-e'}`, `D_bar = C_m^{r2}`, and randomized credential terms. Produce two proofs:
  - `pi_cert = PoK{ alpha, beta, delta : B_bar_j = g0^alpha * ipk'_j^beta * A_bar_j^{-delta} and alpha != 0 }`
  - `pi_validity = PoK{ alpha, beta, gamma, {tau_i}_{i notin D} : B_bar = A_bar^{-alpha} D_bar^beta and h0 * product_{i in D} h_i^{tau_i} = D_bar^gamma * product_{i notin D} h_i^{-tau_i} and beta != 0 }`
  - The verifiable presentation is `vp = ({m}_{i in D}, ipk'_j, ipk_tilde'_j, A_bar_j, B_bar_j, A_bar, B_bar, D_bar, pi_cert, pi_validity)`.
- **`Verify(...)`.** If the policy is a singleton, use the original BBS `Verify`. Otherwise accept if `A_bar != 1`, both proofs verify, and the three pairing equations hold:
  - `e(ipk'_j, g_tilde) = e(g, ipk_tilde'_j)`
  - `e(B_bar_j, h_tilde) = e(A_bar_j, vpk_tilde)`
  - `e(B_bar, g_tilde) = e(A_bar, ipk_tilde'_j)`

The key choice is the **signed-policy approach**. Because the policy is authenticated by a BBS signature/certificate rather than a secret policy key, verification is public and uses no secret values. The indexed and full text confirm that only the policy layer changes; `Setup`, `Issue`, and `VerifyCred` stay identical to base BBS.

**Pairing-free scheme (Section 6).** The paper also constructs a pairing-free issuer-hiding AC from algebraic MACs, following Desmoulins et al. The algorithms mirror Section 5, but the credential is a MAC-style tuple `(A, e)` with `A = C_m^{1/(y+e)}`, and verification uses a discrete-log equality proof instead of pairings. A randomized-credential DLEQ proof, `pi_dleq_rand-cred`, must be obtained anonymously from the issuer, for example via the Orrù et al. oblivious issuance technique. The paper calls this the first pairing-free issuer-hiding AC.

**Accountable Issuer Hiding (later revision, excerpt-level).** The current revision reportedly adds AIH: every presentation includes `ct = Enc(opk, ipk; r)` under an opening authority's key, plus a zero-knowledge proof that the encrypted key is the one that generated the credential and belongs to the accepted policy. Normal verifiers cannot decrypt; under a defined opening procedure, the authority identifies the issuer. This section is based on later-revision excerpts, not on the 2026-03-20 full text. Do not present it as verified from the full paper without checking the latest revision.

## Section-by-section findings

1. **Introduction.** Defines issuer hiding, reviews prior work, and argues existing models miss issuer collusion.
2. **Preliminaries.** BBS signatures, AC syntax, bilinear groups, hardness assumptions.
3. **Issuer-Hiding Anonymous Credentials.** Defines the syntax and security model, including malicious issuers, policy unforgeability, presentation unforgeability, issuer-hiding anonymity, and accountability.
4. **Katz and Sefranek BBS-based issuer-hiding.** Revisits the prior construction and outlines attacks on it and related schemes. Appendix A expands the attacks.
5. **Improved Issuer-Hiding for BBS-Based ACs.** Section 5.1 gives the signed-policy construction above; Section 5.2 proves unforgeability in the AGM (Theorem 1) and everlasting issuer-hiding anonymity (Theorem 2).
6. **Extension to a Pairing-free Scheme.** Section 6.1 gives the algebraic-MAC construction; Section 6.2 argues unforgeability and anonymity follow from the pairing-based construction.
7. **Performance Evaluation.** Table 1 compares element sizes; Table 2 compares exponentiation and pairing counts. There are no measured timings.
8. **Conclusion.** Claims efficiency, AGM security, everlasting anonymity, and suitability for EUDIW/IETF. It also claims "implementation benchmarks," but Section 7 reports complexity counts rather than runtime measurements.
9. **Appendix A.** Attacks on some issuer-hiding ACs.
10. **Appendix B.** Proofs of Theorems 1 and 2.

## Key mechanisms and equations

**BBS background.** In bilinear groups `G1`, `G2`, `GT` with pairing `e`, a standard BBS verification equation for signature `(A, e)` on messages `m_i`, secret `x`, public `X = g2^x`, is:

```text
e(A, X * g2^e) = e(g1 * product_i h_i^{m_i}, g2)
```

The holder proves knowledge of `(A, e, {m_i}_{hidden})` and reveals chosen messages. Re-randomization gives unlinkability. This is background; the paper's exact BBS variant is in its Section 2.6.

**Issuer-hiding statement.** The holder proves:

```text
exists ipk in P, exists sigma:
    BBS.Verify(ipk, attributes, sigma) = 1
    and disclosed attributes match the request
    and the proof is bound to nonce/context
```

The witness includes `ipk` and `sigma`; the verifier learns only that some policy member satisfies it.

**Signed policy.**

```text
sigma_j = (A_j, e_j),  A_j = (g0 * ipk_j)^{1/(x + e_j)}
verify: e(B_j, h_tilde) = e(A_j, vpk_tilde),  B_j = A_j^x
```

The policy signer may be the verifier or a delegated certification authority. Policy unforgeability reduces to forging a valid BBS signature.

**Unforgeability (Theorem 1).** If `pi_setup`, `pi_user`, `pi_cert`, and `pi_validity` are straight-line extractable ZKPOKs and BBS is EUF-CMA-secure, then the protocol is unforgeable in the AGM with advantage bounded by the BBS EUF-CMA advantages plus the knowledge-soundness advantages of the four proofs.

**Issuer-hiding anonymity (Theorem 2).** If the zero-knowledge proofs `pi_setup` and `pi_user` are sound, issuer-hiding anonymity holds unconditionally. The proof adapts Katz-Sefranek.

**Pairing-free size/complexity (Tables 1-2).** With one disclosed attribute and `n` attributes, the paper's proof is `6|G1| + (n+6)|Zp|`; proof generation is `(n+14) exp1`; verification is `(n+13) exp1` with no pairings. Katz-Sefranek is `(n+6) exp1 + 1 exp2` to prove and `(n+5) exp1 + 2 pairings` to verify. Flamini et al. is `(n+5) exp1 + 2 exp2` to prove and `(n+1) exp1 + 2 pairings` to verify. These are operation counts, not timings.

**Accountable Issuer Hiding (later revision, excerpt-level).**

```text
ct = Enc(opk, ipk; r)
pi_AIH = NIZK{ (ipk, r, sigma):
    ct = Enc(opk, ipk; r)
    and BBS.Verify(ipk, attributes, sigma) = 1
    and ipk in P and policy signature valid }
opening: ipk = Dec(osk, ct)
```

The proof binds the ciphertext to the credential and policy. The opening authority is trusted to decrypt only under a controlled procedure and can deanonymize if malicious or coerced.

## Experiments / evaluation

The paper's evaluation is a complexity comparison, not a runtime benchmark:

- **Table 1** compares `pk_pol`, `sk_pol`, and proof sizes in group elements and `Z_p` elements for Katz-Sefranek, Flamini et al., and this paper.
- **Table 2** compares proof-generation and verification costs in exponentiations and pairings.
- **No measured latency, throughput, memory, or proof-size-in-bytes appears in Section 7.** The conclusion's phrase "implementation benchmarks" is not supported by measured numbers in the retrieved version.

The paper claims the pairing-free scheme has the lowest verification cost because it avoids pairings. The pairing-based scheme has comparable proof generation to prior work and lower verification overhead than Katz-Sefranek.

## Paper-specific findings

1. Prior issuer-hiding security models assume honest issuers.
2. One authorized issuer plus a malicious user can yield credentials on arbitrary attributes.
3. The user can impersonate any trusted issuer without identifying the misbehaving issuer.
4. The attacks target the presentation policy and can use self-generated issuing keys.
5. A stronger model explicitly accounts for malicious issuers.
6. A BBS-based issuer-hiding AC uses a verifier-signed policy with BBS-certified issuer keys.
7. The construction is proved unforgeable in the AGM rather than the GGM.
8. It eliminates secret policy keys, so verification uses no secret values.
9. It supports delegating policy generation to a trusted CA.
10. It introduces a pairing-free issuer-hiding AC from algebraic MACs, claimed as a first.
11. The pairing-free variant requires an anonymous issuer-issued DLEQ proof for the randomized credential.
12. A later revision adds Accountable Issuer Hiding (AIH), which encrypts the issuer key under an opening authority and proves consistency; this is not in the 2026-03-20 full text.
13. Performance is reported as element and operation counts, not measured runtime.
14. It targets EUDIW and IETF BBS standardization.

## Limitations and caveats

**Version gap.** We read the 2026-03-20 full text. The current ePrint page lists a 2026-07-03 revision. AIH and any later changes are not verified against the full latest PDF; treat AIH as excerpt-level until the revision is checked.

**Policy authority.** A delegated CA can expand or shrink the policy, including making it a singleton and weakening anonymity. Accountability requires registered keys bound to real identities; unregistered keys may escape attribution.

**Accountability trust (if AIH).** AIH replaces one trust problem with another. The opening authority can deanonymize; if compromised, coerced, or colluding with a verifier, issuer privacy is lost. If too constrained, accountability fails.

**Model idealization.** AGM remains idealized; proofs do not cover implementation bugs or side channels. The pairing-free variant relies on algebraic MACs and an oblivious issuance interaction, and "pairing-free" is not post-quantum.

**No revocation/status.** The verified full text does not address revocation, status, or non-transferability. Status lists can reintroduce linkability.

**No measured implementation.** The paper claims implementation results, but the retrieved version reports only asymptotic/element counts. Do not cite runtime or byte sizes as measured.

**Privacy-accountability tension.** Every AIH presentation carries an encrypted issuer key. Flawed encryption/proofs or decryption logs degrade privacy. The proof may hide the issuer statistically, but the ciphertext and opening capability exist.

## Hackathon hooks

**Enables.** Privacy-preserving allowlists where a user proves "a credential from one of these approved issuers" without revealing which; accountable compliance where normal users stay issuer-anonymous but fraud can be escalated to an opening authority; a Monad policy registry with signed issuer sets; a Zcash-adjacent gate where payment stays shielded while the credential gate supports attribution.

**Does not enable.** Trustless accountability; revocation/status; hiding the issuer from the opening authority; hiding attributes beyond BBS selective disclosure; hiding the policy itself; post-quantum security; measured production performance.

**Demo ideas.**

1. **Hidden issuer, accountable fraud.** Three mock issuers, a signed policy on Monad, and a BBS presentation proving "one of three." Then simulate a colluding issuer and open the AIH ciphertext with a mock authority to attribute the fraud. One flow demonstrates privacy and accountability.
2. **Policy delegation.** A verifier delegates policy signing to a mock CA, rotates the issuer set without a secret policy key, and rejects a forged policy update.
3. **Anonymity meter.** Shrink the policy from many issuers to one to show what the verifier learns.
4. **Pairing-free path.** Show why a legacy device/SE without pairing support needs the algebraic-MAC variant, and display the extra issuer interaction for the randomized-credential DLEQ.

## Claims to avoid

- "Trustless." It relies on a policy authority, issuer registration, and, for AIH, an opening authority.
- "Fully anonymous." Hiding is within a policy and the opening authority can deanonymize.
- "Post-quantum." BBS and elliptic-curve MACs are not.
- "Constant-size" or "scales to thousands of issuers" unless exact complexity is verified.
- "EUDI-certified." It targets EUDIW; that is not certification.
- "Breaks all issuer-hiding schemes." The claim targets specific constructions assuming honest issuers.
- "The paper reports measured implementation benchmarks." The retrieved version reports complexity counts, not timings.
- "AIH is verified from the full paper." It is a later-revision addition, excerpt-level here.
