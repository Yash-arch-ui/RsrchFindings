# P2DEX: Privacy-Preserving Decentralized Cryptocurrency Exchange

## Metadata

- **Authors:** Carsten Baum (Aarhus University), Bernardo David (IT University of Copenhagen), Tore Kasper Frederiksen (Alexandra Institute)
- **Date:** 2021. Extended abstract at ACNS 2021; this is the full version.
- **Length:** 42 pages
- **Type:** UC-secure protocol design, implementation, and benchmark.
- **Read status:** Full main text, protocol overview, security claims, implementation, evaluation, and conclusion read; formal functionalities and proofs sampled.

## One-paragraph summary

P2DEX is a privacy-preserving decentralized exchange for cross-ledger asset swaps. A set of servers runs **private order matching** over secret-shared orders using **publicly verifiable MPC**, while clients move funds to **threshold-signature burner addresses** so that no single server can steal them. Servers post collateral to a smart contract and are financially punished if they cheat; if they behave honestly, the on-chain footprint is as small as a centralized exchange (two settlement transactions). The protocol is proven secure in the **Universal Composability (UC)** framework against an **actively corrupted dishonest majority**, as long as at least one server is honest. If all servers are corrupted, clients can still be refunded, though their orders may leak. The implementation uses **SPDZ2k** over 32/64-bit integers and the Fresco framework, and shows that MPC-based matching is practical: the bottleneck is blockchain finality, not the MPC. The scope is one-shot cross-chain exchange, not perpetuals, margin, funding, or liquidation.

## Problem

Centralized exchanges hold user tokens and are routinely hacked (the paper cites over $8 billion lost). Decentralized exchanges avoid custody but publish orders before settlement, enabling front-running. Atomic swaps require extra transactions and do not provide order matching or privacy. The paper asks:

> Can we securely and efficiently exchange cryptocurrency tokens while preserving order privacy, avoiding front-running, and ensuring users never lose tokens?

The answer is a protocol that combines:

- **Privacy** — orders are secret-shared; matching is computed without revealing them.
- **Security** — UC composition; actively dishonest majority; token-theft resistance.
- **Efficiency** — optimistic on-chain cost equal to a centralized exchange; MPC only for matching.
- **Incentives** — servers post collateral and earn commissions; cheating is publicly proven and slashed.
- **Usability** — clients do lightweight computation and one transfer per exchange.

## Building blocks

- **Publicly verifiable MPC** — servers compute matching and produce a transcript that anyone can verify; if a server cheats, the transcript identifies it.
- **Threshold signatures with identifiable abort** — burner addresses are controlled by a threshold key; all servers must cooperate to sign a payout. A refusing server is identifiable.
- **UC framework** — security holds under concurrent composition, not just standalone execution.
- **Smart contracts** — hold server deposits, verify cheating proofs, enforce refunds, and coordinate cross-chain settlement.
- **Bulletin board** — public ledger for transactions and protocol messages.
- **Global clock** — models synchronous rounds.

## System model

- `n` servers `P_1,...,P_n`; `m` clients `C_1,...,C_m`; `ell` ledgers.
- Each client `C_j` holds `am_j^src` on ledger `L_j^src` at address `vk_j^src`, and wants `am_j^trg` on `L_j^trg` at `vk_j^trg`.
- Servers can exchange between any ledgers as long as all parties can use a standard smart contract on an exchange ledger `L_Ex`, and each ledger pair supports publicly provable double-spend (UTXO or unique coin IDs).
- The adversary can corrupt an **actively dishonest majority** of servers, provided at least one server remains honest.
- Clients are not required to post expensive collateral.

## Protocol

### 0. Smart-contract setup

Servers run a preprocessing step to sample a common threshold-signing key `sk`/`vk`, and each server publishes its individual verification key. They set up `ell*(ell-1)` instances of an identity functionality `F_Ident` for each ledger pair, then post a collateral deposit to the smart contract `F_SC`. If all deposits and signatures are consistent, the contract enters `ready`; otherwise servers are refunded.

### 1. Burner address setup

A client checks that the contract is `ready`, then sends a registration message to all servers. The servers generate a fresh burner address/public key `vk_j^ex` on the client's source ledger using threshold signatures, sign the client's data and the burner key, and return the signature.

