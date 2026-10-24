# DashX Corridor — Programmable Stablecoin Settlement for Monad and ETH

## Metadata

- **Idea:** DashX Corridor, a programmable, compliance-aware stablecoin-to-local-rails settlement layer.
- **Origin:** a Solana-hackathon stablecoin-payments idea, rebuilt for Monad and Ethereum only.
- **Traction (team-reported):** $400K+/month processing, doubling month-over-month; India corridor; EU and UAE next.
- **Existing DashX surface:** cross-border payment infrastructure for freelancers, exporters, and businesses; fiat and stablecoin acceptance; INR settlement; virtual accounts; invoicing; KYC/KYB; FIRC/FIRA; 55+ countries; real-time payouts in 17 markets; stablecoin support (USDC/USDT).
- **Hackathon targets:** Monad (Consumer Products and Payments; Onchain Finance and Trading; Trust, Identity, and AI Infrastructure) and Ethereum/ETHGlobal (payments, DeFi, RWA, agentic commerce).
- **Not targeted:** Zcash. This product needs EVM execution, stablecoins, and licensed local rails; Zcash is not the right settlement surface.
- **Read status:** public research through October 2026. Traction and fee claims from DashX are labelled as team-reported; regulatory statements are product-design constraints, not legal advice.

## One-paragraph thesis

Cross-border payments are not a stablecoin problem. They are a **corridor orchestration** problem: compliance, FX, liquidity, last-mile payout, reconciliation, refunds, and the legal boundary between the stablecoin leg and the local fiat leg. DashX already moves money and has real corridor traction. The hackathon-worthy extension is to make the corridor **programmable**: a business or AI agent submits a payout intent, a Corridor Router selects the compliant rail and banking partner, USDC is escrowed on Monad or Ethereum, local currency is paid out over UPI/IMPS or a local rail, and a **Proof of Payout** verifies delivery without exposing recipient PII. The product is not another remittance app. It is the settlement and compliance layer that lets any platform, ERP, or agent pay into local rails with one API.

## The USP: a corridor clearinghouse, not another payout rail

Stablecoin transit alone does not make cross-border payments cheaper. Banca d'Italia's 2026 mystery-shopping study and BIS research find that stablecoin remittances often resemble correspondent banking and FX netting; the savings come from **netting, liquidity efficiency, and compliance reuse**, not from the token itself. DashX's differentiator is to capture those savings and make them verifiable.

Three primitives:

1. **Multilateral netting.** DashX can assemble both sides of the India corridor: exporters and freelancers receiving USD, and businesses paying INR. That two-sided book is a netting pool; where the book is one-sided, DashX uses a liquidity hub. It nets opposite flows across clients and corridors, settles only the net on-chain, and reduces prefunding, FX conversion, and fees.
2. **Purpose-bound payouts.** Funds are locked to a purpose (payroll, supplier invoice, tax, contractor), beneficiary class, corridor, and time. The payout releases only when the local leg matches the purpose-bound credentials. This is the MAS/BIS "purpose-bound money" idea applied to B2B payouts.
3. **Proof of Payout + Compliance Passport.** Proof of Payout verifies local-currency delivery without exposing recipient PII; the Compliance Passport reuses KYC/KYB and sanctions attestations across corridors with verifiable credentials.

The result is a different category: **a corridor clearinghouse that nets first, proves second, and only then moves money.** Most competitors settle gross and call it a day.

## The problem

### For businesses paying into India

- Cross-border payouts are slow, opaque, and expensive; FX margins and hidden fees eat the transfer.
- Compliance is fragmented: KYC/KYB, AML, sanctions, purpose codes, FIRC/FIRA, tax records, and partner-specific requirements.
- Stablecoin rails are fast but do not solve the last mile: INR payout, UPI/IMPS availability, beneficiary validation, refunds, and reconciliation.
- The stablecoin leg and the fiat leg have different legal treatment. In India, stablecoins are virtual digital assets, not legal tender; a compliant design must keep the stablecoin leg offshore and let only fiat enter India through an authorized dealer (AD) bank.
- A failed payout is not a failed transaction: the money may be stuck at a partner, a beneficiary may reject it, or a sanctions hit may freeze it. Reconciliation and refunds are the real product.

