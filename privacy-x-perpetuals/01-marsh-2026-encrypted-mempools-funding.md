# Reveal, Correct, Then Pay: Encrypted Mempools and Perpetual Funding Security

## Metadata

- **Author:** Benjamin Marsh (Sei Labs and University of Portsmouth)
- **Date:** 15 July 2026
- **Identifier:** arXiv:2607.13832v1 [cs.CR]
- **Length:** 18 pages
- **Type:** Theory / market design / security economics. No empirics and no production system.
- **Read status:** Full main text read, including the model, propositions, proof sketches, numerical table, and conclusion.

## One-paragraph summary

Encrypted mempools hide transactions until ordering is fixed, which protects against victim-dependent MEV. Marsh shows that this same ordering barrier can *increase* a different attack: self-authored state manipulation of a perpetual funding signal. The attacker knows its own transaction, so encryption does not blind it much; but it does prevent adaptive arbitrageurs from submitting a corrective transaction into the already-committed batch. This creates a **reaction gap** `delta`, during which the distorted funding signal persists. The paper microfounds correction as executable arbitrage with local price impact and inventory cost, derives a correction rate `rho`, and shows how the gap multiplies the distortion entering a funding window. It then adds a second channel: privacy can suppress capitalization of predictable funding into entry prices, making it cheap to create receiving notional before the payment. The result is a **two-ledger security index** that decomposes the effect of encryption into attacker blindness, correction shielding, and capitalization shielding. The design fix is **reveal, correct, measure, then pay**: insert a post-reveal correction/clearing stage, measure funding after the transient, and align position eligibility with the public measurement window.

## The core model

### Commit, reveal, execute, react

A batch `B` closes admission at `t_c`, reveals plaintexts at `t_r >= t_c`, and executes. The paper focuses on the common design in which no new transaction can enter `B` after `t_c`.

**Definition 1 (adaptive corrective transaction).** Given revealed transaction `x`, a corrective transaction `c(x)` is one whose payload or execution parameters depend nontrivially on the plaintext or execution effect of `x`.

**Lemma 1 (ordering barrier).** If `B`'s transaction set and order are fixed before `x` is revealed, and there is neither post-commit admission nor a precommitted contingent program implementing `c(x)`, then `c(x)` cannot execute in `B`; its earliest execution is in a later batch or reaction stage.

The proof is scheduling, not cryptography: when `c(x)` can first be computed, admission is closed. Faster decryption reduces `t_r - t_c` but does not reopen the batch. This is the paper's central distinction between **cryptographic latency** and **economic reaction latency**.

### Funding as a two-ledger transfer

Let `y >= 0` be the initial size of the signal distortion. Let `Z_t` be the adversarial component of the funding signal relative to a no-attack path, with `Z_0 = y`, and let `z_t = E[Z_t | Z_0 = y]`. Funding measures the average distortion over a window `W`:

```text
z_bar_W = (1/W) * integral_0^W z_t dt
```

The expected per-unit funding transfer is `tau * z_bar_W`, where `tau > 0` is local signal-to-payment pass-through. Caps and position limits bound the scale but do not change the local comparative statics.

Receiving notional has two forms:

- `N_0 >= 0`: **endowed notional**, held before the hidden transaction, paying no event-specific entry premium.
- `N >= 0`: **created notional**, opened close enough to the event to exploit the predictable transfer, potentially paying an adverse entry price.

This is the **two-ledger** structure: the signal ledger determines the rate; the notional ledger determines the dollar base. Protecting only one leaves the other exposed.

Let `a y^2` be the local all-in cost of creating the distortion, and let `m in [0,1]` be the fraction of targeting ability retained under privacy. A self-authored attacker can keep `m` close to one because it knows its own order and the external reference price. A victim-dependent attacker may have `m` near zero.

## The reaction-gap channel

### Correction from executable arbitrage

A corrector of type `i` can reduce distortion `Z` by order size `x`, with local state impact `ell`, and pays quadratic cost `k_i x^2 / 2`. The gross value of the discrepancy is:

```text
integral_0^x (Z - ell u) du = Z x - (ell/2) x^2
```

