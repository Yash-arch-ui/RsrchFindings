# Audit of Zcash: Orchard (zkSecurity, 2026)

## Metadata

- Auditors: zkSecurity (Tachyon Foundation).
- Window: started 8 June 2026; report 14 July 2026; one week, two consultants.
- URL: https://zksecurity.xyz.
- Scope tags: `orchard` `0.14.0`; Halo2 `halo2_gadgets-0.5.0`, covering `halo2_gadgets`, `halo2_proofs`, and `halo2_poseidon`.
- Length: 198 lines; four findings.
- Type: security audit with manual review, proof-of-concept exploits, an AI scanner (`zkao`), and an MVP Lean/Clean formal verification.
- Read status: full local report text read (`p07.txt`). Findings, severities, and code paths are from that report; Orchard context is from public documentation.

## One-paragraph summary

zkSecurity audited Zcash Orchard and its Halo2 dependencies for one week in June 2026, prioritizing constraint soundness in the Orchard Action circuit and its Halo2 gadgets, plus the Halo2 verifier and Orchard's non-circuit surface. The headline result is a Lean formalization of the full Orchard Action circuit in zkSecurity's Clean framework, with end-to-end soundness and completeness proofs, zero `sorry`s, and one benign axiom establishing the Pallas curve order. Manual review and proofs of concept found no issue affecting deployed Orchard security. The four findings are latent gadget and tooling risks: a Medium empty-message Sinsemilla gap, a Low missing binding between a fixed-base scalar and its windows, a Low PCZT signer-flow gap where ciphertext is not authenticated, and an Informational proof-parsing issue with trailing bytes.

## Problem and threat model

Orchard is Zcash's Halo2-based shielded pool. A malicious prover must not create a valid Action proof for a false statement: a spend without authority, a double spend, a value imbalance, a forged note commitment, or a Merkle path missing the claimed anchor. The audit targets that soundness boundary, including the reusable Halo2 gadgets Orchard composes. It also targets verifier-facing non-circuit code: bundle, action, and public-instance handling; value, nullifier, and note-commitment binding; key, signature, and address parsing; and PCZT verification/extraction.

The PCZT finding is not consensus. A signer may approve a semantically correct note (recipient, value, memo) while signing a transaction whose encrypted output ciphertext is malformed or encrypts different data. A malicious PCZT constructor could exploit this; the recipient may be unable to detect or spend the note, effectively burning funds. The proof-size finding is also non-consensus: direct low-level verifiers may accept non-canonical encodings; higher-level bundle and PCZT paths enforce strict size when configured.

The audit is risk-prioritized, not exhaustive. It excludes Halo2 gadgets Orchard does not use, notably SHA-256, which the report says are known to have serious soundness issues outside scope. Absence of findings is not a guarantee.

## Architecture / protocol

Orchard uses Halo2 over the Pasta cycle (Pallas and Vesta), with no trusted setup. The Action circuit is the unit of proof: one proof per Action, fixed size `K = 11`, and ten public inputs (`anchor`, `cv_net.x`, `cv_net.y`, `nf_old`, `rk.x`, `rk.y`, `cmx`, `enable_spend`, `enable_output`, `disable_cross_address`).

The circuit checks spend authority `[alpha] SpendAuthG + ak = rk`; old note commitment from `g_d`, `pk_d`, `v`, `rho`, `psi`, `rcm`; new commitment `cmx`; nullifier `nf_old = DeriveNullifier_nk(rho_old, psi_old, cm_old)`; a depth-32 Sinsemilla Merkle path to `anchor`; value commitment `cv_net = ValueCommit(v_net, rcv)` with `v_net = v_old - v_new`; and chaining `rho_new = nf_old`. The note commitment is Sinsemilla over

`g*_d || pk*_d || I2LEBSP_64(v) || I2LEBSP_255(rho) || I2LEBSP_255(psi)`,

with point encodings using 255 bits for x and 1 bit for y. `ivk` is a Sinsemilla short commitment to `(ak, nk)`; value commitments are Pedersen. PCZT is the partially created transaction and signer workflow. `Output::verify_note_commitment` is meant to check output note fields against `cmx`; the audit shows it ignores the ciphertext the recipient decrypts.

## Section-by-section findings

