# Halo: Recursive Proof Composition without a Trusted Setup (2019)

## Metadata

- Authors: Sean Bowe, Jack Grigg, Daira Hopwood (Electric Coin Company).
- Date: ePrint received 2019-09-11; last revision 2020-02-18.
- Venue: Cryptology ePrint Archive, Paper 2019/1021; preprint; CC BY.
- URL: https://eprint.iacr.org/2019/1021.
- Length: 20 pages in the public ePrint revision (not verified locally).
- Type: construction plus implementation/evaluation paper; not a formally verified security proof of the demonstration protocol.
- Read status: local metadata/abstract read. The full PDF is Cloudflare-blocked. Details marked [web] are cross-checked against public ePrint excerpts, the Halo2 book, and Electric Coin Company posts; the local source establishes authorship, title, abstract, venue, and dates only.

## One-paragraph summary

Halo is the first practical construction of recursive proof composition without a trusted setup. Earlier recursive SNARKs needed pairing-friendly curves and a structured reference string from a trusted ceremony. Halo instead uses a discrete-log-based polynomial commitment built from the inner-product argument, over an ordinary two-cycle of 255-bit prime-order curves (Tweedledum and Tweedledee in the paper). Its central idea is nested amortization: the recursive verification circuit does not perform the expensive linear-time multiscalar multiplication needed to check an inner proof. It carries the claimed input/output of that operation as public state, verifies the logarithmic-time part inside the circuit, and defers the linear-time part to the next layer. At the end, one linear-time check decides the whole batch. Proof size and verification time therefore do not grow with recursion depth, although verifier work remains linear in circuit size. Halo is recursive but not fully succinct, and it is the conceptual ancestor of Halo2, the proving system used by Zcash Orchard.

## Problem and threat model

The problem is incrementally verifiable computation (IVC) and proof-carrying data: a prover wants a short proof that a long computation was executed correctly, where each step also verifies the previous proof. The natural construction is to express the proof system's verifier as a circuit and recurse. That requires the verifier circuit to be smaller than the circuit it checks, and it requires arithmetic in the verifier's field to be efficient.

The threat model is a malicious prover who may choose witnesses, commitments, and recursion state adversarially, plus a verifier who must not be convinced of a false statement. Halo's soundness ultimately rests on the discrete logarithm assumption in prime-order groups, in the random-oracle model after Fiat-Shamir, and on a transparent setup whose group elements are hash-derived in the uniform-random-string model [web]. There is no toxic waste and no per-circuit ceremony. The paper explicitly says it does not make formal security claims for its demonstration protocol; it aims at a plausibly knowledge-sound construction and at showing practicality [web]. That caveat matters for any hackathon claim.

"Trusted setup" means a structured reference string with a secret trapdoor that, if retained, breaks soundness. Halo removes that, but not all trust: implementations still trust curve parameters, hash instantiations, randomness, and code correctness.

## Architecture / protocol

The architecture has four moving parts.

1. A polynomial commitment scheme. The prover commits to a polynomial by a Pedersen vector commitment to its coefficients, then proves an evaluation using a modified inner-product argument. The commitment is binding under discrete log and additively homomorphic, which lets the verifier combine commitments and defer openings.
2. An arithmetic-circuit argument. The paper instantiates a polynomial IOP derived from Sonic and replaces its pairing-based commitment with the discrete-log commitment above [web]. The prover sends witness-polynomial commitments, the verifier samples challenges, and a quotient/vanishing argument reduces circuit satisfaction to polynomial identities.
3. Amortization of inner-product verification. Checking each inner-product argument requires a linear-size multiexponentiation. An untrusted helper supplies the claimed result, and a new argument shows that all supplied results are correct. The verifier still performs one linear-time operation, but once per batch rather than once per proof.
4. Recursion by nested amortization. The recursive verification circuit treats the expensive multiexponentiation as an unverified public input-output pair `(x, y)`. It checks the logarithmic-time portion and passes the deferred claim forward. The accumulator state `st` contains challenges and claimed group elements; the terminal verifier performs one deferred linear-time check and accepts or rejects the whole chain.

The implementation maintains `st`, performs a logarithmic-time update per proof, and finally does a linear-time check over `st` [web]. Proof size and verification time stay flat with recursion depth because each layer verifies a bounded update, not a growing computation. Total verifier work is still linear in the circuit size, so Halo is not fully succinct.

The two-cycle is essential. A proof system over `E_p/F_p` with group order `q` works natively over `F_q`, while group operations needed to verify proofs use `F_p`. A second curve `E_q/F_q` with order `p` lets each curve efficiently verify proofs about the other. The paper searches for a cycle with high 2-adicity, cube roots of unity for endomorphisms, and Rescue exponent 5 [web]. The result is:

- `E_p/F_p: y^2 = x^3 + 5` of order `q` (Tweedledum)
- `E_q/F_q: y^2 = x^3 + 5` of order `p` (Tweedledee)
- `p = 2^254 + 4707489545178046908921067385359695873`
- `q = 2^254 + 4707489544292117082687961190295928833`