### For platforms and agents

- Payroll platforms, marketplaces, creator platforms, and AI agents need to pay many recipients across corridors.
- They do not want to become compliance or banking companies.
- They need one API, deterministic fees, a receipt, a refund path, and a clear audit trail.

### The gap

Existing providers solve slices:

- **Wise, Payoneer, Skydo, PayPal** are fiat-first and do not natively accept stablecoins.
- **Xflow** has a compliant stablecoin-acceptance pilot for Indian businesses, with the stablecoin leg outside India and fiat entering via an AD Category I bank.
- **Nium + Circle** connect USDC settlement to global payouts across 190+ countries.
- **Visa + Inyo** enable USDC prefunding for cross-border payouts.
- **Stripe** supports stablecoin payouts in private preview.
- **Crossmint, Blockradar, Payzoll** provide stablecoin payment/remittance infrastructure.

The missing layer is a **programmable corridor** that combines compliance, FX, escrow, local payout, proof, and agentic initiation in one product.

## Why now

- **India is the largest remittance market.** The RBI reports a record **$137.7B** inflow in 2024; India is the top recipient globally.
- **UPI is the distribution rail.** UPI processed about **131B transactions worth $1.8T in 2024** (NPCI/Tazapay), giving stablecoin off-ramps a real-time last mile.
- **Stablecoin remittances are growing.** Industry estimates suggest **3–4% of Indian remittances** have moved to stablecoins, with a 4–5% USDT premium in India; this is a regulatory grey zone, not a design goal.
- **Institutional rails are forming.** Circle's Payments Network, Nium's payout network, Visa/Inyo USDC prefunding, and Stripe's machine payments all validate the stablecoin-to-fiat direction.
- **Monad is building an agent-payments economy.** Monad has x402 support, the Agent Hub, MetaMask Agent Wallet support, the Agentic Payments Alliance, and high-throughput stablecoin payments.
- **Ethereum/Base has the liquidity and standards.** USDC, ERC-4337/8196 account abstraction, Safe, and the deepest DeFi/RWA ecosystem.

## The product: DashX Corridor

DashX Corridor is a full-stack settlement layer with eight components.

### 1. Corridor Router — the orchestration brain

Given a payout intent, the router selects:

- the funding rail (USDC on Monad/ETH/Base, or fiat);
- the FX venue and quote;
- the licensed banking/payout partner per corridor;
- the local rail (UPI, IMPS, NEFT, ACH, SEPA, local wallet);
- the fee/SLA/risk trade-off;
- the fallback route if a partner is down.

The router is policy-driven. It can optimize for cost, speed, or success rate, and it can enforce corridor allowlists and per-client limits.

### 2. Compliance Passport — reusable, privacy-preserving KYC/KYB

The compliance burden is abstracted, not eliminated. DashX and its licensed partners still perform the checks. The passport makes them reusable:

- KYC/KYB attestations with expiry and scope;
- sanctions/AML screening results;
- purpose-code and corridor eligibility;
- travel-rule data where required;
- predicate proofs for “verified business,” “allowed corridor,” or “under limit” without exposing the underlying documents.

The client sees one onboarding flow; the partner sees the attestations it needs. The stablecoin leg stays outside India where required; only fiat enters via the AD bank.

### 3. USDC Escrow and Atomic Payout

A payout intent locks USDC in an on-chain escrow on Monad or Ethereum/Base. The escrow releases when the local payout is confirmed by the licensed partner and the Proof of Payout is issued. If the payout fails, the escrow refunds or retries under policy.

The escrow is not custody of user funds in the regulatory sense; it is a settlement condition. The legal design must be reviewed per corridor.

### 4. Liquidity and FX Engine

Cross-border payouts need local liquidity, not just USDC. The engine manages:

- USDC balances on each chain;
- local-currency prefunding with banking partners;
- FX quotes and hedging;
- partner balance limits and rebalancing;
- float/yield on idle balances;
- corridor-level liquidity risk.

