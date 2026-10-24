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

**Act 1 — The old corridor.** A US business pays 50 Indian contractors. The bank wire takes days, the FX margin is opaque, and the finance team cannot prove which recipients were paid without exporting bank data.

**Act 2 — One API.** The business submits one payout intent. DashX quotes the FX, shows the fee and SLA, runs the Compliance Passport, and locks USDC in escrow on Monad.

**Act 3 — The local rails.** The licensed partner pays INR via UPI/IMPS. The dashboard shows each payout, the UTR, and a Proof of Payout. The client verifies delivery without seeing beneficiary PII.

**Act 4 — The failure path.** One beneficiary fails KYC and another is a sanctions hit. The escrow refunds or holds those payouts; the rest settle. Reconciliation shows exactly what happened.

**Act 5 — The agent.** An AI agent initiates a recurring contractor payroll under a policy: max 50 recipients, max $20K/day, allowlisted corridor, human approval above $10K. The agent cannot exceed the policy. The payout runs; the Proof of Payout is anchored on Monad.

**Close:** “Stablecoins move value. DashX moves money into the real economy — with proof.”

## Hackathon positioning

### Best one-liner

> **DashX Corridor is the programmable settlement layer for compliant stablecoin payouts: one API, local rails, and a Proof of Payout that verifies delivery without exposing the recipient.**

### Monad positioning

- **Narrative:** “Monad's 300ms blocks and low fees make batch payroll, per-payout receipts, and Proof of Payout practical. DashX is the payments layer for Monad's agent economy.”
- **Demo:** 1,000 contractor payouts in one batch, USDC on Monad, INR via UPI/IMPS, per-payout proof.
- **Why Monad wins:** payments are a stated Monad priority; Monad has x402, Agent Hub, MetaMask Agent Wallet, and high-throughput stablecoin settlement.

### Ethereum positioning

- **Narrative:** “Ethereum/Base has the deepest USDC liquidity and the strongest institutional rails. DashX is the compliance and proof layer for stablecoin payouts.”
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
| **Wise / Payoneer / Skydo / PayPal** | Fiat-first cross-border payments | DashX accepts stablecoins and orchestrates stablecoin-to-local payout |
| **Xflow** | Compliant stablecoin acceptance for Indian businesses; stablecoin leg outside India; AD Category I bank | DashX is a multi-corridor orchestration layer with escrow, Proof of Payout, and agentic payouts |
| **Nium + Circle** | USDC settlement + global payouts across 190+ countries | DashX focuses on the India-first corridor, proof, and programmable compliance; can integrate Nium as a partner |
| **Visa + Inyo** | USDC prefunding for cross-border payouts | DashX is the client-facing orchestration and proof layer, not a card-network/processor |
| **Stripe** | Stablecoin payouts in private preview; MPP/x402 | DashX is corridor-specific and partner-orchestrated, with local-rail proof |
| **Crossmint / Blockradar / Payzoll** | Stablecoin payment/remittance infrastructure | DashX owns the compliance corridor, FX/liquidity, and Proof of Payout |
| **Banks / AD Category I partners** | Local payout and FX | DashX is the orchestration layer on top; partners remain licensed |

## Moat

1. **Corridor operations.** Banking partners, local liquidity, FX, and payout SLAs are hard to replicate.
2. **Compliance Passport.** Reusable KYC/KYB and predicate proofs reduce onboarding friction and become a switching cost.
3. **Proof of Payout.** A privacy-preserving delivery receipt that clients and auditors can verify; a category standard.
4. **Agentic payout policy.** A safe way for AI agents/ERPs to initiate payouts under limits.
5. **Reconciliation and refunds.** The unglamorous operational layer that determines whether clients stay.

## Business model

- **FX spread / fee:** a transparent fee per payout or a small FX margin.
- **Platform fee:** SaaS/API subscription for platforms and ERPs.
- **Float/yield:** yield on prefunded balances and treasury (subject to regulation).
- **Agentic payout fees:** per-payout or per-batch fees for agent-initiated payments.
- **Proof/compliance tier:** audit and tax reporting for enterprises.
- **Partner revenue share:** referral/integration fees with licensed partners.

## Honest cons and failure modes

- **Regulatory complexity.** India treats stablecoins as VDAs; the stablecoin leg must stay offshore and fiat must enter through an AD bank. Corridor rules change; legal review is mandatory.
- **Banking-partner dependency.** A partner can freeze, de-risk, or change terms. Multi-partner redundancy is essential.
- **Liquidity and FX risk.** Prefunded local balances and FX exposure can lose money; hedging and limits are required.
- **Stablecoin depeg.** USDC/USDT depeg risk affects escrow and settlement; the design needs haircuts, circuit breakers, and alternative rails.
- **Competition.** Xflow, Nium, Visa/Inyo, Stripe, Wise, and Payoneer are all moving. The wedge is the India-first corridor + proof + agentic policy, not generic “stablecoin payments.”
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
| Wedge | 5 | India-first corridor + Proof of Payout + agentic payouts |
| Monad fit | 5 | High-throughput payouts, x402, Agent Hub, payments priority |
| ETH fit | 4 | Deep USDC/institutional rails; crowded payments market |
| Demoability | 5 | Batch payroll + UPI payout + proof + sanctions/refund |
| Defensibility | 4 | Partners/liquidity/compliance are moats, but competition is strong |
| Execution risk | 3 | Regulatory, banking partners, FX, liquidity, reconciliation |
| **Total** | **31/35** | Strong fit; execution and compliance are the hard parts |

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