Introduction and scope: the audit began 8 June 2026, lasted one week with two consultants, and targeted tail risk in Orchard and Halo2. Four areas: the Action circuit and sub-circuits (NoteCommit, CommitIvk, value commitment, nullifier derivation, Sinsemilla Merkle path); reusable Halo2 gadgets (`halo2_gadgets` elliptic-curve, Sinsemilla, utility; `halo2_poseidon`); the `halo2_proofs` PLONK verifier, transcript, and polynomial-commitment verification; and Orchard's non-circuit surface (bundle, action, public instances, bindings, parsing, PCZT). SHA-256 gadgets were excluded and are known to have serious issues outside scope.

Summary: formal verification is the most significant outcome. Manual review and proofs of concept surfaced no issue affecting deployed Orchard security. Circuit findings concentrate on gadget-usage paths that are dangerous to change. `zkao` flagged `#00` and `#01`.

Formal verification: the Action circuit was formalized in Clean with end-to-end soundness and completeness proofs, covering gadget dependencies. Proofs have zero `sorry`s and one benign axiom for the Pallas curve order. Limitations are below.

Findings: `#00` Medium, empty Sinsemilla output y unconstrained; `#01` Low, fixed-base multiplication does not bind scalar to windows; `#02` Low, PCZT verification does not authenticate ciphertext; `#03` Informational, low-level proof verification accepts trailing bytes.

## Key mechanisms and equations

Halo2 footguns. `Value<F>` is witness-only: unless copied into an assigned, constrained cell, a malicious prover can diverge. Gates constrain only selector-enabled rows, so disabled-row cells are free and an off-by-one can expose them. Config-time cells addressed in synthesis only by offset can be missed. Test-only APIs such as `witness_decompose` are ungated risk.

Finding #00, Medium. `hash_to_point` accepts a `Message` with zero pieces. `Message::from` only bounds the word count above:

`assert!(pieces.iter().map(|piece| piece.num_words()).sum::<usize>() < MAX_WORDS);`

An empty message passes: `hash_all_pieces` never runs, `hash_piece` is never called, no `q_sinsemilla1` row is enabled, and the lookup/transition gates do not run. The returned y is advice in `lambda_1`, constrained only by:

`init_y_q_check: 2*y_Q - Y_A = 0`, with `Y_A = (lambda_1 + lambda_2)*(x_a - x_R)` and `x_R = lambda_1^2 - x_a - x_p`.

`x_a` is fixed to `x_Q` and `y_Q` is fixed, but `lambda_1`, `lambda_2`, and `x_p` are free advice. A prover targets any y with `x_p = y^2 - 2*x_Q + 1` and `lambda_2 = 2*y_Q - y`, giving `x_a - x_R = 1` and `Y_A = 2*y_Q`. The PoC returns `(x_Q, -y_Q) = -Q` from a real `SinsemillaChip` on an empty message, while the spec has `SinsemillaHashToPoint(D, []) = Q`.