This is the operational moat: a competitor can copy the API, not the partner balances and FX relationships.

### 5. Payout Receipts and Reconciliation

Every payout produces a signed receipt binding:

- payout intent id;
- client and beneficiary references;
- FX quote and fee;
- partner and rail;
- local payout reference (UPI/IMPS/UTR);
- status and timestamp;
- Proof of Payout hash.

The reconciliation loop matches the on-chain escrow, the partner ledger, and the beneficiary confirmation. It detects stuck payouts, duplicate payments, FX drift, and partner balance mismatches.

### 6. Agentic Payouts

AI agents, ERPs, and payroll platforms can initiate payouts through an API/MCP tool. Every agent action is bounded by:

- per-payout, daily, and corridor limits;
- beneficiary allowlists;
- purpose-code allowlists;
- human approval tiers;
- a kill switch.

This is where Monad's agent-payments ecosystem matters: an agent can pay a contractor, a creator, or an API provider in USDC, and DashX handles the compliant local payout.

### 7. Proof of Payout — the category-defining primitive

A recipient's bank details and identity should not be public. But the client, auditor, and tax authority need proof that the INR arrived.

**Proof of Payout** is a privacy-preserving receipt:

- proves that a payout was executed through a licensed partner;
- proves the local-currency amount and timestamp;
- proves the beneficiary reference matches the intent;
- hides the beneficiary's PII and bank details;
- can be verified by the client, an auditor, or a tax workflow;
- can be anchored on Monad/ETH as a hash or a ZK proof.

This turns “trust us, we paid” into “verify the payout without seeing the recipient.”

### 8. Developer API and Dashboard

One API for:

- quotes;
- beneficiary validation;
- payout creation;
- batch payroll;
- status webhooks;
- receipts and Proof of Payout;
- reconciliation and disputes;
- compliance passport status.

The dashboard shows corridor health, FX, fees, success rates, stuck payouts, and refunds.

### 9. Programmable Netting Engine

The netting engine turns DashX's two-sided corridor book into a liquidity advantage:

- pool payouts and receipts by corridor, client, and settlement window;
- match opposite flows (US→India payouts against India→US receipts from exporters/freelancers);
- settle only the net position on-chain;
- use a liquidity hub for one-way corridors or timing gaps;
- pass a share of the saved FX, prefunding, and on-chain costs back to clients.

Netting must preserve compliance: every netted payout still carries its own Compliance Passport, purpose-bound credential, and Proof of Payout. The net settlement is an optimization, not a way to hide individual obligations.

On Monad, the netting batch and per-payout receipts are cheap enough to run frequently; 300ms blocks make deferred net settlement practical without a long float.

### 10. Purpose-Bound Payouts

A purpose-bound payout is a payment with a machine-checkable "what it is for" condition:

- **payroll:** only to allowlisted beneficiaries in the payroll class;
- **supplier payment:** must match a purchase order or invoice commitment;
- **tax:** must match a tax identifier and jurisdiction;
- **contractor:** must match a statement of work or milestone;
- **refund:** must match the original payout id.

The stablecoin escrow releases only when the local leg returns a matching purpose code, beneficiary class, and merchant/category signal. A payroll batch cannot be redirected to a gambling merchant; a supplier payment cannot be redirected to a personal account; a tax payment cannot be routed to a different jurisdiction.

Privacy is preserved through predicate proofs: the client proves "this payout is payroll for an allowlisted beneficiary" without revealing the salary, the employee's identity, or the full invoice.

## Architecture

