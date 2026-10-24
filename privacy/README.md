# Privacy Research

General privacy primitives used by the Zcash and Monad hackathon ideas. These papers focus on anonymous credentials, revocation, issuer hiding, and delegation — the identity layer beneath private agent commerce.

## Papers

| # | File | Paper | Type | Key result | Hackathon use |
|---|---|---|---|---|---|
| 1 | [01-bbs-anonymous-credentials-2025.md](papers/01-bbs-anonymous-credentials-2025.md) | *A Specification of an Anonymous Credential System Using BBS+ Signatures with Privacy-Preserving Revocation and Device Binding* (Graebnitz et al., 2025) | Specification | BBS+ credential + DAA-style hardware binding + LVVC status credential gives selective disclosure, unlinkability, non-duplication, and privacy-preserving revocation | Scoped agent passes, device-bound credentials, shielded status gates |
| 2 | [02-issuer-hiding-bbs-2026.md](papers/02-issuer-hiding-bbs-2026.md) | *Improved Issuer Hiding for BBS-based Anonymous Credentials* (Kaaniche et al., 2026) | Protocol + security model | Prior issuer-hiding models assume honest issuers; malicious-issuer attacks allow credential forgery; new BBS construction with signed policy and accountable issuer hiding | Avoid issuer profiling in agent credentials; accountable fraud attribution |
| 3 | [03-delegation-verifiable-presentations-2025.md](papers/03-delegation-verifiable-presentations-2025.md) | *On Delegation of Verifiable Presentations from mdoc and BBS Credentials* (Flamini et al., 2025) | Formalization + constructions | Defines delegation of verifiable presentations with correctness, unforgeability, and unlinkability; mdoc and BBS instantiations | Human-to-agent and agent-to-sub-agent authority with scope |

## What this folder establishes

- A credential can prove **attributes without identity**.
- Revocation can be **privacy-preserving** if it uses a status credential instead of a public denylist.
- Issuer identity is itself sensitive; **issuer hiding** prevents the verifier from profiling the authority.
- Delegation is not a signature on a document; it is a **scoped proof** that binds the delegatee, the operation, and the verifier.
- None of these papers solves payment settlement or agent runtime security; they are the identity layer.

## Cross-paper synthesis

1. **Selective disclosure + revocation is the minimum for agent commerce.** An agent needs to prove role, scope, and good standing without dumping its history.
2. **Issuer hiding is underrated.** If every merchant learns that the same bank issued the credential, issuer-based profiling replaces user-based profiling.
3. **Delegation must be scoped to the verifier and operation.** A general "agent may act for me" credential is an over-broad mandate.
4. **Accountable anonymity is the practical compromise.** Privacy by default, attribution by exception for fraud.
5. **Access limitation:** the full ePrint PDFs for all three papers were Cloudflare-blocked. The files separate verified abstract/metadata claims from secondary-source and inference-tagged detail. Do not quote unverified equations or benchmarks as the authors' own.

## Ideas that use this folder

- [VeilMandate](../flagship-ideas/01-veilmandate.md) — task-scoped credentials + shielded payments.
- [ShadeScore](../flagship-ideas/06-shadescore.md) — issuer-hidden credit predicates.
- [Sovereign Runtime](../flagship-ideas/04-sovereign-runtime.md) — scoped delegation for agents.
- [Halo Mandate](../flagship-ideas/09-halo-mandate.md) — recursive proof of delegated authority.
