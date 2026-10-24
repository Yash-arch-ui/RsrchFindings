# On the Linkability of Zcash Transactions (2017)

## Metadata

- Author: Jeffrey Quesnelle, University of Michigan-Dearborn.
- Date: arXiv:1712.01210v1 [cs.CR], 4 December 2017.
- Venue: arXiv preprint (not a peer-reviewed venue in the local text).
- URL: https://arxiv.org/abs/1712.01210.
- Length: 5 pages including references and appendix (page markers in the local extraction).
- Type: empirical blockchain measurement and linkability heuristic.
- Read status: full local text read (`p06.txt`, 578 lines). Counts and tables below are from that text.

## One-paragraph summary

Quesnelle measures how optional shielding was actually used on the Zcash chain through block 196304 (mined 4 October 2017), then exploits the fact that the amounts entering and leaving the Sprout shielded pool are public. Only 19.6% of transactions contained a JoinSplit, only 1.9% of JoinSplits were fully shielded (no public shielding or de-shielding leg), and the shielded pool held about 3.5% of coins on average. Most coins that entered the pool later left it. The paper defines a round-trip transaction (RTT): a shielding transaction and a later de-shielding transaction with exactly equal public amounts, where that amount pair is globally unique in the chain. It finds 10,075 such pairs moving 919,220 coins, or 31.5% of the 2,911,734 coins that entered the pool. The match rate is circumstantial, not proof of common ownership, but the amount uniqueness, timing proximity, and concentration in large mining-pool payouts make the heuristic credible. It is a 2017 Sprout snapshot, not current Orchard data.

## Problem and threat model

Zcash is a Bitcoin fork with transparent addresses (t-addrs) and shielded addresses (z-addrs). Transparent-to-transparent transfers are fully public and linkable, like Bitcoin. Shielded transfers use zk-SNARKs to hide source, destination, and amount, but privacy is optional and only fully effective between two z-addrs. When coins enter or leave the shielded pool, the JoinSplit structure exposes `vpub_old` (coins entering) and `vpub_new` (coins leaving), so the amount at the boundary is public even though the z-address is not.

The threat model is a passive third party with the full public blockchain: no keys, no inside information, no network deanonymization. The adversary wants to link a transparent address that shields coins to a transparent address that later receives the same or nearly the same amount. The paper's claim is not that the zero-knowledge proof is broken. The claim is that metadata around the proof, especially public boundary amounts and timing, narrows the anonymity set.

The user behavior assumption is important. Because shielded transaction creation cost roughly 30 seconds to 1 minute on a contemporary desktop, and because many wallets and exchanges did not support z-addrs, users often treated the shielded pool as a mixing hop: t-addr to z-addr, then z-addr back to t-addr. That behavior is exactly what the heuristic targets.

## Architecture / protocol

The paper describes the Sprout-era JoinSplit. A JoinSplit has three essential fields: `vpub_old`, the amount entering the shielded pool; `vpub_new`, the amount leaving; and a zero-knowledge proof attesting to legitimacy. There are three operations:

- Shielding: t-addr to z-addr. `vpub_old` is non-zero; the amount is public, the z-addr is private.
- De-shielding: z-addr to t-addr. `vpub_new` is non-zero; the amount is public, the z-addr is private.
- Fully shielded: z-addr to z-addr. Both addresses and the amount are private.

The pool size is computed as a running sum of `vpub_old - vpub_new` per JoinSplit. This is the net differential into the shielded pool. The paper builds a relational database of blocks, transactions, and JoinSplits using a modified Bitcoin blockchain parser, then queries it.

The critical protocol fact for the attack is that a shielding transaction's input amount and a de-shielding transaction's output amount are both public. If they are equal, and no other JoinSplit in the entire chain has that same amount, the pair is a strong candidate for the same coins. The z-address in the middle does not prevent the boundary comparison.

## Section-by-section findings

Section I, introduction: frames the problem as optional anonymity. Transparent transactions are fully linkable; shielded transactions hide parties and amounts; but generating them is expensive and adoption is low. The paper previews the 31.5% result.

Section II, background: explains Bitcoin transparency, Zcash t-addrs and z-addrs, and the JoinSplit fields. It notes that even the original Bitcoin whitepaper accepted that linking remains possible, and cites prior Bitcoin anonymity studies.

Section III, shielded transactions: measures usage. Of all Zcash transactions, 19.6% (259,220) contain a JoinSplit; 40.2% of blocks contain no JoinSplit, so 80.4% of transactions are trivially linkable at least one hop. Of the 287,587 JoinSplits, only 1.9% (5,450) have no shielding or de-shielding leg; 40.6% (116,743) include shielding and 57.5% (165,394) include de-shielding. No JoinSplit in the dataset contained both a shielding and a de-shielding operation; the paper says it is unclear whether the software allows it. The shielded pool's final size is 4.3% of supply, with a per-block average of 3.5%. Fully shielded usage is rare and the pool is used mostly as an entry/exit hop.