```text
 client / ERP / AI agent
        |
        v
 +-------------------+       +----------------------+
 | Payout API / MCP  | ----> | Corridor Router      |
 | intent + limits   |       | rail + FX + partner  |
 +-------------------+       +----------+-----------+
                                        |
              +-------------------------+-------------------------+
              |                                                   |
              v                                                   v
 +-------------------+                               +----------------------+
 | Compliance        |                               | USDC Escrow          |
 | Passport          |                               | Monad / ETH / Base   |
 +---------+---------+                               +----------+-----------+
           |                                                    |
           v                                                    v
 +-------------------+                               +----------------------+
 | Licensed partner  | <---------------------------- | Settlement engine    |
 | AD bank / payout  |                               | release / refund     |
 +---------+---------+                               +----------+-----------+
           |                                                    |
           v                                                    v
 +-------------------+                               +----------------------+
 | Local rail        | ----------------------------> | Proof of Payout      |
 | UPI/IMPS/NEFT     |                               | receipt + attestation|
 +-------------------+                               +----------------------+
```

### Monad deployment

- **Contracts:** `CorridorRegistry`, `PayoutIntent`, `USDCEscrow`, `ComplianceAttestation`, `ReceiptRegistry`, `ProofOfPayout`, `LiquidityVault`.
- **Execution:** Monad USDC; x402/agent payments; MetaMask Agent Wallet or smart account; Monad Agent Hub for discovery.
- **Why Monad:** 300ms blocks, high throughput, low fees; batch payroll and micro-payouts become practical; per-payout receipts and Proof of Payout are cheap.
- **Demo:** a batch payroll of 1,000 Indian contractors, paid in USDC on Monad, settled to INR via a mock partner, with Proof of Payout for each.
- **Track:** Consumer Products and Payments; Onchain Finance and Trading; Trust, Identity, and AI Infrastructure.

### Ethereum deployment

- **Contracts:** same core on Base/Arbitrum/ETH; USDC; Safe/ERC-4337/8196 account policies; `ProofOfPayout` verifier.
- **Execution:** Circle USDC, Coinbase/Stripe/Nium rails, Safe smart accounts, institutional custody.
- **Why Ethereum/Base:** deepest USDC liquidity, institutional credibility, account abstraction, and the strongest RWA/payments ecosystem.
- **Demo:** an institutional treasury payout with escrow, compliance passport, and an auditable Proof of Payout; a sanctions hit blocks one payout and refunds automatically.
- **Track:** DeFi, payments, RWA, agentic commerce.

## Data model

### Payout intent

```json
{
  "intent_id": "dx_2026_10_001",
  "client_id": "acme_us",
  "corridor": "US-IND",
  "funding_asset": "USDC",
  "funding_chain": "monad",
  "amount_usdc": 12500.00,
  "fx_quote_id": "fx_2026_10_001",
  "beneficiaries": 50,
  "purpose_code": "contractor_payment",
  "local_rail": "UPI",
  "deadline": "2026-10-25T12:00:00Z",
  "policy_id": "payroll_standard"
}
```

### Compliance attestation

```json
{
  "attestation_id": "cp_2026_10_001",
  "subject": "acme_us",
  "scope": ["US-IND", "contractor_payment"],
  "kyc_status": "verified",
  "sanctions_status": "clear",
  "expires_at": "2027-04-01T00:00:00Z",
  "proof_hash": "0x..."
}
```

### Proof of Payout

```json
{
  "payout_id": "po_2026_10_001",
  "intent_id": "dx_2026_10_001",
  "beneficiary_ref": "ben_8842",
  "local_amount_inr": 1042500,
  "fx_rate": 83.40,
  "partner": "licensed_ad_bank_1",
  "local_rail": "UPI",
  "utr": "UTR123456789",
  "executed_at": "2026-10-24T10:15:00Z",
  "proof_hash": "0x...",
  "pii_disclosed": false
}
```

## Demo (5 minutes)

**Act 1 — The gross corridor.** Twenty clients send $2M of gross US→India payouts in one week. Every payout settles gross; the clients pre-fund the full amount, pay FX on each leg, and cannot prove delivery without exporting bank data.

**Act 2 — Netting.** DashX pools the two sides of the corridor: exporters and freelancers receiving USD, and businesses paying INR. Opposite flows net from $2M gross to $200K of net settlement. Only the net moves on-chain; prefunding, FX, and fees fall. The savings are passed back to clients.