Both are 255-bit primes with 126-bit security against Pollard rho after accounting for the order-6 automorphism [web]. The "128-bit security level" language is aspirational.

Halo2, the production successor, keeps the discrete-log inner-product commitment, transparent setup, and accumulation ideas, but changes:

- Arithmetization: PLONKish columns, rows, selectors, custom gates, permutation arguments, and lookup arguments, instead of Halo's Sonic-derived polynomial IOP. This makes range checks, Sinsemilla, and Poseidon gadgets practical.
- Curves: Pasta (Pallas and Vesta) instead of Tweedledum/Tweedledee. Pallas is `y^2 = x^3 + 5` over `GF(0x40000000000000000000000000000000224698fc094cf91b992d30ed00000001)`; Vesta is the same equation over `GF(0x40000000000000000000000000000000224698fc0994a8dd8c46eb2100000001)`. Both are 255-bit, have 2-adicity 32, form a cycle, and have 32-byte compressed points. Orchard instantiates Halo2 over Vesta to prove statements about Pallas arithmetic [web].
- Specification: Halo2 is documented in the Halo2 book and code, not in a peer-reviewed Halo2 paper at the time of writing. The book says its protocol description will likely be replaced by a Halo2 paper and security proof [web]. Treat Halo2 security claims as implementation-and-audit claims, not a published theorem.

## Section-by-section findings

Section 1, introduction and contributions: Halo is framed as the first practical recursive composition without a trusted setup. The claimed advantages are no ceremony, ordinary 255-bit curves, proof size and verification time independent of recursion depth, and a recursion threshold below `2^17` multiplication gates. The paper acknowledges that the verifier is linear-time and that it is not fully succinct.

Section 2, preliminaries: defines groups, the discrete-log relation, polynomial commitments, arguments of knowledge, and security notions. Security is argued in the algebraic group model and random-oracle model after Fiat-Shamir, with a uniform-random-string setup.

Section 3, polynomial commitments with amortized succinctness: introduces the Pedersen/inner-product commitment and the amortization observation. The verifier's linear-time opening check is the bottleneck; a helper supplies its output, and a batched argument checks all outputs with one linear operation. This is the technical heart of the paper.

Section 4, nested amortization: explains how to keep the recursive verification circuit small by delegating expensive operations to the terminal verifier. The circuit takes `(x, y)` as public inputs, assumes `y = f(x)`, and accumulates such claims.

Section 5, protocol: specifies the interactive argument for arithmetic-circuit satisfiability, including commitment rounds, challenges, quotient polynomial split, multipoint opening, and the final inner-product argument. Fiat-Shamir makes it non-interactive; Rescue with a duplex sponge is used in the implementation [web].

Section 6, implementation: covers the curve cycle, endomorphism optimizations, challenge sampling as squares to avoid extra inversions in-circuit, and benchmarks. The endomorphism algorithm uses about 3.5 multiplication constraints per bit of a 128-bit challenge [web].

Section 7, conclusion: restates nested amortization, the 3.5 KiB proof size, the asymptotic improvement over Bulletproofs, and the "helped" Sonic connection without pairings or a trusted setup.

## Key mechanisms and equations

Polynomial commitment. With setup `sigma = (G, bold G, H, F_p)`, `bold G in G^d`, `H in G`, and polynomial `p(X)` of degree at most `d-1` with coefficient vector `a`:

`Commit(sigma, p(X); r) = <a, bold G> + [r] H`.

The opening relation is:

`{ ((P, x, v); (a, r)) : P = <a, bold G> + [r] H, v = <a, b> }`

where `b = (1, x, x^2, ..., x^{d-1})`. The inner-product argument runs `k = log2(d)` rounds. In each round the prover sends cross terms of the form

`L_j = <a_lo, G_hi> + [l_j] H + [<a_lo, b_hi>] U`
`R_j = <a_hi, G_lo> + [r_j] H + [<a_hi, b_lo>] U`

and the verifier sends a challenge `u_j`; the vectors are compressed by half each round. The final check in the Halo2 presentation is:

`sum_j [u_j^{-1}] L_j + P' + sum_j [u_j] R_j = [c] G'_0 + [c b_0 z] U + [f] W`.

The Halo paper uses the same idea with its own notation; do not present the Halo2 equation as verbatim Halo text without checking the original.

Accumulator polynomial. The deferred linear-time check is a commitment to a polynomial determined by the round challenges:

`G = Commit(sigma, g(X, u_1, ..., u_k))`

where `g` has degree `2^k - 1`; the coefficient vector `s` of `g` is the binary challenge-combination vector, and `G'_0 = <s, bold G>`. The verifier circuit witnesses `G` and the challenges, checks the logarithmic-time relation, and emits a fresh deferred claim `G'`. Only the final verifier computes the full multiexponentiation.

