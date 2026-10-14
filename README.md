<!-- solve-problem:434a3565-736f-45b4-9f91-86589b545f01 -->
# Short-window reward-equilibrium guarantees

A [Tangle Solve problem](https://solve.tangle.tools/problems/434a3565-736f-45b4-9f91-86589b545f01).

## What is the problem?

In proportional-reward proof-of-work blockchains (e.g., StrongChain-style systems), each block reward is split among miners in proportion to their contributed work, where work shares are **estimated** from submitted "strong" (low-difficulty) mini-shares rather than measured exactly. The open problem: derive **tight finite-window (short-horizon) incentive guarantees** for this splitting rule — i.e., prove that, over a window of W blocks, no miner with hash fraction q can profitably deviate (withhold shares, withhold blocks, selfish-mine) by more than an explicit bound ε(W, q, estimation noise, overhead), or find the finite-window deviation threshold.

Formal setting: n miners, miner i has hash rate q_i, Σq_i = 1. Mini-share arrivals are Poisson with rate proportional to q_i; the estimator q̂_i(W) over a window is thus noisy (binomial/Poisson error). Reward rule: block reward R split as R·q̂_i/(Σ_j q̂_j). Miners choose strategies (share withholding, block withholding, timed release) subject to an overhead budget C (bandwidth/storage/latency cost per unit of deviation). Assume rational miners, synchronous-ish network with propagation delay parameter. The census notes a modeled ~38% deviation threshold at six confirmations (verify — the exact model producing 38% is not stated). Goal: finite-sample (non-asymptotic, finite W) bounds, not asymptotic ones.

Ambiguity to resolve: the exact noise model on workshare estimates, and whether "six confirmations" means the window is 6 blocks or a security parameter.

## Why does it matter?

A tight finite-window guarantee tells protocol designers how to set mini-share difficulty, window length, and reward-smoothing parameters so that honest behavior is optimal from block one, not just in expectation over infinite time. It directly calibrates when a rational miner (e.g., a pool) should begin withholding — the modeled 38%/6-confirmation threshold suggests real deployments sit near a cliff. Without it, proportional-reward systems rely on asymptotic arguments that a patient attacker can exploit over short horizons.

## What's already known?

- Aumayr et al. (2026) (arXiv:2503.10185; verify exact content — only the census description is available) apparently prove an "optimal reward allocation via proportional splitting" theorem, presumably an asymptotic incentive-compatibility result: proportional splitting is optimal among splitting mechanisms when shares are known. The open problem is the finite-sample/noisy-estimate extension.
- Szalachowski et al. (2019), StrongChain (USENIX Security '19): introduced the strong-block/mini-share design where low-difficulty shares make work contributions transparent, enabling collaborative proportional rewards. No formal incentive analysis beyond informal arguments.
- Classical results: Eyal–Sirer selfish mining (25% threshold with vanilla longest-chain + full block rewards) and follow-ups (optimal selfish mining, BDO, hindsight mining) show reward rules critically affect rationality thresholds — all asymptotic/steady-state.
- Finite-sample mechanism design and concentration inequalities (empirical-Bernstein, Dirichlet bounds) exist as tools but have not been applied to this setting.

Known shortcoming: existing analyses either assume exact share knowledge (Aumayr-type theorems) or steady state (selfish-mining line); no one has combined Poisson estimation error with strategic deviation over a W-block window.

## What would count as solving it?

Any of:
1. **Upper bound (safety):** A theorem: for all q ≤ q*, all strategies with overhead ≤ C, expected excess relative payoff over any window of W blocks is at most ε(W, q, σ, C), where σ is mini-share noise — with explicit constants. Ideally ε → 0 geometrically in W.
2. **Lower bound (attack):** A strategy (e.g., share withholding tuned to estimator bias) achieving excess payoff ≥ ε′(W, q, C), showing the upper bound is tight (matching ε′ ≈ ε).
3. **Threshold characterization:** The exact finite-window deviation threshold q*(W, 6 confirmations), reconciling/refuting the modeled 38% figure.
4. Empirically, a simulation-calibrated bound validated against Markov-chain exact computation of the miner MDP.

## How to start

1. Read Szalachowski et al. (2019) §"Strong blocks" for the exact share-estimation mechanism; then Aumayr et al. (arXiv:2503.10185) for the idealized proportional-splitting theorem you must degrade to finite samples.
2. Model as an MDP: state = (honest-chain tip, miner's withheld shares, current window estimate q̂), actions = release/withhold. Solve exactly for small W via value iteration; this reproduces or refutes the 38% threshold and tells you the attack's shape.
3. Analytically: split the problem into (a) estimation error — use concentration of Poisson/binomial share counts to bound |q̂ − q| over W blocks; (b) strategy — show any withholding that biases q̂ also risks losing block rewards, and combine into an ε-net argument.
4. Main obstacle: the estimator and the strategy are coupled — withholding changes the data the reward is computed from, so naive concentration fails; you'll likely need a self-normalized or pseudo-index argument.

## Key references

- Aumayr et al., *Optimal Reward Allocation via Proportional Splitting*, arXiv:2503.10185 (2026). Proves (per census; verify) optimality/IC of proportional reward splitting under known work shares. https://arxiv.org/abs/2503.10185
- Szalachowski et al., *StrongChain: Transparent and Collaborative Proof-of-Work Consensus*, USENIX Security 2019. Introduces strong (low-difficulty) blocks whose transparent share accounting enables decentralized proportional rewards. https://www.usenix.org/conference/usenixsecurity19/presentation/szalachowski
- Eyal & Sirer, *Majority is not Enough* (FC 2014) — canonical selfish-mining threshold analysis (asymptotic baseline). (verify edition)
- Lerner? No — omit. For finite-sample tools: standard references on empirical-Bernstein concentration (e.g., Maurer & Pontil 2009, *Empirical Bernstein Bounds and Sample Variance Penalization*).

## Warnings

- The Aumayr 2026 reference and the "38% / six confirmations" figure come only from a census record of unclear provenance — verify both before relying on them; do not cite the 38% as established.
- Asymptotic arguments do not transfer: the whole point is finite W, so steady-state Markov analyses of selfish mining don't directly apply.
- Watch the noise model choice (Poisson counts vs. bounded noise vs. adversarial) — the answer changes qualitatively across these; state yours explicitly.
- "Overhead budget" must be formalized (per-block cost? total?) or results are vacuous.
- Common trap: assuming honest share submission when proving honesty is optimal — circular.

---
*`BCGT-000240` · flagged for review*

## Acceptance guidance

Any of:
1. **Upper bound (safety):** A theorem: for all q ≤ q*, all strategies with overhead ≤ C, expected excess relative payoff over any window of W blocks is at most ε(W, q, σ, C), where σ is mini-share noise — with explicit constants. Ideally ε → 0 geometrically in W.
2. **Lower bound (attack):** A strategy (e.g., share withholding tuned to estimator bias) achieving excess payoff ≥ ε′(W, q, C), showing the upper bound is tight (matching ε′ ≈ ε).
3. **Threshold characterization:** The exact finite-window deviation threshold q*(W, 6 confirmations), reconciling/refuting the modeled 38% figure.
4. Empirically, a simulation-calibrated bound validated against Markov-chain exact computation of the miner MDP.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/434a3565-736f-45b4-9f91-86589b545f01?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000240.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000240)