### 2. Private order placement

The client transfers `am_j^src` from `vk_j^src` to `vk_j^ex` and inputs its transfer information into the appropriate `F_Ident` instance. The order itself is **secret-shared** among the servers; no server learns the price.

### 3. Confirmation

Servers wait until all clients have made their transfers and inputs. They sign the transfer information with the threshold key. If anything fails, at least one server signs an `abort` message and sends it to the smart contract.

### 4. Private matching

Servers run `compSwap` over all `F_Ident` instances using publicly verifiable MPC. If all shares are complete, they sign `ok`; otherwise they sign `abort`. If `ok` is signed, the servers use optimistic reveal to obtain the swaps. The MPC never learns non-matched orders; unmatched orders are discarded without disclosing their price limits.

### 5. Pay out

Servers compute payout transactions `tx_a = (id_a, In_a, Out_a, Sig_a)` and sign them under the burner addresses using threshold signatures. The transactions are sent to the clients touched by the transfer. If all transactions are signed, the servers sign `done`, and the smart contract refunds their deposits.

### Cheating recovery

- If a server sends `abort`, the contract waits for an `ok` or `done` signed by the threshold key.
- If `done` appears after `abort`, servers are refunded.
- If `ok` appears, each server runs `Reveal` and `Allow Verify` for each `F_Ident`, posting signed client registrations, transaction IDs, and shares. The contract checks whether all shares and signature shares are correct.
- If a server cheated, its deposit is sent to registered clients and honest servers are reimbursed.
- If no `ok`/`done` appears, servers post client registrations and source transactions; after a delay they create refund transactions. Missing or invalid shares cause the cheater's deposit to be shared among clients.

### Client DoS protection

A client who registers but does not transfer funds could block the batch. The paper modifies the protocol so that servers can proceed with clients whose transfers are finalized and refund late transfers; this prevents a single unstable client from delaying everyone.

## Security guarantees

- **UC-secure** against an actively corrupted dishonest majority, with at least one honest server.
- **No token theft** — funds are held at threshold addresses; a single server cannot move them.
- **No front-running** — orders are secret-shared and matched privately.
- **Public verifiability** — cheating is proven on-chain; the transcript identifies the cheater.
- **Refunds** — even if all servers are corrupted, clients can be refunded by the smart contract, though order privacy may be lost.
- **Optimal optimistic on-chain cost** — two settlement transactions, same as a centralized exchange; deposits are a one-time setup cost.
- **Malicious case** — at most seven transactions to complete the exchange or refund clients and return honest servers' deposits.
- **Clients do not post expensive collateral** — unlike prior insured-MPC work.
- **Servers are incentivized** by commissions and penalized by deposit slashing.

## Implementation and evaluation

### Matching algorithm

`CcompSwap` is a simplified limit-order matching function:

- All orders are for a **constant amount** of tokens from ledger `L_a`.
- Orders are matched **only by price**.
- Sort buy orders by maximum price and sell orders by minimum price.
- Match the largest acceptable buy price with the smallest acceptable sell price if the buy price is larger.
- Clearing price is the **average** of the two prices.
- Unmatched orders are discarded without revealing their limits.

The implementation uses Batcher's odd-even merge sort, with comparison depth `O(log^2 m)` and `O(m log^2 m)` comparisons. Overall round complexity is `O(log k * log^2 m)` and multiplication gate complexity `O(k * m log^2 m)`, where `k` is the bit length.

### MPC stack

- **SPDZ2k** over 32/64-bit integers.
- **Fresco** Java framework, dishonest-majority setting.
- Arithmetic sharing chosen to match real-world order matching and avoid binary-circuit overhead.
- Preprocessing is input-independent and can run before clients submit orders.

### Complexity

| Orders | 32-bit mults | 32-bit rounds | 64-bit mults | 64-bit rounds |
|---|---|---|---|---|
| 4 | 2,269 | 151 | 4,075 | 158 |
| 8 | 7,417 | 262 | 13,351 | 275 |
| 16 | 22,833 | 410 | 41,151 | 431 |
| 32 | 66,465 | 595 | 119,871 | 626 |
| 64 | 184,449 | 817 | 332,799 | 862 |
| 128 | 492,097 | 1,077 | 888,127 | 1,169 |

