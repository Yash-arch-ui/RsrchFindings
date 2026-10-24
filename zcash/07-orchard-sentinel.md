# Orchard Sentinel — Verifiable Circuit CI for Zcash

## Metadata

- **Target hackathons:** Zcash (primary), ETHGlobal (security tooling)
- **Tracks:** Zcash, security, formal verification, developer tooling, ZK
- **Papers used:** Orchard audit (zkSecurity, 2026), Halo recursive proofs (2019/1021), Zcash linkability (1712.01210)
- **One-liner:** A CI pipeline that proves your Halo2 circuit matches its specification, and catches the gadget footguns that the Orchard audit exposed.

## The problem

The 2026 Orchard audit delivered a paradoxical result: no live consensus bug, but four latent hazards that are easy to reintroduce:

1. **Empty Sinsemilla messages** leave the output y-coordinate unconstrained. Orchard never hashes an empty message, so it is safe. Another consumer could be exploitable.
2. **Full-width fixed-base multiplication** does not bind the scalar to its window decomposition. Orchard uses it correctly, but a DLEQ-style caller could be broken.
3. **PCZT output verification** authenticates the note commitment but not the ciphertext. A signer can approve a note the recipient cannot decrypt, burning funds.
4. **Low-level proof verification** accepts trailing bytes. Canonical size is enforced only at higher layers.

The deeper problem is process. The audit's formal verification was an MVP: circuits were ported to Lean by hand with AI assistance, and there is **no mechanical link** between the Lean model and the Rust source. The auditors say they could have formalized different circuits than Orchard actually uses. The empty-Sinsemilla finding is concrete evidence: the Lean port requires a non-empty input vector, while the Rust gadget accepts an empty one. The proof is about a different circuit.

Every Zcash-adjacent team that writes a Halo2 circuit inherits these footguns. There is no standard CI that checks them.

## The solution

Orchard Sentinel is a **proof-carrying circuit CI** for Halo2 and Orchard-style circuits.

1. **Spec extraction.** Parse the circuit's Rust/Halo2 source and extract the constraint system: gates, selectors, columns, regions, assignments, and public inputs. Produce a normalized IR.
2. **Clean model generation.** Translate the IR into zkSecurity's Clean/Lean model, with a source map from every Lean constraint back to the Rust line. Unlike the audit's hand port, the translation is mechanical and versioned.
3. **Differential proof.** Prove soundness/completeness for the extracted circuit, and separately prove that the IR matches the source via a translation-validation check. If the source changes, the proof obligation changes.
4. **Footgun test suite.** Run targeted tests:
   - empty Sinsemilla message returns `Q`, not `-Q`;
   - a reused `ScalarFixed` cannot produce two different scalars (DLEQ);
   - every selector that writes a result cell is enabled;
   - no cell is addressed by a fragile hard-coded offset without a copy constraint;
   - `Proof::verify` rejects trailing bytes;
   - PCZT verification authenticates `enc_ciphertext` and `out_ciphertext`.
5. **AI-assisted fuzzing.** Use `zkao`-style scanning plus property-based tests to generate adversarial witnesses: empty messages, zero scalars, max-width decompositions, malformed proofs, mismatched ciphertexts.
6. **PCZT signer guard.** A library that wraps the PCZT verify/extract flow and refuses to sign unless the ciphertext authenticates the approved note and the proof size is canonical.
7. **Report and badge.** Produce a machine-readable certificate: circuit hash, spec version, proofs, tests, and known gaps. A Zcash wallet or protocol can pin the certificate.

## Why this is out of this world

The Orchard audit is one of the first serious attempts to formally verify a production Zcash circuit. Its biggest weakness is the gap between the proof and the Rust code. Orchard Sentinel turns that weakness into a product: **a proof that the proof is about the code you ship**.

The demo moment: introduce a one-line change to a Halo2 gadget. Sentinel's CI fails, shows the exact constraint that lost its binding, and produces a counterexample witness. Then revert the change and watch the proof pass.

## Once-a-decade primitive

Orchard Sentinel issues **translation-validation certificates for circuits**. CI proves not merely that an abstract Clean/Lean model is sound, but that the model was mechanically extracted from the exact Rust/Halo2 constraint system shipped in the commit. The certificate pins the source hash, normalized IR, proof artifact, test corpus, and known exclusions; wallets and protocols can reject uncertified circuit builds.

