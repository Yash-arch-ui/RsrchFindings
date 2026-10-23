# Rialto: Privacy-Preserving Decentralized Exchange Marketplaces

## Metadata

- **Authors:** Kavya Govindarajan, Dhinakaran Vinayagamurthy, Praveen Jayachandran (IBM Research India), Chester Rebeiro (IIT Madras)
- **Date:** arXiv:2111.15259v2 [cs.CR], 20 December 2021
- **Length:** 17 pages
- **Type:** Protocol design, formal security definitions, implementation, and evaluation.
- **Read status:** Full main text, security analysis, evaluation, and conclusion read; appendices sampled.

## One-paragraph summary

Rialto is a fully decentralized, privacy-preserving exchange marketplace with order matching, on-chain settlement, and price discovery. Traders submit **Pedersen commitments** to their order rates and account balances, secret-share rates to a fixed set of **broker** nodes, and prove in zero knowledge that a buy order is covered by the account balance. Brokers run **MPC sorting** over the secret-shared rates and submit a sorted list to a smart contract, which performs maximal matching in the clear over the sorted order. Settlement uses the homomorphic property of Pedersen commitments so that private rates can be added to balances without revealing them; only the **aggregate marketplace fee** is revealed. A **Waksman-network shuffle** with re-randomized balance commitments unlinks traders from their accounts across rounds. The paper defines order-rate confidentiality, trader unlinkability, account-balance confidentiality, front-running resilience, fairness, integrity, and availability, and evaluates the design on Hyperledger Fabric + MP-SPDZ. The key limitation for perpetuals is scope: Rialto is a **spot** marketplace with unit-quantity orders, no shorting, no margin, no leverage, no funding, and no liquidation.

## Problem and design space

Traditional centralized exchanges keep order and balance information private from the market but not from the operator. Decentralized exchanges remove the operator but publish orders and balances on-chain, enabling front-running and strategy copying.

The paper lays out design alternatives along three axes:

1. **Decentralization** — centralized vs blockchain-based.
2. **Account-balance confidentiality** — public vs committed/private.
3. **Order-rate confidentiality** — public vs encrypted/secret-shared.

A key observation: **order privacy without balance privacy is insufficient**. If balances are public, an observer can infer an order's price from the before/after balance change. Rialto therefore protects both.

## System model

- Traders place **limit orders**; buy orders specify a maximum price, sell orders a minimum.
- Each order is for **exactly one unit** of the asset. Multi-unit orders are represented as multiple unit orders. This simplifies matching but does not support quantity privacy or volume discounts.
- The marketplace records orders and periodically triggers matching.
- Unmatched orders are carried forward for a limited number of rounds, then expelled.
- Spot only: a buyer must have sufficient balance; a seller must hold the asset. **No margin buying and no short selling.**
- Account model, not UTXO, to support KYC, authorization, deposit interest, and loan rates.
- Settlement records ownership on a ledger managed by the marketplace; physical delivery is out of scope.

## Architecture

### Roles and trust

- **Traders:** untrusted, financially incentivized to lie.
- **Blockchain facilitators:** individual nodes may be malicious; a sufficient majority is honest (e.g. two-thirds BFT).
- **Broker nodes:** may collude with traders, facilitators, or other brokers.
  - **Rialto:** brokers are semi-honest (try to learn but follow the protocol).
  - **Rialto+:** brokers are malicious (can arbitrarily deviate); a `(k,n)` threshold secret-sharing scheme with `k = floor(n/2)+1` lets the protocol proceed when a minority is malicious.
- Colluding traders and blockchain facilitators are treated as malicious in both versions.

### Components

- **Hyperledger Fabric** permissioned blockchain.
- **Smart contracts (chaincode) in Go** for balances, order book, matching, settlement.
- **MP-SPDZ** for MPC:
  - semi-honest: replicated-ring-party for 3 parties, Shamir for >3;
  - malicious: `ps-rep-ring-party`.
- **Bulletproofs** for range proofs and Pedersen commitments on Edwards25519.
- **Rust wrapper + Cgo** for on-chain proof verification.

## Protocol

### 1. Order submission

A trader secret-shares its private rate into `M` additive shares, where `M` is the number of brokers. It submits to the ledger:

- commitments to each share,
- a commitment to its account balance,
- a ZK range proof that the buy order rate is `<=` the account balance.

It sends each broker the opening of that broker's share commitment. It also sends a share of a random blinding factor used later for re-randomization. For a buy order, the blockchain locks funds equal to the buyer's rate in escrow by adding the share commitments; the account-balance commitment is reduced by the locked value. Only one trade order per account per round is allowed, preventing double spending.

### 2. Sorting using MPC

Brokers wait for the earlier of `T` seconds or `N` orders, then run `SortingMPC`:

- inputs: each broker's share of each trader's rate;
- output: a single totally ordered list of buy and sell orders in increasing price order;
- leakage: the sorted list reveals **relative positions** of all orders.

The brokers submit the sorted list to the smart contract, triggering matching.

### 3. Input-share validation

A malicious broker colluding with a trader could modify rate-share inputs to gain a matching advantage. Rialto/Rialto+ validate each broker's input against its public Pedersen commitment using a distributed Schnorr-style ZKPoK:

- **Preparation:** each broker generates random `y_i, s_i` and `d_i = g^{y_i} h^{s_i}`.
- **Validation (MPC):** compute `e_i = H(g,h,y_i,s_i)`, `a_i = y_i + e_i v_i`, `b_i = s_i + e_i r_i`; output `{a_i,b_i,d_i,e_i}`.
- **Offline verification:** each broker checks `g^{a_i} h^{b_i} = d_i (c_i)^{e_i}`.

This avoids exponentiation inside MPC and preserves soundness/zero-knowledge.

### 4. Matching

The smart contract receives the sorted list and matches in the clear:

- Consider buy and sell orders in ascending order.
- Match a buy with the first sell whose rate is lower than the buy's rate.
- Produces a **maximal matching** (proof in Appendix D).
- Unmatched orders carry forward; stale orders are removed.

**Fairness issue:** ascending order is fair to sellers but not to buyers, because the most competitive buyers can be left unmatched. Rialto fixes this with a **swapping procedure**: swap unmatched buyers with matched buyers who have lower rates, giving precedence to higher-rate buyers. After at most `n` swaps, the matching is both maximal and fair.

### 5. Settlement

Rialto uses a **"marketplace earns the rate difference"** scheme: each trader settles at their proposed rate, and the difference between buyer and seller rates is paid to the marketplace as fees.

1. Brokers run `SettlementMPC` on the rate shares of matched buyers and sellers.
2. Output: the **sum of all differences** across matched pairs, not any individual spread.
3. Brokers submit the aggregate to the marketplace.
4. The smart contract adds the seller's committed rate to the seller's balance commitment, removes the buyer's escrowed funds, and adds fees to the marketplace account, using Pedersen homomorphism.

The aggregate fee is the only settlement information revealed.

### 6. Re-randomization and shuffling

After settlement, the difference between old and new account balance equals the order rate. That would link an account to its trade order and link accounts across rounds.

Rialto prevents this with:

- a secret-shared random blinding factor submitted at order time;
- a **Waksman permutation network** run in MPC;
- re-randomized account-balance commitments.

Each broker computes the commitment of zero using its blinding-factor share offline, adds it to the balance commitment, and permutes the result. The composition of independent broker permutations is uniformly random. The marketplace replaces old balance commitments with the shuffled set. The trader can compute its new commitment locally.

Complexity: a Waksman circuit has depth `O(log N)` and `O(N log N)` swappers; composing `M` networks gives `O(M N log N)`.

### 7. Price discovery

The marketplace reveals the **top-K settlement rates** after each round to support price discovery. Rialto computes them by reconstructing the buyer's rate for the top-K matched buy orders (after sorting) and revealing those rates. This is the main intentional leakage.

## Security properties

### Property 1: Order-rate confidentiality

No information about order rates except the top-K settlement rates is revealed at the end of a round.

The formal definition uses a simulator that receives:

- top-K rates for rounds `<= i`,
- the trader's account balance at the start of round `i`,
- the trader's own rates.

Rialto proves **relaxed order-rate confidentiality** where the simulator also receives the **order-book leakage**:

- top-K settlement rates per round,
- the ordering between order rates,
- the next round each account participates in (for matched orders),
- aggregate marketplace fees per round.

### Property 2: Trader unlinkability

An order cannot be linked to its trader, and orders from different rounds cannot be linked to the same unknown trader. Formally, the probability of linking an order in round `i` to an order by the same trader in round `i'` is at most `1/min(N_i, N_i')`.

The shuffle is what provides this. Without it, the balance difference equals the order rate and links the account.

### Property 3: Account-balance confidentiality

No information about a trader's balance is revealed except what is inferred from the union of order-book leakage across rounds.

### Property 4: Front-running resilience

A malicious trader cannot learn an ongoing unmatched order and submit a competing order for economic advantage. This follows from order-rate confidentiality before matching.

### Property 5: Market matching fairness

A less competitive buy/sell order cannot be matched while a more competitive order remains unmatched.

### Property 6: Market integrity

Once matched, settlement is automated. A deviating trader can be identified and penalized. Every settlement satisfies:

- buy rate `>=` sell rate,
- buyer pays no more than its buy rate,
- seller receives no less than its sell rate,
- buyer debit equals seller credit plus marketplace fee.