### Benchmarks

- AWS `m5.xlarge` (4 vCPU, 16 GiB RAM), 2-3 servers.
- LAN: same data center, <0.1 ms latency, up to 10 Gbps.
- WAN: Ireland/UK/Germany, ~10-25 ms latency.
- Memory never exceeds **536 MB**.
- The plot shows online matching in the range of milliseconds for small batches, **seconds on LAN**, and **tens of seconds on WAN** at 128 orders, depending on 32/64-bit and 2/3 servers.
- Preprocessing for `m=128`, `k=64` takes about **1.5 minutes** at 9,496 triples/second.
- Threshold signature estimates: **62-246 seconds** depending on phase and 2/3 servers, based on Gennaro-Goldfeder threshold ECDSA.
- **Blockchain finality is the real bottleneck:** Bitcoin ~10 minutes/block with 6 confirmations; Ethereum ~10-20 seconds/block and ~3 minutes finality; Cardano ~10 minutes.

## Findings

1. **Privacy and settlement can be composed without a trusted operator.** Publicly verifiable MPC + threshold signatures + smart-contract deposits gives order privacy, correctness, and refunds.
2. **Insured MPC is a powerful pattern.** Servers post collateral; cheating is publicly proven and slashed; clients get refunds even under a dishonest majority.
3. **Optimistic efficiency is real.** If nobody cheats, on-chain cost is two transactions, comparable to a centralized exchange; MPC is used only for matching.
4. **Threshold signatures are the custody primitive.** Burner addresses controlled by a threshold key prevent any single server from stealing funds.
5. **The matching algorithm is intentionally minimal.** Constant amount, price-only, average clearing price, no quantity privacy, no order expiry, no partial fills, no advanced order types.
6. **MPC is practical for small batches.** 128 orders is feasible; WAN latency and server count dominate. The blockchain finality delay is larger than the MPC time.
7. **The protocol is one-shot, not a continuous market.** It matches a batch and settles; it does not maintain persistent positions, margin, funding, or liquidation.
8. **The security model is strong but assumes at least one honest server.** If all servers are corrupted, privacy fails but refunds remain possible.

## Limitations and caveats

- **Not a perpetual protocol.** Cross-chain spot exchange only; no margin, leverage, shorting, funding, or liquidation.
- **Constant order amount.** No variable quantity, no quantity privacy, no volume-based matching.
- **Price-only matching.** No price-time priority, no pro-rata, no advanced order types.
- **Average clearing price.** Simple but not a full auction design; it may not maximize welfare or match real venue rules.
- **No partial fills / order expiry / cancellation.** The paper's future work mentions these.
- **Servers must be online and coordinate.** The protocol is not fully asynchronous; it uses a global clock and synchronous rounds.
- **At least one honest server.** Dishonest-majority security requires this; full corruption loses privacy.
- **Deposit sizing.** Each server's deposit must cover the total client input value in the exchange (or a fraction in practice), which is capital-intensive.
- **Clients must transfer to burner addresses.** This is a usability and custody step; clients need a wallet and chain interaction.
- **Cross-chain assumptions.** Ledgers must support threshold signatures and publicly provable double-spend; not all chains qualify.
- **WAN scaling.** Round complexity and latency make large batches across continents slow.
- **No state channel.** The paper's future work proposes MPC state channels for high-frequency exchange.
- **No production deployment.** The implementation benchmarks the MPC component, not the full cross-chain system.
- **No economic analysis of server competition or fee setting.**
- **No privacy for unmatched order limits.** Unmatched orders are discarded without disclosing limits, but the fact of participation and batch membership may leak.
- **No formal treatment of perp-specific risks.** Oracle manipulation, funding, liquidation cascades, and cross-margin are out of scope.

## Hackathon preparation

### What this paper supports building

- A **private cross-chain order matcher** with server bonds and threshold custody.
- An **insured MPC clearinghouse**: servers post collateral, matching is publicly verifiable, cheating is slashed.
- A **privacy-preserving batch auction** for spot or perp intents.
- A **threshold-custody settlement layer**: burner addresses, threshold signatures, refund paths.
- A **client-side lightweight trading flow**: one transfer, secret-shared order, payout handled by servers.
- A **UC-style security argument** for a hackathon prototype: what is assumed, what is proven, what is trusted.