**Act 3 — Purpose lock.** A payroll batch tries to route one payment to a non-payroll merchant. The purpose-bound credential does not match the merchant category; the payout is held before INR leaves the partner. A supplier payment with a mismatched purchase order is blocked the same way.

**Act 4 — Local rails and proof.** The licensed partner pays the valid INR payouts over UPI/IMPS. Each payout gets a UTR and a Proof of Payout. One beneficiary fails KYC and one is a sanctions hit; the escrow holds or refunds those payouts, and reconciliation shows exactly what happened. The client verifies delivery without seeing beneficiary PII.

**Act 5 — The agent.** An AI agent initiates a recurring contractor payroll under a policy: max 50 recipients, max $20K/day, allowlisted corridor, human approval above $10K. The agent cannot exceed the policy. The netting engine settles the batch; each payout carries a Proof of Payout anchored on Monad.

**Close:** “Stablecoins move value. DashX nets the corridor, binds the purpose, and proves the payout.”

## Hackathon positioning

### Best one-liner

> **DashX is the corridor clearinghouse for purpose-bound, proof-carrying stablecoin payouts: net the flows, settle only the difference, and prove every local payout without exposing the recipient.**

### Monad positioning

- **Narrative:** “Monad's 300ms blocks and low fees make multilateral netting, batch payroll, per-payout receipts, and Proof of Payout practical. DashX is the corridor clearinghouse for Monad's agent economy.”
- **Demo:** 1,000 contractor payouts in one batch, USDC on Monad, INR via UPI/IMPS, per-payout proof.
- **Why Monad wins:** payments are a stated Monad priority; Monad has x402, Agent Hub, MetaMask Agent Wallet, and high-throughput stablecoin settlement.

### Ethereum positioning

- **Narrative:** “Ethereum/Base has the deepest USDC liquidity and the strongest institutional rails. DashX is the netting, purpose-bound compliance, and proof layer for stablecoin payouts.”
- **Demo:** institutional treasury payout with escrow, Compliance Passport, and auditable Proof of Payout.
- **Why Ethereum wins:** USDC, Circle, Safe/ERC-4337/8196, and the RWA/institutional ecosystem.

### What not to pitch

- “Another remittance app.” DashX is infrastructure for platforms, ERPs, and agents.
- “No compliance.” Compliance is abstracted, not eliminated; licensed partners and local law still apply.
- “Stablecoins are legal tender in India.” They are VDAs; the stablecoin leg should stay offshore and only fiat should enter via an AD bank.
- “Instant everywhere.” UPI/IMPS are fast, but partner SLAs, beneficiary validation, and sanctions checks can add time.
- “Trustless banking partner.” The partner is a trust and regulatory dependency; the product must state it.

## Competitive landscape

| Player | What it does | Where DashX differs |
|---|---|---|
| **Wise / Payoneer / Skydo / PayPal** | Fiat-first cross-border payments | DashX nets the corridor and proves purpose/delivery; fiat-first competitors settle gross |
| **Xflow** | Compliant stablecoin acceptance for Indian businesses; stablecoin leg outside India; AD Category I bank | DashX is a two-sided netting and proof layer, not just stablecoin acceptance |
| **Nium + Circle** | USDC settlement + global payouts across 190+ countries | DashX can integrate Nium as a payout partner; the wedge is netting, purpose-bound compliance, and Proof of Payout |
| **Visa + Inyo** | USDC prefunding for cross-border payouts | DashX nets first, then settles; the product is the corridor clearinghouse and proof layer |
| **Stripe** | Stablecoin payouts in private preview; MPP/x402 | DashX is corridor-specific, netted, purpose-bound, and partner-orchestrated, with local-rail proof |
| **Crossmint / Blockradar / Payzoll** | Stablecoin payment/remittance infrastructure | DashX owns the netting book, compliance corridor, FX/liquidity, and Proof of Payout |
| **Banks / AD Category I partners** | Local payout and FX | DashX is the orchestration layer on top; partners remain licensed |

Most competitors settle gross. DashX nets first, binds the purpose, and proves delivery. The netting book is the economic wedge; the proof is the trust wedge.

## Moat

