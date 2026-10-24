# Source Map — Zcash + Monad Research Set

The user supplied 16 unique sources (17 links, with Paper 12 and Paper 13 being the same arXiv ID, and Paper 8 containing two ePrint links). This map records where each source is analyzed and the access status.

| User # | Source | Type | Analysis file | Access status |
|---|---|---|---|---|
| 1 | [MinMandate (ePrint 2026/1674)](https://eprint.iacr.org/2026/1674) | Protocol + evaluation | [privacy-x-agentic-ecommerce/papers/01-minmandate-2026.md](privacy-x-agentic-ecommerce/papers/01-minmandate-2026.md) | ePrint PDF blocked; full preprint retrieved from the public GitHub repo and read |
| 2 | [AESP (arXiv 2603.00318)](https://arxiv.org/pdf/2603.00318) | Protocol + SDK | [privacy-x-agentic-ecommerce/papers/02-aesp-2026.md](privacy-x-agentic-ecommerce/papers/02-aesp-2026.md) | Full PDF read |
| 3 | [Secure Autonomous Agent Payments (arXiv 2511.15712)](https://arxiv.org/pdf/2511.15712) | Conceptual framework | [privacy-x-agentic-ecommerce/papers/03-secure-autonomous-agent-payments-2025.md](privacy-x-agentic-ecommerce/papers/03-secure-autonomous-agent-payments-2025.md) | Full PDF read |
| 4 | [Cloaked MCP Server](https://glama.ai/mcp/servers/CloakedAgent/cloaked) | Vendor product page | [privacy-x-agentic-ecommerce/papers/04-cloaked-mcp-2026.md](privacy-x-agentic-ecommerce/papers/04-cloaked-mcp-2026.md) | Full page read; vendor claims only, no audit |
| 5 | [Halo (ePrint 2019/1021)](https://eprint.iacr.org/2019/1021) | Construction + implementation | [zcash/papers/01-halo-recursive-proofs-2019.md](zcash/papers/01-halo-recursive-proofs-2019.md) | Full PDF read via Internet Archive copy of the 2020-02-18 ePrint revision |
| 6 | [On the linkability of Zcash transactions (arXiv 1712.01210)](https://arxiv.org/pdf/1712.01210) | Empirical measurement | [zcash/papers/02-zcash-linkability-2017.md](zcash/papers/02-zcash-linkability-2017.md) | Full PDF read |
| 7 | [Audit of Zcash: Orchard (zkSecurity, 2026)](https://reports.zksecurity.xyz/reports/zcash-orchard/) | Security audit + formal verification | [zcash/papers/03-orchard-audit-2026.md](zcash/papers/03-orchard-audit-2026.md) | Full report text read |
| 8a | [BBS+ AC spec (ePrint 2025/824)](https://eprint.iacr.org/2025/824) | Specification | [privacy/papers/01-bbs-anonymous-credentials-2025.md](privacy/papers/01-bbs-anonymous-credentials-2025.md) | Full PDF read via Internet Archive copy of the 2025-05-02 ePrint revision |
| 8b | [Improved Issuer Hiding (ePrint 2026/555)](https://eprint.iacr.org/2026/555) | Protocol + security model | [privacy/papers/02-issuer-hiding-bbs-2026.md](privacy/papers/02-issuer-hiding-bbs-2026.md) | Full PDF read via Internet Archive copy of the 2026-03-20 revision; current 2026-07-03 revision and AIH are excerpt-level |
| 9 | [Delegation of Verifiable Presentations (ePrint 2025/2306)](https://eprint.iacr.org/2025/2306) | Formalization + constructions | [privacy/papers/03-delegation-verifiable-presentations-2025.md](privacy/papers/03-delegation-verifiable-presentations-2025.md) | ePrint PDF blocked; full CEUR workshop version + ePrint excerpts |
| 10 | [SoK: Security of Autonomous LLM Agents (arXiv 2604.15367)](https://arxiv.org/pdf/2604.15367) | SoK | [privacy-x-agentic-ecommerce/papers/05-sok-security-autonomous-llm-agents-2026.md](privacy-x-agentic-ecommerce/papers/05-sok-security-autonomous-llm-agents-2026.md) | Full PDF read |
| 11 | [SoK: Blockchain Agent-to-Agent Payments (arXiv 2604.03733)](https://arxiv.org/pdf/2604.03733) | SoK | [privacy-x-agentic-ecommerce/papers/06-sok-blockchain-agent-to-agent-payments-2026.md](privacy-x-agentic-ecommerce/papers/06-sok-blockchain-agent-to-agent-payments-2026.md) | Full PDF read |
| 12 | [Beyond the Mandate: AP2 (arXiv 2608.23858)](https://arxiv.org/pdf/2608.23858) | Threat model + PoC | [privacy-x-agentic-ecommerce/papers/07-ap2-security-analysis-2026.md](privacy-x-agentic-ecommerce/papers/07-ap2-security-analysis-2026.md) | Full PDF read |
| 13 | Same as 12 | Duplicate | Same as 12 | Duplicate; analyzed once |
| 14 | [Can LLMs Keep a Secret? (arXiv 2310.17884)](https://arxiv.org/pdf/2310.17884) | Benchmark | [privacy-x-agentic-ecommerce/papers/08-can-llms-keep-a-secret-2023.md](privacy-x-agentic-ecommerce/papers/08-can-llms-keep-a-secret-2023.md) | Full PDF read |
| 15 | [Privacy in Action (arXiv 2509.17488)](https://arxiv.org/pdf/2509.17488) | Mitigation + benchmark | [privacy-x-agentic-ecommerce/papers/09-privacy-in-action-2025.md](privacy-x-agentic-ecommerce/papers/09-privacy-in-action-2025.md) | Full PDF read |
| 16 | [MAGPIE (arXiv 2506.20737)](https://arxiv.org/pdf/2506.20737) | Benchmark | [privacy-x-agentic-ecommerce/papers/10-magpie-2025.md](privacy-x-agentic-ecommerce/papers/10-magpie-2025.md) | Full PDF read |

## Access limitation

`eprint.iacr.org` serves live PDFs behind a Cloudflare challenge for this environment. Where an Internet Archive snapshot of the PDF existed, the full paper was retrieved and read from that copy: Halo, the BBS+ AC specification, and the 2026-03-20 revision of Improved Issuer Hiding. Their notes are now full-text based.

Two sources remain partly access-limited:

- **Zhang, Wang, Zhang (SSRN 6185958).** The 59-page PDF is still blocked after attempts via SSRN, the Internet Archive, ResearchGate, and the author's site. The note keeps a clearly marked reconstruction. Do not cite it as the authors' model.
- **Delegation ePrint extension (2025/2306).** The ePrint PDF is blocked, but the full CEUR workshop version was read. BBS-specific ePrint additions remain excerpt-level and are tagged.

## What is fully verified

- Full arxiv PDFs: AESP, Secure Agent Payments, the two SoKs, AP2, ConfAIde, PrivacyChecker, MAGPIE.
- Full Zcash report: Orchard audit.
- Full Zcash paper: linkability.
- Full Halo paper via Internet Archive (2020-02-18 revision).
- Full BBS+ AC specification via Internet Archive (2025-05-02 revision).
- Full Improved Issuer Hiding v1 via Internet Archive (2026-03-20 revision); the 2026-07-03 revision is not.
- Full local page: Cloaked.
- Full MinMandate preprint via GitHub.
- Full CEUR workshop version of the delegation paper.

## What is abstract-level or partially verified

- Zhang, Wang, Zhang (SSRN 6185958): abstract/metadata plus a marked reconstruction.
- Delegation ePrint extension: the CEUR workshop version is full; the ePrint-only BBS additions are excerpt-level.
- Improved Issuer Hiding 2026-07-03 revision and its Accountable Issuer Hiding section: excerpt-level.