### Property 7: Availability / DoS resilience

- Traders cannot flood with unmatched orders: one order per account per round, funds locked, possible order fees, stale-order expiry.
- Broker DoS is covered by treating the broker as malicious.
- Blockchain DoS is mitigated by decentralization.

## Evaluation

### Setup

- 14-core 64-bit Intel Xeon Gold 6132 @ 2.60 GHz.
- Hyperledger Fabric, 4 peers by default.
- 512 orders per round, 3 brokers, 30-second round, 12 rounds, 12 experiments.
- Order prices uniform with mean 255 for buyers and 245 for sellers, variance 15.
- Bucketization baseline with bucket width 4.

### Results

- **Latency:** Rialto has the highest matching/settlement latency due to MPC, but scales sub-linearly with order volume from 128 to 1,024 orders per round. The 30-second round dominates average waiting time (~15 seconds).
- **Brokers:** increasing brokers from 3 to 8 increases sorting time linearly; matching and settlement stay nearly flat.
- **Privacy:** measured as `KL(P_E, P_T) / H(P_T) * 100`, the relative entropy of the estimated order-rate distribution from the true distribution. Privacy is higher against brokers than against traders (traders know their own rate), higher for lower `K`, and higher for more orders per round. Bucketization privacy does not vary much with `K` because rates are approximated to bucket granularity.
- **Throughput/fees:** maximal fair matching matches **85-90%** of orders, versus about **50%** for price-time matching. Price-time yields higher marketplace fees because it matches the most competitive buyer to the most competitive seller, creating a larger spread.
- **Communication:** with 512 orders and 3 brokers, each party communicates **1.38 MB** for semi-honest MPC and **20.79 MB** for malicious MPC.
- **Blockchain scalability:** varying peers from 4 to 12 did not affect latency.
- **Matching percentage/fees:** varying the quoted spread from 2% to 8% did not affect latency but changed match rate and fees.

## Findings

1. **Order privacy is not enough without balance privacy.** Public balances let an observer infer order rates from balance changes. Rialto protects both.
2. **MPC sorting leaks rank.** The sorted list reveals the relative ordering of all rates. Rialto explicitly includes this in its order-book leakage and proves only relaxed confidentiality.
3. **The matching smart contract sees the sorted list in the clear.** Privacy ends for the sorted order at the matching boundary, even though the rates remain secret-shared.
4. **Top-K price discovery is a deliberate leakage.** It is necessary for market utility, but it reduces order-rate confidentiality. The paper quantifies the tradeoff rather than claiming perfect privacy.
5. **Unlinkability requires an explicit shuffle.** Without re-randomization and Waksman permutation, the balance delta links account and order across rounds.
6. **Malicious brokers require input validation.** The distributed Schnorr proof binds MPC inputs to on-chain commitments; otherwise a colluding broker can tilt matching.
7. **Maximal fair matching improves fill rates but reduces fees.** 85-90% matched vs ~50% for price-time, but price-time extracts more spread.
8. **The design is spot-only.** Unit quantity, no shorting, no margin, no leverage, no funding, no liquidation, no perpetuals. The perp extension is not a parameter change; it requires a different risk and state model.

## Limitations and caveats

- **Spot only.** No margin buying, short selling, leverage, derivatives, or perpetuals.
- **Unit quantity.** Each order is one unit; no quantity privacy, volume discounts, or bulk-order optimization.
- **No cross-margin / portfolio risk.** Balances are asset-specific; there is no unified collateral or liquidation engine.
- **Permissioned blockchain.** Hyperledger Fabric with known identities; not a permissionless Ethereum deployment.
- **Broker trust.** Semi-honest in Rialto; malicious in Rialto+ with a minority threshold. If all brokers collude, privacy is lost.
- **Top-K leakage.** Settlement rates for the top-K matched buy orders are revealed.
- **Ordering leakage.** The sorted list reveals relative rate positions.
- **Participation leakage.** The next round each matched account participates in is exposed.
- **Aggregate fee leakage.** The total marketplace fee is revealed.
- **Matching in the clear.** The smart contract sees the sorted list.
- **No formal UC proof.** The security analysis is property-based with simulators, not a full UC realization.
- **No privacy for unmatched orders across rounds.** Unmatched orders can remain and restrict the anonymity set; the analysis notes this.
- **No physical delivery / cross-chain settlement.** Ownership is recorded on the marketplace ledger.
- **Performance depends on MPC and Fabric.** WAN latency and broker count matter; the evaluation is a research prototype.
- **No economic model for broker incentives.** Brokers are assumed to run; fees are possible but not analyzed.
- **No regulatory/KYC design.** Account model supports KYC, but the paper does not implement it.

