# Sweeps

This directory contains all parameter sweep infrastructure, specifications, results, and findings for the Bitcoin fork governance study.

---

## Quick Start

To explore results without running any sweeps:

```bash
# Distribution of outcomes across all 2,694 scenarios
sqlite3 sweep_results.db \
  "SELECT outcome, COUNT(*) FROM scenarios GROUP BY outcome"

# Full schema and query examples
cat DATABASE.md
```

---

## Running a Sweep

Sweeps require Warnet with a Kubernetes cluster. The pipeline has 5 stages:

```
1_generate_*.py  →  2_build_configs.py  →  3_run_sweep.py  →  4_analyze_results.py  →  5_build_database.py
```

### Step 1: Generate scenario parameters

Choose the generator appropriate to the sweep type:

```bash
# For a compositional grid sweep (e.g., pool_composition_arm_a or arm_b):
python tools/1_generate_compositional.py \
    --spec specs/pool_composition_arm_a.yaml \
    --output-dir pool_composition_arm_a/

# For an LHS sweep:
python tools/1_generate_lhs.py \
    --spec specs/lhs_2016_full_6param.yaml \
    --output-dir lhs_2016_full_6param/

# For a 2D grid sweep:
python tools/1_generate_scenarios.py \
    --spec specs/committed_sweep_2016_v3.yaml \
    --output-dir committed_sweep_2016_v3/
```

This produces `<sweep_dir>/scenarios.json`.

### Step 2: Build Warnet network configs

```bash
python tools/2_build_configs.py \
    --input pool_composition_arm_a/scenarios.json \
    --output-dir pool_composition_arm_a/ \
    --base-network lite   # or: full
```

This produces per-scenario subdirectories under `<sweep_dir>/configs/` and `<sweep_dir>/networks/`, plus a `build_manifest.json`.

### Step 3: Run on Kubernetes

For large sweeps, split the manifest across namespaces. See `tools/split_manifest.py`:

```bash
python tools/split_manifest.py \
    --input pool_composition_arm_a/build_manifest.json \
    --n-splits 6 \
    --output-prefix pool_composition_arm_a/build_manifest_ns
```

Then run each namespace (adapt namespace name and scenario range):

```bash
python tools/3_run_sweep.py \
    --input pool_composition_arm_a/build_manifest_ns_0.json \
    --results-dir pool_composition_arm_a/results/ns-0 \
    --namespace arma-0 \
    --duration 13000 --retarget-interval 2016 --interval 2 \
    --startup-wait 30 --cooldown 45 \
    --no-auto-restart
```

Timing: each scenario takes ~217 minutes (3.6 hours) wall-clock on both lite and full networks.

### Step 4: Analyze results

```bash
python tools/4_analyze_results.py \
    --results-dir pool_composition_arm_a/results/ \
    --output-dir pool_composition_arm_a/analysis/
```

Produces per-scenario outcome files, summary statistics, and a combined CSV.

### Step 5: Add to database

```bash
python tools/5_build_database.py
```

Reads all `analysis/` directories (auto-discovered) and appends to `sweep_results.db`. Idempotent — re-running does not create duplicate rows.

---

## Sweep Specifications

All sweep configurations are in `specs/`. See [specs/README.md](specs/README.md) for which specs are canonical (paper-cited) vs. exploratory (developmental).

---

## Multi-Server Deployments

For full 168-scenario sweeps like arm_a and arm_b, use 2 servers × 6 namespaces each (14 scenarios/namespace):

```
Server 1: namespaces arma-0 to arma-5  → scenarios 0–83
Server 2: namespaces arma-6 to arma-11 → scenarios 84–167
```

See the `RUN_INSTRUCTIONS.md` in each sweep directory for copy-paste commands.

After completion, collect results:

```bash
rsync -av server1:~/warnetScenarioDiscovery/tools/sweep/<sweep_name>/results_server1/ \
    <sweep_name>/results_server1/
rsync -av server2:~/warnetScenarioDiscovery/tools/sweep/<sweep_name>/results_server2/ \
    <sweep_name>/results_server2/
```

---

## Results Database

`sweep_results.db` contains all 2,694 scenarios from the canonical sweeps. See [DATABASE.md](DATABASE.md) for the full schema and query guide.

Key table: `scenarios`

| Column | Description |
|---|---|
| `sweep_name` | Which spec produced this scenario |
| `scenario_id` | e.g., `sweep_0042` |
| `outcome` | `v27_dominant`, `v26_dominant`, `contested`, `stalemate` |
| `economic_split` | E parameter (0.0–1.0) |
| `pool_committed_split` | C parameter (0.0–1.0) |
| `network` | `lite` or `full` |
| `retarget_interval` | 2016 or 144 |
| `retarget_fired_first` | Which chain fired the first retarget |
| `v27_win_time_s` | Seconds to v27 dominance (null if not achieved) |

---

## Findings

See [SWEEP_FINDINGS.md](SWEEP_FINDINGS.md) for the complete technical record (~4,200 lines). See [../FINDINGS.md](../FINDINGS.md) for the curated summary of all 21 numbered findings.