1. **Two-sided netting book (or liquidity-hub network).** DashX assembles exporters/freelancers receiving USD and businesses paying INR; the more clients on both sides, the lower the cost. A one-sided competitor cannot match the economics without a liquidity hub.
2. **Corridor operations.** Banking partners, local liquidity, FX, and payout SLAs are hard to replicate.
3. **Compliance Passport.** Reusable KYC/KYB and predicate proofs reduce onboarding friction and become a switching cost.
4. **Purpose-bound compliance standard.** A machine-checkable purpose/payout format that clients, partners, and auditors can adopt.
5. **Proof of Payout.** A privacy-preserving delivery receipt that clients and auditors can verify; a category standard.
6. **Agentic payout policy.** A safe way for AI agents/ERPs to initiate payouts under limits.
7. **Reconciliation and refunds.** The unglamorous operational layer that determines whether clients stay.

## Business model

- **Netting savings share:** a share of the FX, prefunding, and on-chain savings created by netting.
- **FX spread / fee:** a transparent fee per payout or a small FX margin.
- **Platform fee:** SaaS/API subscription for platforms and ERPs.
- **Float/yield:** yield on prefunded balances and treasury (subject to regulation).
- **Agentic payout fees:** per-payout or per-batch fees for agent-initiated payments.
- **Proof/compliance tier:** audit, purpose-bound compliance, and tax reporting for enterprises.
- **Partner revenue share:** referral/integration fees with licensed partners.

## Honest cons and failure modes

- **Regulatory complexity.** India treats stablecoins as VDAs; the stablecoin leg must stay offshore and fiat must enter through an AD bank. Corridor rules change; legal review is mandatory.
- **Banking-partner dependency.** A partner can freeze, de-risk, or change terms. Multi-partner redundancy is essential.
- **Liquidity and FX risk.** Prefunded local balances and FX exposure can lose money; hedging and limits are required.
- **Netting is not magic.** Netting needs a two-sided book and sufficient volume; one-way corridors need a liquidity hub and may save less. A small network will not reach bank-scale netting efficiency.
- **Purpose semantics.** A purpose code is not proof of purpose. The local partner must return reliable merchant/category/beneficiary data, or the purpose-bound control is weak.
- **Stablecoin transit alone is not cheaper.** Banca d'Italia's 2026 mystery-shopping study and BIS research find stablecoin remittances are not automatically cheaper; the savings must come from netting, liquidity, and operations.
- **Netting versus traceability.** Netting across clients must not obscure individual obligations. Every netted payout needs its own Compliance Passport, purpose credential, and Proof of Payout.
- **Stablecoin depeg.** USDC/USDT depeg risk affects escrow and settlement; the design needs haircuts, circuit breakers, and alternative rails.
- **Competition.** Xflow, Nium, Visa/Inyo, Stripe, Wise, and Payoneer are all moving. The wedge is the two-sided netting book + purpose-bound compliance + proof, not generic “stablecoin payments.”
- **Compliance is not free.** “No compliance overhead for clients” is a UX claim; DashX still performs KYC/KYB, sanctions, and travel-rule checks.
- **Payout failure and disputes.** Beneficiary rejection, wrong UTR, duplicate payout, and sanctions holds need a real refund/dispute process.
- **Agent risk.** An agent with payout authority can be prompt-injected; limits, allowlists, and human approval are mandatory.
- **Proof scope.** Proof of Payout proves execution through a partner, not the beneficiary’s legal identity or the absence of fraud.

## Build plan (5 humans + 10 agents, no time cap)

| Workstream | Humans | Agents | Output |
|---|---:|---:|---|
| Corridor router + FX | 1 | 2 | Quotes, partner selection, fallback routes |
| Compliance passport | 1 | 2 | KYC/KYB attestations, sanctions, predicate proofs |
| Escrow + receipts | 1 | 2 | Monad/ETH contracts, Proof of Payout, refunds |
| Partner integration | 1 | 2 | Mock AD bank/UPI partner, webhooks, reconciliation |
| Agent/API + UX | 1 | 2 | Payout API/MCP, policy limits, dashboard |
| Integration/QA | all | shared | End-to-end demo, failure paths, rehearsal |