**Impossible demo:** change one selector or permit an empty Sinsemilla message. Sentinel points to the exact source line, produces a counterexample witness, and invalidates the certificate. Revert the line and the proof becomes valid again. It is a proof that the proof is about the code actually shipped.

The moat is a canonical Orchard/Halo2 regression corpus plus source maps and machine-verifiable certificates that become an ecosystem release requirement.

## Sources and what each contributes

| Source | Contribution |
|---|---|
| Orchard audit | Four findings, Halo2 footguns, Clean formal verification, limitations of the hand port |
| Halo paper | Recursive proof composition, transparent setup, Pasta/Halo2 lineage |
| Zcash linkability | Why wallet/privacy tooling needs rigorous circuit and boundary analysis |

## Intuitive workflow

1. **A Zcash team writes a Halo2 circuit.** They push a commit.
2. **Sentinel extracts the constraint system.** It maps every gate and selector to the source.
3. **Sentinel generates a Lean model.** The model is mechanically linked to the extracted IR.
4. **Sentinel runs the proof.** Soundness and completeness pass for the current circuit.
5. **Sentinel runs the footgun tests.** Empty Sinsemilla, DLEQ scalar reuse, selector gaps, trailing bytes, PCZT ciphertext.
6. **A developer changes a gadget.** The proof obligation changes; the CI fails with a counterexample.
7. **The team fixes it.** The proof and tests pass; a certificate is issued.
8. **A wallet integrates the PCZT guard.** It refuses to sign a note with a mismatched ciphertext.

## Demo (5 minutes)

**Act 1 — The footgun.** Show the empty-Sinsemilla exploit returning `-Q` instead of `Q`. Prove it with a witness.

**Act 2 — The formal gap.** Show the audit's Lean model requiring non-empty input while the Rust gadget accepts empty. The proof is about a different circuit.

**Act 3 — Sentinel.** Extract, translate, prove, and link. The proof now covers the Rust source.

**Act 4 — The regression.** Change one selector; Sentinel fails with the exact line and a counterexample.

**Act 5 — The signer guard.** A malicious PCZT supplies a valid note commitment but an undecryptable ciphertext. The guard refuses to sign.

**Close:** "The proof is only as good as its link to the code."

## Hackathon fit

- **Zcash:** directly improves the security of Zcash wallet and circuit development.
- **ETHGlobal:** security tooling track; strong technical depth.
- **Judging:** real audit findings, formal methods, and a live counterexample.
- **Adoption:** Zcash wallets, Halo2 projects, Orchard forks, and ZK auditors.

## Cons and risks

- **Translation is hard.** Mechanical Rust-to-Clean translation is a research project; an approximate translation weakens the guarantee.
- **Halo2 diversity.** Circuits use different gadgets, custom gates, and layouts; a generic extractor is difficult.
- **Proof engineering.** Lean/Clean proofs require expertise; the tool may only cover a subset.
- **False confidence.** A certificate can be over-read as "fully verified" when it covers only the extracted model.
- **Compiler trust moves upward.** The extractor, IR normalizer, and translation validator become security-critical; a bug there can certify the wrong circuit.
- **Maintenance.** Zcash/Halo2 APIs change; the extractor and proofs must track them.
- **No production audit.** The tool itself needs review.

## Judge Q&A

**"Isn't this just the Orchard audit?"** The audit is a one-time manual engagement. Sentinel is a repeatable CI that mechanically links the proof to the source and catches regressions.

**"Can you really translate Rust to Lean automatically?"** For a subset of Halo2 circuits, yes; for the rest, the tool produces a proof obligation and a source map, with explicit gaps. We do not claim full automation.

**"Why is this a hackathon project?"** The footgun tests and PCZT guard are concrete, demoable, and immediately useful even if the full formal pipeline is incomplete.

**"What about SHA-256 gadgets?"** They were out of scope for the Orchard audit and are known to have issues. Sentinel can include them as a separate test pack.

## Claims to avoid

- "Fully verified Zcash."
- "No bugs."
- "Automatic Rust-to-Lean for all circuits."
- "Replaces an audit."
- "Production-ready."

## One-sentence pitch

**Orchard Sentinel is a proof-carrying CI for Halo2 circuits that mechanically links formal proofs to the Rust source and catches the exact gadget footguns the Orchard audit exposed.**