The optimal size and fraction of distortion removed are:

```text
x_i*(Z) = Z / (ell + k_i)
theta_i = ell / (ell + k_i) in (0, 1]
```

Opportunities for type `i` arrive as a Poisson process with intensity `nu_i`. In stage `s in {c, p}`, an opportunity is actionable with probability `alpha_i,s`. Starting from `Z_0 = y`:

```text
E[Z_t | Z_0 = y] = y e^{-rho_s t}
rho_s = sum_i nu_i alpha_i,s theta_i
```

This is **Proposition 1**. Since later actionability dominates closed-stage actionability, `rho_p >= rho_c`.

### Ordering-induced rate gap

Partition correctors into precommitted types `C` and ordinary adaptive types `A`. Let `q_i in [0,1]` be closed-stage coverage for `i in C`; ordinary adaptive types have zero closed-stage actionability. Normalize later actionability to one:

```text
rho_c = sum_{i in C} nu_i q_i theta_i
rho_p = sum_{i in C} nu_i theta_i + sum_{i in A} nu_i theta_i
rho_p - rho_c = sum_{i in C} nu_i (1 - q_i) theta_i + sum_{i in A} nu_i theta_i
```

Thus `rho_p > rho_c` whenever some precommitted coverage is incomplete or ordinary adaptive correction has positive capacity. In the homogeneous case, `rho_c / rho_p = q * nu_C / (nu_C + nu_A)`: coverage and adaptive capacity are separate.

### The induced two-stage response

For a one-shot impulse:

```text
z_t = y e^{-rho_c t},                         0 <= t < delta
z_t = y e^{-rho_c delta} e^{-rho_p (t-delta)}, delta <= t <= W
```

Define:

```text
A(rho, s) = (1 - e^{-rho s}) / rho,  rho > 0
A(rho, s) = s,                      rho = 0
```

Then the expected average distortion is `z_bar_W = g(delta; rho_c, rho_p, W) y`, where:

```text
g(delta) = [ A(rho_c, delta) + e^{-rho_c delta} A(rho_p, W - delta) ] / W
```

**Proposition 2:** under `0 <= delta < W` and `rho_p > 0`:

```text
dg/ddelta = (e^{-rho_c delta} / W) * (1 - rho_c/rho_p) * (1 - e^{-rho_p (W-delta)})
```

So `g` is weakly increasing in the reaction gap, and strictly increasing exactly when some correction capacity becomes more actionable after reveal.

For the transparent same-batch benchmark, `g_pub = A(rho_p, W) / W`. The encrypted design's ratio is at least one:

```text
g_enc / g_pub = [ A(rho_c, delta) + e^{-rho_c delta} A(rho_p, W-delta) ] / A(rho_p, W) >= 1
```

With no effective closed-stage correction, `rho_c = 0`, `rho := rho_p`, and:

```text
g(delta; 0, rho, W) = [ delta + (1 - e^{-rho(W-delta)}) / rho ] / W
R_g(delta) = [ rho delta + 1 - e^{-rho(W-delta)} ] / [ 1 - e^{-rho W} ]
```

If `rho W -> infinity` and `rho(W-delta) -> infinity`, then `R_g(delta) = 1 + rho delta + o(1)`. A short absolute gap can have a large proportional effect when correction would otherwise be fast.

### Endowed notional and the information threshold

For endowed receiving notional `N_0`:

```text
Pi_0(y) = m N_0 tau g y - a y^2
y* = m N_0 tau g / (2a)
V_0 = m^2 N_0^2 tau^2 g^2 / (4a)
```

Comparing encrypted and public regimes with unchanged costs and pass-through:

```text
V_0,enc / V_0,pub = (m_enc tau_enc g_enc / (m_pub tau_pub g_pub))^2
V_0,enc > V_0,pub  <=>  m_enc / m_pub > g_pub / g_enc
```

There is no unconditional "privacy improves manipulation security" theorem. Privacy helps only when the attacker's lost targeting ability dominates the persistence gained from weaker correction.

