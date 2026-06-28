# Key Findings

Curated summary of the 21 numbered findings from the parameter sweep program. Each finding is referenced in the paper; the full technical record with supporting data, sweep citations, and tables is in [sweeps/SWEEP_FINDINGS.md](sweeps/SWEEP_FINDINGS.md).

---

## The Two-Scale Structure

The central result of this study is that Bitcoin fork outcomes are governed by two nested scales operating through different mechanisms.

**At the global scale**, `economic_split` (fraction of BTC custody supporting the new rules) is the dominant predictor (60% Random Forest feature importance on the full 60-node network). It determines whether a scenario even reaches the contested zone. Below E ≈ 0.50, v27 fails structurally. Above E ≈ 0.82, v27 wins regardless of mining behavior.

**Within the contested zone** (E ∈ [0.50, 0.82]), `pool_committed_split` (fraction of mining hashrate ideologically committed to v27) becomes the decisive parameter. The mechanism is the 2016-block difficulty retarget: whichever chain accumulates 2016 blocks first fires a difficulty adjustment that makes its blocks 2–3× more profitable, cascading all remaining pools.

---

## Findings by Category

### Economic Conditions

**F1 — pool_committed_split dominates at 2016-block retarget; economic_split dominates at 144-block**  
At 2016-block retarget, pool_committed_split is the dominant predictor (52.8% RF importance on full multi-sweep dataset). At 144-block retarget, economic_split dominates at 77.2%. The two parameters swap rank positions entirely between retarget regimes. This is the core regime comparison finding.

**F8 — Two-scale structure: economic_split separates globally, pool_committed_split separates locally**  
Over the full parameter range, economic_split determines whether scenarios enter the contested zone. Within the contested zone, pool_committed_split determines the winner. These are not contradictory — they describe the same boundary at different zoom levels. Feature importance at global scale: economic_split 60%, pool_committed_split 16.6%, pool_max_loss_pct 13.2%, pool_ideology_strength 10.3% (full 60-node network, n=692).

**F10 — Economic Self-Sustaining Point (ESP) at E ≈ 0.74**  
Below E ≈ 0.74, v27 cannot sustain economic majority even when it wins the hashrate war. At E ≈ 0.74, the price oracle feedback becomes self-reinforcing. Above E ≈ 0.82, pool ideology and loss tolerance delay but cannot prevent v27 victory.

**F12 — Price divergence cap affects cascade magnitude but not the cascade mechanism**  
The ±10% price cap binds in high-parameter scenarios (natural equilibrium would reach 13–16%), enabling some v26-dominant outcomes by suppressing cascade pressure. At ±30%, outcomes fully stall (12/12 contested). At ±40%, v27 wins. The economic node dead zone (ideology + inertia locks nodes in place) is confirmed at all cap levels — economic nodes never switch in response to price signals alone.

**F14 — Economic override is total at E ≥ 0.82**  
All 27 scenarios at E ∈ {0.82, 0.90, 0.95} produce v27-dominant outcomes regardless of pool ideology (0.40–0.80) or max_loss (0.25–0.45). Pool ideology creates resistance that delays cascade timing (700s to 10,920s) but cannot change the outcome.

### Mining Pool Dynamics

**F3 — The inversion zone: high pool commitment can hurt v27 at low economic support**  
Within E ∈ [0.60, 0.70], increasing pool_committed_split from 0.20 to 0.35 sometimes decreases v27 win rate. Mechanism: high pool commitment at low economic support creates a price signal that reflects pool ideology rather than genuine economic adoption, causing a reverse cascade. *Update (2026-06-28): This non-monotonicity is driven by Foundry's identity in deterministic pool ordering and vanishes under random pool composition (see F21).*

**F5 — Pool ideology parameters gate the cascade, not the outcome direction**  
The product `pool_ideology_strength × pool_max_loss_pct` determines whether committed pools hold through the retarget spike or capitulate early. Near the diagonal threshold (~0.16–0.18 at E=0.78), small changes flip whether committed v26 pools survive the cascade. The direction is context-dependent: the product measures defender resilience, not attacker strength.

**F7 — pool_committed_split threshold at C ≈ 0.25 (full network, within contested zone)**  
Within the PRIM transition zone on the full 60-node network, economic_split ≈ 0.563 is the dominant local predictor. On the lite network, pool_committed_split threshold ≈ 0.346. The difference reflects lite-network economic quantization (~25% resolution vs ~4%). *Contingent on which specific pools commit — see F21.*

**F15 — pool_committed_split dominance at 2016-block confirmed via unbiased LHS on full network**  
692-scenario LHS on full 60-node network confirms feature importance ranking and reveals the two-scale structure directly. PRIM v27 box: economic_split ≥ 0.665 wins 81% of scenarios regardless of pool parameters. The full-range global picture supersedes any lite-network finding. Finding 15 (n=64) is superseded by this result.

### Non-Causal Parameters (Confirmed)

**F2 — pool_neutral_pct controls cascade intensity, not outcome**  
Varying neutral pool fraction from 10% to 50% (sweeping AntPool and f2pool from committed to neutral) produces identical fork outcomes at all tested economic levels. The inversion zone persists even when the committed v26 block collapses to 8% of total hashrate. Neutral fraction affects cascade duration (reorg count and mass) but not who wins.

