# Sweep Specifications

Each YAML file in this directory defines a parameter sweep. The numbered scripts
in `tools/` consume these specs to generate, run, and analyze scenarios.

---

## Canonical Sweeps (Paper-Cited)

These specs produced the results reported in the paper. Outcomes are in
`sweep_results.db`.

| Spec | Network | Design | Description |
|---|---|---|---|
| `lhs_2016_full_6param.yaml` | full (60-node) | 6-param LHS | 692-scenario Latin Hypercube sample across the full parameter space at 2016-block retarget |
| `committed_sweep_2016_v3.yaml` | lite | 2D grid | Economic × committed-split grid at 2016-block retarget |
| `econ_sweep_2016.yaml` | full | 1D sweep | Economic adoption sweep across the full range |
| `neutral_pct_sweep.yaml` | full | 1D sweep | pool_neutral_pct varied 10–50% |
| `hashrate_split_isolated.yaml` | full | 1D sweep | hashrate_split varied with all other parameters fixed |
| `ideology_product_sweep.yaml` | full | 2D grid | pool_ideology_strength × pool_max_loss_pct grid |
| `retarget_regime_comparison.yaml` | full | comparison | 2016-block vs 144-block retarget, matched parameters |
| `price_cap_sweep.yaml` | lite | sensitivity | Price-divergence cap varied across multiple levels |
| `phase3_transition.yaml` | lite + full | LHS | Dense sampling within the transition zone |
| `pool_composition_arm_a.yaml` | lite | compositional grid | 168-scenario grid varying which pools commit at fixed aggregate hashrate |
| `pool_composition_arm_b.yaml` | full | compositional grid | Full-network replication of the arm-A compositional grid |

---

## Exploratory Sweeps (Development, Not Paper-Cited)

These specs were used during parameter-space exploration and hypothesis
generation. They are preserved for reproducibility and to document what was
tested.

| Spec | Purpose |
|---|---|
| `lhs_initial_*.yaml` | Early LHS attempts during initial parameter-space exploration |
| `committed_2016_v1.yaml`, `committed_2016_v2.yaml` | Earlier economic × committed grid iterations |
| `sigmoid_*.yaml` | Tests of an alternative ideology function |
| `solo_miner_*.yaml` | Solo-miner hashrate sensitivity |
| `profitability_threshold_*.yaml` | pool_profitability_threshold sensitivity |
| `econ_inertia_*.yaml` | Economic-node inertia sensitivity |
| `user_param_*.yaml` | User-node parameter sweeps |
| `targeted_*.yaml` | Follow-up targeted runs for specific hypotheses |
| `baseline_*.yaml` | Baseline parameter-validation runs |

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

The generator script `tools/1_generate_compositional.py` (or the appropriate
`1_generate_*.py` variant) reads the spec and produces `scenarios.json`. Then
`tools/2_build_configs.py` builds per-scenario Warnet configs.