Endomorphism optimization. For `phi(x, y) = (zeta_p x, y)` with `phi(P) = [zeta_q] P`, Algorithm 1 multiplies a point by a challenge using a 3.5-constraints-per-bit double-and-add variant [web]. This reduces the dominant in-circuit cost of recursive verification.

## Experiments / evaluation

The paper reports a full implementation on a 16-core Intel i9-7960X at 2.80 GHz using 16 threads [web]. Fully recursive proofs are at least 3.5 KiB. Recursion becomes possible at a crossover just below `2^17` multiplication gates. Proof size and verification time do not increase with recursion depth. For comparison, the paper cites Fractal proofs above 120 KiB at the 128-bit security level.

The evaluation is a feasibility demonstration, not a production benchmark suite. It does not establish worst-case prover memory, side-channel resistance, or end-to-end Zcash block verification. The concrete security estimate is 126 bits against Pollard rho, not 128.

## Paper-specific findings

1. Recursive composition without a pairing-based trusted setup is practical, not merely theoretical. The key is amortizing the inner-product verifier rather than making the whole verifier succinct.
2. Nested amortization converts a linear-time obstacle into a deferred public claim. The circuit stays bounded; the terminal verifier pays one linear cost for the chain.
3. Proof size and verification time are independent of recursion depth, but the verifier is linear in circuit size, so Halo is recursive and not fully succinct.
4. A two-cycle of ordinary curves is sufficient. Tweedledum and Tweedledee provide high 2-adicity, endomorphisms, and Rescue compatibility.
5. The helper is untrusted for soundness: its claimed linear-operation outputs are checked by the batched argument, so it can affect performance but not validity.
6. Endomorphism-based scalar multiplication costs about 3.5 constraints per challenge bit and materially lowers the recursion threshold.
7. The setup is transparent: group elements are hash-derived, with no toxic waste.
8. The paper deliberately avoids formal security claims for its demonstration protocol. It offers plausibility and an implementation, not a complete theorem.
9. The concrete security target is 126-bit Pollard-rho security, even though the text often says "128-bit security level."
10. Halo2 is a substantial redesign, not a patch. It keeps the commitment and accumulation but switches to PLONKish arithmetization, lookups, and Pasta; its production case rests on code review and audits.

## Limitations and caveats

- The local source is metadata and abstract only. Section-level and evaluation details are from public excerpts marked [web]; recheck them against the PDF before quoting exact text.
- Halo is not fully succinct. The verifier's work is linear in circuit size; "constant-time verification for arbitrary computation" is wrong.
- The paper does not prove its demonstration protocol secure. Later work formalized accumulation schemes (ePrint 2020/499), but that is not a proof of this code.
- The paper uses Tweedledum/Tweedledee, not Pasta. Pasta belongs to Halo2 and Zcash Orchard.
- The 3.5 KiB and `2^17` figures are for its own circuit family and hardware, not Orchard proof sizes or Zcash block-validation times.
- "No trusted setup" means no toxic-waste ceremony, not no public parameters, no hash assumptions, or no implementation trust.
- Halo recursion is proof composition over a chain, not a rollup, bridge, or consensus protocol by itself.

## Hackathon hooks

What this enables: transparent-setup recursive proofs for proof-carrying data, incremental computation, agent audit trails, and compressed verification of long histories. A team can build a Halo2 or Ragu prototype that proves a sequence of policy checks or state transitions and accumulates them without a ceremony. Ragu is a Rust PCD framework implementing a modified Halo construction, compatible with Pasta; its README says it is under heavy development and unaudited, so use it for a demo, not production custody [web].

What this does not enable: Orchard recursion at consensus level. Orchard uses Halo2 proofs for individual Actions; recursive composition is a separate track. It does not give post-quantum security, make a linear-time verifier constant-time, or make an unaudited library safe.

Demo ideas:
- A "proof-carrying agent mandate": each agent action is a Halo2 statement over Pasta, and a recursive accumulator proves the whole history satisfies a spending and policy invariant. Show flat proof size as history grows.
- A Halo2 gadget soundness range: reproduce the empty-Sinsemilla and fixed-base scalar-binding footguns in a toy circuit, then show the fixes and a regression test.
- A dashboard comparing a Groth16/Sapling-style trusted-setup path with a Halo2/Pasta path on setup assumptions, proof size, verifier work, and recursion depth, using measured local numbers.

## Claims to avoid

- "Halo uses Pasta." It uses Tweedledum/Tweedledee; Halo2 and Orchard use Pasta.
- "Halo has 128-bit security." The concrete Pollard-rho estimate in the paper is 126 bits.
- "Halo is fully succinct." Its verifier is linear in circuit size.
- "Halo has a formal security proof." The paper disclaims formal security claims for its demonstration protocol.
- "Zcash Orchard recursively verifies its proofs." Orchard uses Halo2 per Action; recursion is not the consensus verification path.
- "No setup at all." It has a transparent setup with public parameters and hash-derived group elements, not a trusted ceremony.
- "Ragu is production-ready." Its own README says heavy development and no audit.