Impact: exploitable for consumers permitting empty messages and using the full point; Orchard does not, and x-only `extract` callers are unaffected. Recommendation: bind the empty-case y to `y_Q` via `fixed_y_q` and the permutation machinery (no new gate/column; Orchard's constraint system unchanged), or reject empty messages in `Message::from`, `Message::from_pieces`, or `hash_all_pieces`. File: `sinsemilla/chip/hash_to_point.rs`.

Finding #01, Low. Full-width fixed-base multiplication decomposes a `ScalarFixed` into windows and computes `[windows]*B`, but never constrains windows to equal the witnessed scalar. `ScalarFixed::new` takes a host-side `Value<pallas::Scalar>`; `witness_scalar_fixed` ignores its layouter, assigns nothing, and returns `EccScalarFixed { value, windows: None }`. The only cells are window digits inside `.mul`; they are range/coordinate-checked but never tied to `value`. The enforced relation is existential:

`result = [windows]*B` for prover-chosen windows.

Two multiplications sharing one `ScalarFixed` need not use the same scalar. The PoC reuses one for `P = [alpha]*G` and `Q = [alpha]*H`, a discrete-log-equality (DLEQ) relationship, and accepts `P = [5]*G`, `Q = [11]*H`. Orchard is safe: `alpha` is a private hint and spend authority only needs `[alpha]*SpendAuthG`. This is a latent footgun for DLEQ callers. Recommendation: document the existential contract and bind shared scalars with `copy_decompose` rather than witnessing windows from a `Value`. File: `ecc/chip/mul_fixed/full_width.rs`.

Finding #02, Low. `Output::verify_note_commitment` reconstructs a note from PCZT metadata:

`Note::from_parts(recipient, value, Rho::from_nf_old(spend.nullifier), rseed)`

and checks `ExtractedNoteCommitment::from(note.commitment()) == self.cmx`. It authenticates note fields against the commitment, but never inspects `self.encrypted_note`, i.e. `enc_ciphertext` and `out_ciphertext`. Those bytes come from the PCZT constructor, and `tx_extractor.rs` copies them verbatim into the final action. Documentation delegates the ciphertext check to the signer, but the crate provides no helper.

Impact: not consensus; a signer can approve a correct note while signing an undecryptable or different ciphertext, so the recipient may be unable to detect or spend it, burning funds. Recommendation: provide a helper checking `enc_ciphertext` (and `out_ciphertext` when `ock` is available) against the approved note fields, and wire it into the signer checklist. File: `pczt/verify.rs`.

Finding #03, Informational. `Proof::verify` and `Proof::add_to_batch` feed proof bytes into the Halo2 transcript without checking that it consumes the whole slice:

`let mut transcript = Blake2bRead::init(&self.0[..]);`

then `plonk::verify_proof(...)`. Halo2 reads the expected elements and stops, so trailing bytes are accepted. `Bundle::try_from_parts` calls `validate_proof_size` with `ProofSizeEnforcement::Strict`, and PCZT extraction validates size before an authorized bundle; direct callers or `Unenforced` bundles get no check. Recommendation: reject non-canonical length in the low-level verifier, or document these as transcript-only APIs requiring `Proof::expected_proof_size`. File: `circuit.rs`.

## Experiments / evaluation

The evaluation is the audit process and its artifacts:

- One week, two consultants, starting 8 June 2026.
- Four scoped areas: Action circuit/sub-circuits, reusable Halo2 gadgets, `halo2_proofs` verifier, and Orchard's non-circuit/PCZT surface.
- Four findings: one Medium (`#00`), two Low (`#01`, `#02`), one Informational (`#03`).
- `zkao` flagged `#00` and `#01`.
- Manual proofs of concept demonstrated the empty-Sinsemilla output `-Q`, the desynchronized DLEQ `P = [5]*G`, `Q = [11]*H`, and the PCZT ciphertext gap.
- Formal verification produced end-to-end soundness and completeness proofs for the top-level Action circuit, with zero `sorry`s and one benign axiom for the Pallas curve order.
- Orchard Action circuit parameters: `K = 11`, ten public inputs, depth-32 Sinsemilla Merkle tree.
- The report states no issue affecting deployed Orchard security was found.

Baselines: the Zcash protocol specification, the Orchard circuit, and the Halo2 constraint system. Metrics: constraint soundness, completeness, PoC exploitability, and severity.

## Paper-specific findings

1. The full Orchard Action circuit was formalized in Clean with end-to-end soundness and completeness proofs, zero `sorry`s, and one benign Pallas-order axiom.
2. No finding affects deployed Orchard consensus security. The circuit findings are latent hazards for future changes and other gadget consumers.
3. `#00` is a real soundness gap for empty Sinsemilla messages, but Orchard never hashes one. The exploit returns `-Q` instead of `Q` by choosing `x_p` and `lambda_2` to satisfy the initialization gate.
4. `#01` is a "value, not cell" bug: `ScalarFixed` windows are not bound to the scalar, so the gadget enforces only an existential relation. Orchard is safe because `alpha` is a private hint.
5. `#02` is a signer-flow issue, not consensus. PCZT verification authenticates the note commitment but not `enc_ciphertext`/`out_ciphertext`, so a signer can approve a note the recipient cannot decrypt.
6. `#03` is API hygiene: low-level `Proof::verify` and `add_to_batch` accept trailing bytes; strict size is enforced only by higher-level bundle and PCZT paths.
7. The four Halo2 footguns are general integration hazards: values are not cells, selectors gate constraints, fragile offsets address cells, and test-only APIs expand risk.
8. Formal verification has stated limits (approximate Clean model, no mechanical link to Rust, hand/AI translation, incomplete lower-level review), and the recommendations are both code and documentation changes.

## Limitations and caveats

- One week, two consultants, large scope: prioritized, not exhaustive. Absence of findings is not proof of absence.
- The formal verification is an MVP. Clean's raw constraints and linear witness tape are more primitive than Halo2 regions, custom gates, and row-column layout; the port is approximate.
- The formalization is not mechanically connected to Rust. Translation was hand-done with AI assistance and source-conformance instructions, but no waterproof mechanism enforces conformance; the auditors could have formalized different circuits.
- Not all specs/interfaces were human-written or reviewed. The top-level Action spec was reviewed and co-authored, but lower-level gaps not exercised by the Action circuit may remain.
- The empty-Sinsemilla case shows the gap: the Clean port requires an input vector of length `n + 1`, while Orchard only uses non-empty inputs. That is not a proof about the empty case.
- SHA-256 gadgets were excluded and are known to have serious soundness issues outside scope. Do not infer anything about them here.
- "No issue affecting deployed Orchard" is narrower than "Orchard is proven secure." It excludes consensus outside scope, network privacy, side channels, wallet key management, and SHA-256.
- PCZT depends on signer/wallet integration; it is not a consensus bug. Trailing bytes depend on the API called; higher-level `ProofSizeEnforcement::Strict` paths are unaffected.

## Hackathon hooks

What this enables: a concrete signer-security project. Build the missing PCZT ciphertext-authentication helper, wire it into a signer checklist, and demo a malicious PCZT that approves the right note commitment while carrying a wrong `enc_ciphertext`; the hardened signer rejects it. Add strict proof-length enforcement or a `Proof::expected_proof_size` guard to direct verification. Both are small, testable, and traceable to `#02` and `#03`.

It also enables a Halo2 gadget regression harness: empty Sinsemilla messages are rejected or return `Q`, a reused `ScalarFixed` cannot desynchronize a DLEQ, and every selector-enabled row is constrained. A harder direction is spec-to-circuit conformance: differential testing or translation validation comparing a small Rust sub-circuit's constraints against a Clean model, since the audit's proofs are not mechanically linked to Rust.

This does not show Orchard is broken, give a consensus exploit, make SHA-256 gadgets safe, or replace a full audit of a modified circuit. It does not prove Rust matches the formal model, and it does not authorize changing Orchard's constraint system without re-running soundness checks.

Demo ideas:
- "PCZT Guard": a signer UI that shows approved note fields, recomputes the commitment, validates `enc_ciphertext` and `out_ciphertext`, and refuses mismatches; include a red-team mode with swapped ciphertext.
- "Gadget Footgun Lab": a Halo2 test suite reproducing the empty-Sinsemilla `-Q` result, the `ScalarFixed` DLEQ desynchronization, and a missing-selector bug, then showing fixes and regression tests.
- "Canonical Proof Gate": a verifier wrapper that rejects trailing bytes and a matrix showing which Orchard/Halo2 APIs enforce size and do not.

## Claims to avoid

- "The audit found a critical Orchard vulnerability." No issue affects deployed Orchard security; the top severity is Medium on a gadget path Orchard does not use with empty messages.
- "Orchard is broken." Formal verification supports circuit soundness, and no deployed-protocol issue was found.
- "The empty-Sinsemilla bug affects Zcash users." Orchard does not hash empty Sinsemilla messages.
- "The PCZT ciphertext issue is a consensus bug." It is a signer/wallet flow issue; consensus does not require decryptable ciphertext.
- "Trailing proof bytes let an attacker forge proofs." They are accepted as non-canonical encoding; they do not create a false statement.
- "The Clean proof mechanically verifies the Rust code." It does not; the port is approximate and hand/AI-assisted.
- "All Halo2 gadgets were audited." SHA-256 gadgets were excluded and are known to have serious issues.
- "The audit covers the whole Zcash protocol." It covers scoped Orchard/Halo2 code, not network privacy, side channels, or wallet key management.
- "Findings #01 and #02 are exploitable in Orchard today." The audited code uses the gadgets correctly; these are latent or signer-flow hazards.