### What this paper does not support building directly

- A full perpetual exchange with funding and liquidation.
- A high-frequency continuous order book.
- A variable-quantity, price-time matching engine.
- A permissionless server set without collateral or incentives.

### Demo script (3 minutes)

1. Show the front-running problem on a public DEX.
2. Submit two cross-chain orders to P2DEX; show only secret shares and burner addresses on-chain.
3. Run MPC matching; show the matched pair and average clearing price without revealing unmatched limits.
4. Show threshold signing and payout; no single server can move funds.
5. Trigger a cheating server; show the proof, slashing, and client refund.
6. Show the optimistic path: two transactions, same as a centralized exchange.

### Judge Q&A prep

- **"How is this different from an atomic swap?"** Atomic swaps are pairwise and reveal terms; P2DEX matches multiple secret-shared orders and settles cross-ledger with threshold custody.
- **"What if all servers collude?"** Privacy fails, but the smart contract can still refund clients. The protocol's strong guarantee assumes at least one honest server.
- **"Why MPC instead of ZK?"** ZK alone needs a prover who sees the data; MPC distributes matching across non-colluding servers. Public verifiability prevents a server from lying about the result.
- **"Is it fast enough?"** For 128 orders, MPC is seconds on LAN; blockchain finality is the bottleneck. It is not a high-frequency engine.
- **"Can it support perps?"** Not directly. Perps need persistent positions, oracles, funding, margin, and liquidation. P2DEX is a one-shot cross-chain exchange.
- **"What is the trust assumption?"** At least one honest server, threshold signing, a smart contract on a ledger with sufficient consensus, and a public bulletin board.

### Claims to avoid

- "Fully trustless."
- "Supports perpetuals."
- "High-frequency."
- "No trust assumptions."
- "Production-ready."
- "Variable-size private orders."

## Hackathon-level idea: Bonded Shade

**One-liner:** An insured MPC clearinghouse for private perpetual intents, with server bonds, threshold custody, and optimistic settlement.

**User:** Traders who want private perp execution, and LPs/servers who earn fees for providing matching and settlement.

**Core flow:**

1. **Bonded server set:** a permissionless-ish set of matching servers posts collateral and registers a threshold key.
2. **Private intent:** trader submits collateral and a secret-shared perp intent (side, size, limit, leverage) plus a proof of collateral sufficiency.
3. **Private batch matching:** servers run publicly verifiable MPC to match intents and compute a uniform clearing price; unmatched intents never reveal their limits.
4. **Optimistic settlement:** on the happy path, only the settlement transactions land on-chain; positions are represented as commitments.
5. **Funding:** apply the reveal-correct-measure-pay rule; the batch's own price impact is corrected before the funding signal is measured.
6. **Liquidation:** threshold keepers prove underwaterness and settle; a challenge period and server bond cover errors.
7. **Cheating recovery:** if a server aborts or signs an invalid match, the transcript identifies it; its bond reimburses affected traders.
8. **Public risk proof:** publish aggregate OI, insurance coverage, and solvency without individual positions.

**Demo moment:** A server tries to tilt a match toward a colluding trader. The publicly verifiable transcript identifies the bad share, the bond is slashed, and the traders are refunded — all without revealing the order book. Then the happy path settles in two transactions.

**Moat:** The composition of insured MPC, threshold custody, private perp intents, and decryption-aware funding. The hard part is not the MPC; it is the risk engine around it.

**Cons:**

- MPC clearing is heavy for continuous perp markets.
- The server set and bonds reintroduce a permissioned/economic trust layer.
- Perp funding and liquidation add state that a one-shot MPC batch does not handle.
- Deposits must be large enough to cover client exposure; capital-intensive.
- WAN latency and cross-chain finality make it unsuitable for high-frequency trading.
- The matching algorithm must be extended from constant-size spot orders to variable-size perp positions.
- Public aggregate OI can still leak through time.
- The design is a research prototype; no production deployment or audit.

**Integration with the existing portfolio:** Bonded Shade is the private execution/clearing layer for Perp Forge. It borrows P2DEX's insured-MPC pattern, Rialto's batch fairness, ZKPerp's commitment/privacy model, and CipherClock's funding discipline.