Section IV, round-trip transactions: defines the RTT, gives the SQL query, reports 10,075 pairs and 919,220 coins, and argues the false-positive rate is low. It also relaxes the definition to fee-adjusted RTTs, where `vpub_new = vpub_old - f` for combinations of common fees, to catch users who made a shielded transfer in between. It reports 388 one-fee pairs (6,058 coins) and 485 two-fee pairs (4,411 coins).

Section V, discussion and conclusion: argues that optional privacy leads users to the path of least resistance. A user who wants a t-addr may shield and immediately de-shield, which does not defeat a determined analyst. The paper recommends a second fully shielded transaction after receiving coins and leaving some portion in the pool if returning to transparency. It highlights mining pools: pools that shield block rewards before paying miners' t-addrs are linked by the heuristic, revealing miner payout addresses.

The appendix lists sample RTT transaction IDs and amounts, including a 3,479.51898254 ZEC pair at 2 minutes, a 12.14981195 ZEC pair at 3 minutes, a 3.77326919 ZEC pair at 928 minutes, and a 220.01805591 ZEC pair at 15 minutes. It also gives a one-fee example: `vpub_old = 0.67209594`, `vpub_new = 0.67199594`, `f = 0.0001`, 8-minute difference.

## Key mechanisms and equations

Round-trip definition. Let `j1` and `j2` be JoinSplits in blocks of height `h1` and `h2` with `h2 > h1`. If exactly one transaction pair `(t1, t2)` has `j1 in t1`, `j2 in t2`, and

`vpub_new(j2) = vpub_old(j1)`,

then `(t1, t2)` is an RTT. The "exactly one pair" condition is global: if the amount appears more than once later, the pair is excluded as ambiguous. De-shielding must come later than shielding.

Pool accounting. Each JoinSplit contributes `vpub_old - vpub_new` to the pool; the pool size is the running sum of these differentials. This produces the 3.5% average and 4.3% final share.

Fee-adjusted RTT. If the user made shielded transfers in between, fees may be paid from the shielded balance, so the paper relaxes to

`vpub_new = vpub_old - f`,

where `f` is a combination of common fees. One-fee RTTs target `t-addr -> z-addr -> z-addr -> t-addr`; two-fee RTTs target two shielded transfers and two fees. Only the five most common fees and matches within 24 hours are used, and two-fee combinations that sum to a common fee (for example `0.0001 + 0.0001 = 0.0002`) are excluded.

SQL heuristic. The query selects JoinSplits `G` with `G.vpub_old > 0`, matches `G.vpub_old = H.vpub_new` with `H.Block > G.Block`, groups by `G.vpub_old`, and keeps groups with `COUNT(G.Id) = 1`; it joins back to transactions `E` and `F`. See Figure 4 of the paper.

Amount granularity. Zcash is divisible to 8 decimal places. An exact high-precision match with global uniqueness is argued to be unlikely to occur by chance. This is probabilistic, not cryptographic.

## Experiments / evaluation

Dataset: the Zcash blockchain through block 196304, mined 4 October 2017. The unit is the whole coin in some totals; the paper notes that only the whole-number portion is reported for coin totals.

Usage results:
- 19.6% (259,220) of all transactions contain a JoinSplit.
- 40.2% of blocks contain no JoinSplit.
- 80.4% of transactions are trivially linkable at least one transaction back.
- 287,587 JoinSplits total.
- 1.9% (5,450) are fully shielded; 40.6% (116,743) include shielding; 57.5% (165,394) include de-shielding.
- Average shielded-pool share is 3.5%; final share is 4.3%.

RTT results:
- 10,075 transaction pairs, 919,220 coins.
- 2,911,734 coins entered the shielded pool in total.
- Therefore 31.5% of entering coins are in an RTT.
- 96% (9,673) of matches are within two hours; 402 are more than 24 hours apart.
- Time buckets: [0,5) 1,373 pairs / 156,237 coins; [5,15) 5,022 / 421,021; [15,30) 1,479 / 147,546; [30,60) 1,015 / 95,034; [60,120) 500 / 35,741; [120,1440) 284 / 60,518; [1440, infinity) 402 / 3,120.
- Large denominations dominate: of the top 250 JoinSplits by shielding amount, 236 are RTTs; top 10: 10 RTTs / 34,153 coins; top 50: 49 / 143,924; top 500: 460 / 765,212; top 1,000: 585 / 834,301.

Fee results:
- 98.6% of non-coinbase transactions include a fee.
- Most common fee: 0.0001 ZEC, used by 46.40% (523,036) of transactions; then 0.001 (3.03%), 0.0002 (2.99%), 0.00009 (2.70%), 0.00005 (2.14%), 0.00000226 (2.10%), and zero (1.43%).
- One-fee RTTs: 388 pairs, 6,058 coins.
- Two-fee RTTs: 485 pairs, 4,411 coins.

