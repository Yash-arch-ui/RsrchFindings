# Stablecoin Payments Research

Cross-border stablecoin settlement and local-rail payouts, with a focus on **Monad** and **Ethereum/Base**. This is a separate topic from perpetuals, privacy, and agentic commerce: the product is the compliant corridor, not the trade or the credential.

## Idea

- [01-passage-cross-border-corridor.md](01-passage-cross-border-corridor.md) — **Passage Corridor**: a programmable, compliance-aware stablecoin-to-local-rails settlement layer for Monad and ETH, with a Corridor Router, multilateral netting, purpose-bound payouts, Compliance Passport, USDC escrow, liquidity/FX engine, Proof of Payout, and agentic payouts.

## Why this topic

India is the world's largest remittance recipient, UPI is a massive real-time rail, and stablecoin settlement is growing — but the hard part is not moving USDC. It is compliance, FX, liquidity, last-mile payout, reconciliation, refunds, and regulatory boundaries. The winning product is an **orchestration layer** that keeps the stablecoin leg offshore where required, uses licensed banking partners for local payouts, and gives clients one API and one receipt.

## Scope

- **In scope:** India first (UPI/IMPS/NEFT), then EU and UAE corridors; B2B payouts, payroll, freelancer/creator payouts, invoice settlement, treasury.
- **Out of scope:** consumer remittance app, custody, unlicensed money transmission, and any design that treats stablecoins as legal tender in India.
- **Chains:** Monad (high-throughput payouts, agent payments, x402) and Ethereum/Base (USDC liquidity, account abstraction, institutional rails). Zcash is not a fit for this product.