**F4 — hashrate_split is non-causal (sampling artifact in early sweeps)**  
The +0.83 correlation observed in early LHS sweeps was a confound: high hashrate_split scenarios also happened to have pool configurations that independently favored v27. When hashrate_split is varied in isolation with all other parameters fixed, its effect is zero across the full range.

**F6 — pool_profitability_threshold and solo_miner_hashrate are non-causal**  
Both confirmed non-causal at 2016-block retarget (lite network, sep ≤ 0.011) and confirmed on full 60-node network (near-zero RF importance). Excluded from all boundary fitting.

**F9 — User and economic node inertia parameters are non-causal**  
`econ_inertia`, `econ_switching_threshold`, `user_ideology_strength`, `user_switching_threshold` — all confirmed non-causal. Economic nodes are permanently locked by an ideology dead zone; inertia and switching threshold parameters do not shift this boundary. Confirmed on full network.

### Cascade Mechanism

**F11 — The 2016-block retarget is the cascade trigger, not the cause**  
When v27 accumulates 2016 blocks before v26, its difficulty drops to reflect the actual (slow) block rate — typically to 33–71% of original. This makes v27 blocks 1.4–3× more profitable per hashrate unit. Committed v26 pools facing losses exceeding max_loss_pct (26%) cascade to v27 simultaneously. Without a retarget, the profitability gap (3–10%) is insufficient to breach ideology thresholds, and the fork persists indefinitely.

**F13 — Full-network vs. lite-network finding reversal in the transition zone**  
Within the PRIM transition zone, lite network: pool_committed_split dominates (sep=0.188). Full network: economic_split dominates (sep=0.164). Both are correct in their respective contexts — the lite network's economic quantization artificially elevates pool parameters. Any finding from lite-network-only sweeps about pool parameter dominance should be treated as potentially inflated.

**F16 — Two-layer cascade structure: pool hashrate war decoupled from economic migration**  
Layer 1 (pool hashrate): controlled by pool_committed_split. Layer 2 (economic migration to winning chain): controlled by pool_max_loss_pct. The two layers are operationally decoupled — pool_committed_split means for full_switch vs. no_switch scenarios are statistically indistinguishable (0.386 vs 0.391). 81% of v27 pool hashrate war wins do NOT result in full economic adoption.

**F17 — Full economic migration requires low pool_max_loss_pct (< 0.217)**  
All full_switch outcomes in the Phase 3 transition zone have max_loss_pct ∈ [0.163, 0.217]. Mechanism: low loss tolerance forces committed pools to flip rapidly after the retarget spike, generating a 41–47% price gap that crosses economic node switching thresholds. High loss tolerance slows the cascade enough that the price signal never reaches that magnitude.

### Pool Composition (Arm A)

**F18 — pool_committed_split is a Foundry identity proxy at C = 0.214, not a true scalar**  
In deterministic pool ordering (all prior sweeps), Foundry USA (26.89% hashrate) is always the first pool assigned to v27. At C = 0.214, Foundry alone constitutes the entire committed block. Random composition (Arm A) confirms: when Foundry is absent from v27, the C = 0.214 point always loses (0% v27 win rate across 24 compositions). The prior "flip-point" finding is an identity effect, not a hashrate threshold.

**F19 — Pool composition identity matters within the transition zone**  
At fixed C = 0.30, AntPool alone (19.6% actual hashrate) committed to v27 produces v27 wins; binancepool+f2pool (21.6% actual hashrate) committed to v27 produces stalemates. The mechanism is retarget race speed: a single large pool's removal from v26 simultaneously depletes v26's block rate and accelerates neutral pool drift to v27. Multiple smaller pools at the same aggregate hashrate leave the largest pool (AntPool or Foundry) anchoring v26, preventing the retarget from firing.

**F20 — Realized committed hashrate is a sharper predictor than target C**  
Due to pool granularity, realized committed hashrate (committed_hashrate_actual) differs from target C. Actual hashrate bins predict win rate more cleanly than target C: actual < 0.20 → 3–13% v27 win; actual 0.20–0.25 → 55.6%; actual 0.30+ → 69–100%.

**F21 — Large pool identity effect is amplified on lite network (2026-06-28)**  
The Arm A pool identity effect was measured on the lite 25-node network, where economic quantization (~25% resolution) suppresses economic_split's explanatory power and inflates pool parameter effects. On the full 60-node network (Arm B, in progress), the identity effect is expected to partially dissolve as economic conditions regain their dominant role. The qualitative finding (retarget race mechanism is real) should be robust; the quantitative thresholds are network-size-dependent.

---

## Parameter Reference

| Parameter | Causal? | Direction | Key Finding |
|---|---|---|---|
| `economic_split` | Yes | v27 favored above 0.74 | F1, F8, F10 |
| `pool_committed_split` | Yes | v27 favored above ~0.25–0.30 | F1, F7, F8 |
| `pool_ideology_strength × pool_max_loss_pct` | Yes (interaction) | High values favor defender | F5, F17 |
| `pool_neutral_pct` | No (intensity only) | — | F2 |
| `hashrate_split` | No | — | F4 |
| `pool_profitability_threshold` | No | — | F6 |
| `solo_miner_hashrate` | No | — | F6 |
| `econ_inertia`, `econ_switching_threshold` | No | — | F9 |
| `user_ideology_strength`, `user_switching_threshold` | No | — | F9 |

For full parameter descriptions, ranges, and calibration rationale, see [docs/scenario_parameters.md](docs/scenario_parameters.md).