There is no ground truth and no precision/recall measurement. The evaluation argument is internal: exact amount, global uniqueness, and temporal proximity. The 96% within-two-hours statistic is the main circumstantial support.

## Paper-specific findings

1. Optional shielding was barely used in 2017. Only 19.6% of transactions contained a JoinSplit, and only 1.9% of JoinSplits were fully shielded.
2. The pool was small: about 3.5% of coins on average and 4.3% at the final block, so most shielded activity was entry and exit.
3. Boundary amounts leak. Since `vpub_old` and `vpub_new` are public, an analyst can compare shield and de-shield amounts without breaking the zk-SNARK.
4. An exact-amount, globally-unique, time-ordered heuristic matched 31.5% of coins entering the pool. This is a candidate set, not a proven ownership graph.
5. Timing strengthens the inference: 96% of matches occurred within two hours, many within 15 minutes.
6. Large transfers are especially exposed. Of the top 250 JoinSplits by amount, 236 were RTTs because mining pools used shield-then-distribute payouts.
7. Mining-pool payouts are a concrete privacy failure. Miners receiving from a z-addr may believe the source is hidden, but the round trip reveals payout t-addrs.
8. Fee-adjusted matching finds additional low-volume patterns (388 one-fee pairs, 485 two-fee pairs), so one intermediate shielded transfer does not necessarily break the heuristic.
9. The mitigation advice is behavioral: add a fully shielded hop and leave a remainder in the pool. It is not a protocol fix or a guarantee.
10. The result is a 2017 Sprout measurement, not a current Orchard statistic, but boundary-amount and timing leakage remains a useful analytical lens.

## Limitations and caveats

- The heuristic is circumstantial. The paper says not all RTTs are truly linked; an exact amount match can be coincidental. There is no ground truth, so 31.5% is a match rate, not a proven linkage rate.
- The "exactly one pair" rule excludes ambiguous amounts, which may undercount true round trips, but a single coincidental unique match is still treated as linked.
- JoinSplits can contain multiple shielded operations, and the paper says there is no upper bound on shielded transactions. Classification by presence of shielding/de-shielding is not a volume measurement.
- The data predates Sapling (2018) and Orchard (2022). JoinSplit is a Sprout structure, and modern formats, wallet behavior, exchange support, and pool sizes differ.
- The heuristic catches equal or fee-adjusted round trips. It misses splitting, merging, change, multiple hops, non-equal returns, and fully shielded activity that never touches transparency. It is not a complete linkability measure.
- The mining-pool finding is a case study, not a claim that all pools or miners are identifiable.
- The cost figure (30 seconds to 1 minute per shielded transaction) is hardware- and era-specific; the 3.5% and 4.3% shares are whole-coin 2017 snapshots.

## Hackathon hooks

What this enables: a modern "boundary leakage" analyzer for Zcash. Ingest blocks, extract transparent-to-shielded and shielded-to-transparent value movements, match exact and fee-adjusted amounts, bucket by time, and visualize candidate round trips. The same logic can power a wallet warning: "this shield/de-shield pattern is a likely round trip; add a shielded hop, change the amount, or leave a remainder." It also enables historical replication: compare Sprout, Sapling, and Orchard eras on the same heuristic to show how usage and wallet UX changed.

What this does not enable: it does not deanonymize fully shielded z-to-z transfers, prove common ownership, or identify users. It does not produce current Zcash statistics without a current dataset. It does not break zk-SNARKs. It should not be used as a compliance verdict without corroborating evidence.

Demo ideas:
- "RoundTrip Radar": a dashboard over recent Zcash blocks with exact and fee-adjusted matching, a time-delta histogram, and a per-transaction privacy-risk explanation. Use the 2017 paper as the baseline and clearly label the dataset year.
- "Shielded hop simulator": let a user choose shield amount, delay, intermediate z-to-z hops, and change; show when the heuristic still matches and when it stops. This makes the paper's mitigation advice tangible.
- "PCZT privacy lint": integrate a round-trip warning into an offline signer flow, alongside the Orchard audit's ciphertext-authentication check. The signer can show both "is this note semantically what I approved?" and "does this transparent boundary pattern look linkable?"

## Claims to avoid

- "31.5% of Zcash transactions were deanonymized." The paper matched 31.5% of coins entering the pool under a heuristic, not 31.5% of transactions or users.
- "31.5% are proven linked." The paper says the matches are circumstantial and may include false positives.
- "Zcash's cryptography is broken." The attack uses public boundary amounts and timing, not a break of the zk-SNARK.
- "Fully shielded transactions leak amounts." They do not; the leak is at shielding and de-shielding boundaries.
- "These numbers describe Orchard today." They describe Sprout through block 196304 in October 2017.
- "The heuristic catches all linkability." It misses many patterns and does not measure fully shielded activity.
- "All mining pools are identifiable this way." The paper reports a case study of large pool payouts, not a universal claim.
- "A second shielded hop is a guaranteed fix." It raises cost; it is not a formal privacy guarantee.
