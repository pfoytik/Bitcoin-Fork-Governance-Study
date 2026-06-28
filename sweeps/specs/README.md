# Sweep Specifications

Each YAML file in this directory defines a parameter sweep. The numbered scripts in `tools/` consume these specs to generate, run, and analyze scenarios.

---

## Canonical Sweeps (Paper-Cited)

These specs produced the findings reported in the paper. Results are in `sweep_results.db`.

| Spec | Findings | Description |
|---|---|---|
| `lhs_2016_full_6param.yaml` | F8, F15 | 692-scenario LHS on full 60-node network; establishes two-scale structure and global feature importance |
| `committed_sweep_2016_v3.yaml` | F3, F5, F7 | 2D E×C grid on lite network; discovery of the inversion zone and transition zone |
| `econ_sweep_2016.yaml` | F10, F14 | ESP discovery and economic override above E=0.82 |
| `neutral_pct_sweep.yaml` | F2 | pool_neutral_pct varied 10–50%; confirms non-causal |
| `hashrate_split_isolated.yaml` | F4 | hashrate_split varied in isolation; confirms non-causal |
| `ideology_product_sweep.yaml` | F5, F17 | Pool ideology × max_loss sweep; cascade gating mechanism |
| `retarget_regime_comparison.yaml` | F1 | 2016-block vs 144-block retarget; parameter rank inversion |
| `price_cap_sensitivity.yaml` | F12 | ±10%, ±30%, ±40% price divergence cap |
| `phase3_transition.yaml` | F16, F17 | Two-layer cascade structure; full_switch vs no_switch in transition zone |
| `pool_composition_arm_a.yaml` | F18, F19, F20, F21 | 168-scenario compositional grid on lite network; pool identity effects |
| `pool_composition_arm_b.yaml` | F21 | Same 168-scenario grid on full 60-node network; arm A replication |

---

## Exploratory Sweeps (Development, Not Paper-Cited)

These specs were used during parameter space exploration and hypothesis generation. They are preserved for reproducibility and to document what was tested and ruled out.

| Spec | Purpose |
|---|---|
| `lhs_initial_*.yaml` | Early LHS attempts before parameter space was understood |
| `committed_2016_v1.yaml`, `committed_2016_v2.yaml` | Earlier E×C grid iterations |
| `sigmoid_*.yaml` | Tests of sigmoid ideology function (replaced by linear threshold) |
| `solo_miner_*.yaml` | Solo miner hashrate sensitivity (confirmed non-causal, see F6) |
| `profitability_threshold_*.yaml` | pool_profitability_threshold sensitivity (confirmed non-causal, see F6) |
| `econ_inertia_*.yaml` | Economic node inertia sensitivity (confirmed non-causal, see F9) |
| `user_param_*.yaml` | User node parameter sweeps (confirmed non-causal, see F9) |
| `targeted_*.yaml` | Follow-up targeted runs for specific hypotheses |
| `baseline_*.yaml` | Baseline parameter validation runs |

---

## Spec Format

Each spec is a YAML file with the following top-level keys:

```yaml
name: my_sweep
type: lhs | grid_2d | compositional_grid | targeted | baseline
network: lite | full
n_scenarios: 168
description: |
  Human-readable description of purpose, design, and expected results.

# Type-specific fields (examples):
economic_split_values: [0.55, 0.65, 0.74, 0.78]   # for grid/compositional types
pool_committed_split_values: [0.10, 0.15, ...]
compositions_per_point: 6                            # for compositional_grid type
base_composition_seed: 1000

# Fixed parameters held constant across all scenarios in this sweep
fixed:
  hashrate_split: 0.25
  pool_neutral_pct: 30.0
  ...
```

The generator script `tools/1_generate_compositional.py` (or the appropriate `1_generate_*.py` variant) reads the spec and produces `scenarios.json`. Then `tools/2_build_configs.py` builds per-scenario Warnet configs.