**Numerical illustration in the paper:** `rho W = 8`, no closed-stage correction, `delta/W = 0.1`. Then `R_g ~= 1.80`, so attack value rises by about `3.24x` when targeting is unchanged. Encryption still raises value whenever the attacker retains more than `1/1.80 ~= 56%` of its public targeting ability.

## The tax-base channel

### Visibility and capitalization

Let the predictable expected incremental funding payment per unit of receiving notional be:

```text
p = q_0 y
q_0 = m tau g
```

Let `e` be the adverse entry premium paid by a trader entering the receiving side before the funding event. Let `v in [0,1]` be effective visibility: `v = 1` means competing traders can condition entry on the pending manipulation; `v = 0` means no transaction-specific entry is possible before the batch closes.

Competing sniping demand and passive entry supply:

```text
D(e; p) = v kappa_M (p - e)^+
S(e) = kappa_L e
```

For `p > 0`, the entry market clears at:

```text
e* = zeta(v) p
zeta(v) = v kappa_M / (v kappa_M + kappa_L)
```

`zeta` is increasing in visibility and responsive capital, and decreasing in entry depth. A newly entered receiver retains only `(1 - zeta) p` before residual costs.

### Two-ledger security index

For created notional `N`, let `b N^2` summarize acquisition, carry, adverse self-impact, and unwind cost. With `q := (1 - zeta) q_0`:

```text
Pi(N, y) = q_0 N_0 y + q N y - a y^2 - b N^2
Gamma := q^2 / (4ab) = (1-zeta)^2 m^2 tau^2 g^2 / (4ab)
```

**Theorem 1.** If `Gamma < 1`:

```text
y* = 2 b q_0 N_0 / (4ab - q^2)
N* = q q_0 N_0 / (4ab - q^2)
V = q_0^2 N_0^2 / [4a (1 - Gamma)]
```

If `Gamma > 1`, the quadratic model has a positive homogeneous attack direction and is locally scalable until a cap, budget, position limit, or global impact curve binds. At `Gamma = 1`, the quadratic vanishes along a nonzero ray; endowed notional creates unbounded linear gain along that ray if `q_0 N_0 > 0`.

For two regimes with the same `a`, `b`, and `tau`:

```text
Gamma_enc / Gamma_pub =
  (m_enc / m_pub)^2          [attacker blindness]
  * (g_enc / g_pub)^2        [correction shielding]
  * ((1-zeta_enc)/(1-zeta_pub))^2  [capitalization shielding]
```

This is the paper's central composition result. Encryption can reduce the first term while increasing the second and third. For local tax-base scalability, encryption is harmful exactly when:

```text
m_enc / m_pub > (g_pub / g_enc) * ((1 - zeta_pub)/(1 - zeta_enc))
```

The capitalization channel can make this threshold much lower than the endowed-notional threshold. **Example in the paper:** if public visibility capitalizes 80% of predictable funding, encrypted same-batch entry capitalizes none, and `R_g = 1.8`, then `Gamma_enc > Gamma_pub` whenever the attacker retains more than roughly 11% of its public information effectiveness.

## The design fix: reveal, correct, measure, then pay

### Sequence

1. **Order and reveal.** Commit the encrypted batch, reveal transactions, execute or simulate the state transition.
2. **Correct.** Permit a public reaction period of length `L`, a reaction auction, or a protocol-native rebalancing step that can condition on the revealed state.
3. **Measure.** Start the funding signal window only after correction, or exclude the reveal-induced transient.
4. **Pay on an aligned tax base.** Positions opened in the concealed batch do not receive the imminent event; alternatively, receiving notional accrues continuously over the same post-reveal window used to measure the signal.

### Correction buffer bound

Suppose residual distortion at reveal is `y`, and its conditional mean satisfies `z_t <= y e^{-rho t}` after reveal. Wait `L`, then measure over `W`:

```text
z_bar_{L,W} <= y h(L; rho, W)
h(L; rho, W) := e^{-rho L} (1 - e^{-rho W}) / (rho W)
```

**Theorem 2 (decryption-aware dollar bound).** Under local creation cost `a y^2`, decay rate at least `rho`, pass-through at most `tau`, endowed notional at most `N`, and concealed-batch positions ineligible for the current event:

```text
epsilon_priv(L) = (m^2 N^2 tau^2 / (4a)) e^{-2 rho L} ((1 - e^{-rho W})/(rho W))^2
```

For every `epsilon > 0`, a sufficient buffer is:

```text
L >= (1/(2 rho)) * [ log( (m^2 N^2 tau^2 / (4a epsilon)) * ((1 - e^{-rho W})/(rho W))^2 ) ]^+
```

This bounds the **incremental transfer from the concealed batch**, not all post-reveal manipulation. Public trading after reveal is ordinary oracle-manipulation territory.

### Why both controls are needed

| Design | Signal ledger | Tax-base ledger |
|---|---|---|
| Immediate post-reveal measurement | Preserves reaction-gap distortion | Concealed entrants may receive the event |
| Correction buffer only | Reduces `g` or `h(L)` | Final-timestamp entry can still be cheap |
| Eligibility lag only | Removes same-batch created notional | Endowed notional still profits from uncorrected signal |
| Reveal-correct-measure-pay | Adaptive arbitrage acts before measurement | Excludes concealed entrants or accrues notional over the public window |
| Exogenous non-manipulable oracle | Removes/weakens the self-authored signal channel | Tax-base timing still matters for predictable transfers |

The signal and tax-base controls are complements, not substitutes.

## Paper-specific findings

1. **Encrypted mempools are not universally manipulation-reducing.** For victim-dependent MEV they can be strongly protective; for self-authored funding-signal manipulation they can increase attack value by shielding correction.
2. **Decryption speed is not the relevant latency.** The earliest stage at which a transaction contingent on revealed state can execute is the security-relevant variable.
3. **Funding is a two-ledger system.** The rate and the receiving base are separate attack surfaces. Hiding the signal trade but leaving timestamp entry open is insufficient.
4. **Protocol-native correction is especially valuable under privacy.** A precommitted arbitrage module, solver auction, or rebalancing hook moves correction capacity from adaptive to closed-stage coverage.
5. **Permutation lowers attacker targeting `m`; it does not restore adaptive correction.** Reveal-then-clear lowers `g` and raises effective visibility `v`. The controls are complementary.
6. **Long funding windows do not mechanically remove the risk.** The relevant dimensionless quantity in the fast-correction regime is approximately `rho delta`, not merely `delta/W`.
7. **Dollar security requires concentration limits.** Attack values scale with `N_0^2`; rate caps bound basis points but not the dollar transfer.
8. **The practical rule is simple:** do not reveal and immediately pay against the revealed state.

## Limitations and caveats

- **One-shot impulse, linear-quadratic correction.** The model uses constant impact, constant inventory cost, Poisson opportunities, and a one-shot distortion. It is a local comparative-statics model, not a full dynamic equilibrium.
- **No strategic attacker response to the rule.** The attacker optimizes `y` and `N` in a static payoff; it does not adapt to the buffer, eligibility rule, or repeated-game reputation.
- **The correction sector is stylized.** Fixed costs, non-nested technologies, finite balance sheets, and competition among correctors can change the constant-rate law.
- **`m` is unobservable.** The information-retention parameter is a modeling device. Calibrating it for a real encrypted mempool is an open measurement problem.
- **No empirical venue calibration.** The numerical examples are dimensionless illustrations, not Binance/dYdX estimates.
- **Caps and funding-rule nonlinearities are only bounded, not modeled.** The paper works inside a favorable sign branch and a rate cap; it does not model the piecewise funding template.
- **The oracle may be exogenous.** If the funding signal is a deep external oracle that the hidden venue transaction barely moves, the channel is weak.
- **No full protocol implementation.** The design fix is a mechanism, not a deployed threshold-encryption system.
- **The paper assumes encryption works as intended.** It studies composition with the application, not cryptographic failures, censorship, or decryption-key compromise.

## Hackathon preparation

### What this paper supports building