## Scorecard

| Dimension | Score | Why |
|---|---:|---|
| Market size | 5 | India remittances are $137.7B; cross-border B2B is large |
| Wedge | 5 | Two-sided netting + purpose-bound payouts + Proof of Payout + agentic payouts |
| Monad fit | 5 | High-throughput netting/payouts, x402, Agent Hub, payments priority |
| ETH fit | 4 | Deep USDC/institutional rails; crowded payments market |
| Demoability | 5 | Netting + purpose lock + UPI payout + proof + sanctions/refund |
| Defensibility | 5 | Netting network effects + purpose-bound compliance + proof standard |
| Execution risk | 3 | Regulatory, banking partners, FX, liquidity, reconciliation |
| **Total** | **32/35** | Strong fit; execution and compliance are the hard parts |

## Sources

- DashX: https://dashx.xyz/
- DashX business infrastructure: https://dashx.xyz/business
- DashX vs Wise: https://dashx.xyz/comparison/wise
- Xflow stablecoin acceptance pilot: https://www.business-standard.com/content/press-releases-ani/xflow-becomes-the-first-cross-border-payments-platform-to-bring-compliant-stablecoin-acceptance-to-indian-businesses-126051400726_1.html
- Nium + Circle USDC settlement: https://www.circle.com/pressroom/nium-and-circle-to-connect-usdc-settlement-with-global-payouts
- Visa + Inyo USDC prefunding: https://fintechnews.sg/139027/digitalassets/visa-usdc-stablecoin-prefunding/
- Stripe machine payments: https://docs.stripe.com/payments/machine
- Stablecoins in emerging markets playbook: https://tazapay.com/guides/stablecoins-cross-border-payments-emerging-markets
- RBI remittance data: https://www.rbi.org.in/Scripts/PublicationsView.aspx?id=23436
- India stablecoin/UPI policy context: https://btcmedia.news/posts/2026-10-24-bloom-malaysia-thailand-india-nexus/
- Monad x402: https://docs.monad.xyz/guides/x402
- Monad x402 Foundation: https://www.monad.xyz/blog/monad-foundation-joins-x402-foundation
- Monad machine economy: https://monad.xyz/blog/rise-of-machine-economy
- Ankr x402 on Monad: https://www.ankr.com/blog/ankr-launches-x402-payments-starting-with-monad/
- Ethereum AI agents: https://ethereum.org/ai-agents/
- ERC-4337: https://eips.ethereum.org/EIPS/eip-4337
- ERC-8196 AI Agent Authenticated Wallet: https://eips.ethereum.org/EIPS/eip-8196
- Banca d'Italia, stablecoin remittance mystery-shopping study: https://www.bancaditalia.it/media/notizia/are-stablecoins-efficient-for-remittances-evidence-from-a-mystery-shopping-exercise-by-banca-d-italia/
- Global Finance summary of the Banca d'Italia study: https://gfmag.com/banking/stablecoin-remittances-face-reality-check-banca-ditalia-study/
- BIS, Exploring multilateral platforms for cross-border payments: https://www.bis.org/cpmi/publ/d213.pdf
- BIS, Project Rosalind: https://www.bis.org/project/rosalind
- BIS, Project Agorá: https://www.bis.org/project/agora
- IMF, Programmability in Payment and Settlement (WP/24/177): https://www.elibrary.imf.org/downloadpdf/view/journals/001/2024/177/001.2024.issue-177-en.pdf
- World Bank, Remittance Prices Worldwide: https://remittanceprices.worldbank.org/
- European Commission, eID and KYC portability: https://digital-strategy.ec.europa.eu/en/library/reports-expert-group-eid-and-kyc-processes
- BIS, Annual Economic Report 2025 — tokenisation and cross-border payments: https://www.bis.org/publ/arpdf/ar2025e3.htm
- BIS, stablecoins and cross-border payments: https://www.bis.org/publ/bppdf/bispap167.pdf