## Hackathon preparation

### What this paper supports building

- A **private batch-auction perp**: encrypted order intents, MPC/secure sorting, uniform clearing, public aggregate risk.
- A **commitment-based margin engine**: prove collateral sufficiency without revealing balances.
- A **privacy-preserving price discovery** module: reveal top-K aggregate prices, not individual orders.
- A **shuffle-based unlinkability** layer for position/account state across funding rounds.
- A **fair matching** library: maximal matching plus buyer-swap fairness.
- A **leakage-aware matching spec**: explicitly list sorted-order, top-K, participation, and fee leaks.

### What this paper does not support building directly

- A full perpetual with funding, liquidation, and cross-margin.
- A permissionless EVM deployment without a different MPC/commitment stack.
- A quantity-private matching engine.
- A production broker network with incentives.

### Demo script (3 minutes)

1. Show a public order book: a large buy is visible, and the market front-runs it.
2. Submit the same order to Rialto: only commitments and secret shares; no price/size on-chain.
3. Run MPC sorting; show the sorted list but not the rates.
4. Match maximally and fairly; show 85-90% fill vs 50% price-time.
5. Settle with homomorphic commitments; reveal only the aggregate fee.
6. Shuffle balances; show that the trader's next-round account cannot be linked.
7. Turn on top-K disclosure; show the privacy/utility tradeoff.

### Judge Q&A prep

- **"How is this different from a dark pool?"** A dark pool trusts an operator. Rialto distributes matching across brokers and proves correctness; the operator does not see plaintext rates in the semi-honest model.
- **"What leaks?"** Sorted order, top-K rates, next-round participation, aggregate fees. We quantify it rather than claiming perfect privacy.
- **"Can it support perps?"** Not as-is. Perps need persistent positions, funding, margin, oracles, and liquidation. Rialto is a spot matching/settlement layer.
- **"Why Hyperledger Fabric?"** Permissioned identity and smart-contract access control; it is a research prototype, not a permissionless deployment.
- **"What if brokers collude?"** Privacy fails if all brokers collude. Rialto+ tolerates a malicious minority with threshold secret sharing.
- **"Is the matching really private?"** Rates are secret-shared, but the sorted list and matching output are visible to the smart contract. It is relaxed confidentiality, not end-to-end.

### Claims to avoid

- "Fully private exchange."
- "No information leaks."
- "Supports perpetuals."
- "Permissionless."
- "Trustless."
- "Quantity-private."

## Hackathon-level idea: ShadeBook

**One-liner:** A private batch-auction perpetual exchange with MPC clearing and public risk proofs.

**User:** Institutional traders who want size without telegraphing, and LPs who need aggregate risk visibility.

**Core flow:**

1. **Private order intent:** trader submits collateral and an encrypted/secret-shared perp order (side, size, limit, leverage) plus a ZK proof of collateral sufficiency.
2. **Batch discovery:** orders collect for a fixed window; no individual order is public.
3. **Secure clearing:** MPC (or an FHE/coSNARK alternative) sorts and matches; the clearing price and aggregate open interest are computed without revealing individual orders.
4. **Public risk proof:** publish aggregate long/short OI, insurance-fund coverage, and a solvency proof; individual positions stay private.
5. **Funding at batch close:** apply the decryption-aware funding rule from paper 1 — reveal, correct, measure, pay — so the batch's own price impact does not set the funding rate.
6. **Private liquidation:** threshold keepers receive encrypted liquidation hints or proofs; a keeper proves underwaterness without revealing the position to the public.
7. **Settlement:** on-chain state updates use commitments; a shuffle unlinks accounts across rounds.

**Demo moment:** A whale submits a $50M perp order. The public sees only a batch and aggregate OI. The batch clears, funding is measured after a correction window, and a keeper liquidates a position without revealing it. The privacy passport shows exactly what leaked: batch membership, aggregate OI, and settlement activity.

**Moat:** The combination of private batch clearing, public aggregate risk proofs, and decryption-aware funding. Each piece exists separately; the composition is the product.

**Cons:**

- MPC/FHE clearing is heavy and latency-sensitive.
- Batch auctions change UX and may reduce fill quality.
- Private liquidation detection is unsolved; threshold keepers reintroduce trust.
- Public aggregate OI can still leak through time-series changes.
- The design needs a robust oracle and a funding rule; those are separate hard problems.
- It is a research prototype, not a production exchange.
- The privacy claim is only as strong as the MPC/FHE assumptions and the operator/keeper set.

**Integration with the existing portfolio:** ShadeBook is the privacy-native execution venue for Perp Forge. CipherClock protects funding, VeilRisk attests solvency, VeilMap discloses leakage, and TailMark prices the liquidation tail.