- A **reaction-gap simulator**: input funding window, correction intensity, closed-stage coverage, and attacker retention; output `g`, `R_g`, attack value, and critical `m`.
- A **privacy-aware funding clock**: a contract/module that snapshots eligibility before the batch, delays measurement by `L`, and accrues notional over the public window.
- A **mempool design auditor**: compute the earliest adaptive execution stage for a given commit-reveal design, including precommitted contingent programs.
- A **two-ledger risk dashboard**: separate signal-ledger and tax-base-ledger exposure, with concentration limits and cap analysis.
- A **proof-carrying funding spec**: publish the funding rule, eligibility rule, buffer, and leakage assumptions as a machine-readable artifact.

### What this paper does not support building directly

- A production encrypted mempool or threshold-decryption network.
- A live perp exchange with liquidity.
- A claim that a specific venue's funding is being manipulated; that requires data and identification.
- A universal privacy token or mixnet.

### Demo script (3 minutes)

1. Show a transparent perp: attacker distorts the mark, arbitrage corrects in the same batch, funding transfer is small.
2. Switch on commit-reveal encryption: the same attack now gets a one-batch reaction gap. Show `g` and `R_g` rising.
3. Add tax-base entry: show public entrants capitalize the predictable funding, then show encrypted entrants paying no premium.
4. Trigger the two-ledger index: show `Gamma > 1` and the attack scaling.
5. Apply reveal-correct-measure-pay: show the buffer and eligibility lag reduce `epsilon_priv` below a target.
6. Export the funding spec and deploy a testnet contract that enforces the eligibility snapshot.

### Judge Q&A prep

- **"Doesn't encryption prevent front-running?"** It prevents victim-dependent front-running, but it can shield a self-authored state manipulation from correctors. The attack does not need to see the victim.
- **"Why not just decrypt faster?"** Faster decryption shrinks wall-clock latency but does not reopen a closed batch. The reaction gap is a scheduling property.
- **"Why not just lengthen the funding window?"** A small `delta/W` can still matter when `rho W` is large; `rho delta` is the relevant scale.
- **"Is this an argument against privacy?"** No. It is an argument against composing privacy with an application that pays against the first post-reveal state. The fix preserves privacy and adds a correction/eligibility stage.
- **"Can you measure `m`?"** Not directly in the hackathon. Show a sensitivity analysis and label it as an assumption.
- **"What is the product?"** A funding-security module and simulator for privacy-preserving perp venues, not a new token.

### Claims to avoid

- "Encrypted mempools increase all MEV."
- "Privacy is bad for perps."
- "A rate cap solves the attack."
- "The numbers are calibrated to a real venue."
- "The model covers repeated games, strategic liquidity, or full funding-rule nonlinearities."

## Hackathon-level idea: CipherClock

**One-liner:** A decryption-aware funding clock and attack simulator for privacy-preserving perpetuals.

**User:** Perp DEX designers, encrypted-mempool teams, risk labs, and market supervisors.

**Core flow:**

1. Ingest a venue's batch/decryption schedule, funding window, cap, and eligibility rule.
2. Estimate or parameterize correction intensity `rho_c`, `rho_p` and attacker retention `m`.
3. Compute `g`, `R_g`, endowed and created-notional attack values, and the two-ledger index `Gamma`.
4. Recommend a correction buffer `L`, an eligibility snapshot, and a notional concentration limit.
5. Generate a Solidity/Noir/Aleo-style funding module that enforces the chosen rule.
6. Publish a machine-readable privacy/funding spec with assumptions and sensitivity bounds.

**Demo moment:** Two identical perps, one with immediate post-reveal payment and one with reveal-correct-measure-pay. The attacker's profit collapses in the second, while the privacy guarantee is unchanged.

**Moat:** The reaction-gap calibration, the two-ledger attack model, and a library of composable funding-rule templates.

**Cons:**

- `m` and correction intensities are hard to calibrate credibly.
- The model is one-shot and local; a real attacker can adapt.
- The product is a simulator plus a small contract, not a full exchange.
- Venues may resist an eligibility lag because it changes user experience.
- The attack narrative is subtle for judges who expect "privacy = good."

**Integration with the existing portfolio:** CipherClock is the funding-security layer for Perp Forge and the privacy counterpart to Funding DNA. Perp Forge designs the payoff; CipherClock protects the funding window; TailMark prices the liquidation tail; TierBridge handles execution.
