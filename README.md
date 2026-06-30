# Bitcoin Soft Fork Governance — Simulation Study

**Paper:** Quantifying Bitcoin Network Resilience Through Critical Scenario Discovery  
**Workshop:** University of Wyoming Bitcoin Research Initiative, July 2026  
**Author:** Peter Foytik, Old Dominion University

> This repository contains the simulation scenario, network configurations, parameter sweep tooling, and full results database accompanying the paper. All 21 quantitative findings in the paper are reproducible from the data and tools here.

---

## What This Is

This study models a contested Bitcoin soft fork — a new consensus rule set (v27) competing against the existing rule set (v26) — and maps the conditions under which each side wins, loses, or produces a sustained chain split. Simulations run real `bitcoind` nodes on Kubernetes using [Warnet](https://github.com/bitcoin-dev-tools/warnet), with agents representing mining pools, exchanges, institutions, and users making independent, economically-motivated decisions.

The central question: **under what combinations of miner commitment, economic adoption, and pool ideology does v27 succeed or fail?**

The answer is a two-scale decision boundary:
- At the global level, `economic_split` (fraction of BTC custody supporting v27) determines whether the scenario falls into the contested zone at all
- Within the contested zone, `pool_committed_split` (committed mining hashrate fraction) and the 2016-block difficulty retarget mechanism determine the winner

---

## Key Findings at a Glance

- **Economic Self-Sustaining Point (ESP) at E ≈ 0.74** — below this, v27 cannot win regardless of mining support; above E ≈ 0.82, v27 wins regardless of mining
- **Pool committed hashrate threshold at C ≈ 0.25–0.30** — within the contested zone, v27 wins when more than ~25–30% of hashrate is ideologically committed to v27
- **2016-block retarget cascade** — the decisive mechanism: the first chain to accumulate 2016 blocks fires a difficulty adjustment that makes its blocks 2–3× more profitable, cascading all remaining pools
- **Pool identity matters, not just aggregate hashrate** — a single large pool (Foundry, 27%) commits provides stronger cascade signal than equivalent smaller pools; this effect is magnified on lite network simulations
- **Full-network finding**: on the 60-node network with proper economic resolution, `economic_split` dominates at 60% feature importance; pool composition is secondary

---

## Repository Structure

```
bitcoin-fork-governance-study/
│
├── README.md                   ← You are here
├── METHODOLOGY.md              ← How the simulation works
│
├── scenarios/                  ← Warnet scenario script
│   ├── partition_miner_with_pools.py   ← Main simulation scenario
│   ├── lib/                    ← Price, fee, difficulty, and strategy oracles
│   └── config/                 ← Pool and economic node configuration
│
├── networks/                   ← Bitcoin network topologies
│   ├── realistic-economy-v2/   ← Full 60-node network (primary results)
│   └── realistic-economy-lite/ ← Lite 25-node network (fast reproduction)
│
├── sweeps/                     ← Parameter sweep infrastructure and results
│   ├── sweep_results.db        ← SQLite database: all 2,694 scenarios
│   ├── specs/                  ← YAML specs for all sweep configurations
│   ├── tools/                  ← Sweep pipeline scripts (1–5)
│   └── DATABASE.md             ← Database schema and query guide
│
├── analysis/                   ← Boundary fitting and visualization
│   ├── fit_boundary.py         ← Random Forest + PRIM boundary fitting
│   ├── plot_decision_boundary.py
│   └── scenario_potential.py
│
└── docs/
    ├── assumptions.md          ← Model assumptions and calibration
    ├── BCAP_Alignment.md       ← Mapping to BCAP governance framework
    ├── realistic_economy_model.md  ← Network design rationale
    ├── Boundary_Fitting.md     ← Phase 2 boundary fitting details
    └── figures/                ← All paper figures (PNG)
```

---

## Prerequisites

- Python 3.10+
- [Warnet](https://github.com/bitcoin-dev-tools/warnet) with a Kubernetes cluster (only needed to run new sweeps)
- Python packages: `numpy`, `scipy`, `scikit-learn`, `pandas`, `pyyaml`, `matplotlib`

```bash
pip install numpy scipy scikit-learn pandas pyyaml matplotlib
```

---

## Exploring the Results

The fastest way to engage with the findings is through the SQLite results database. No Kubernetes or warnet installation required.

```bash
cd sweeps

# Outcome distribution across all 2,694 scenarios
sqlite3 sweep_results.db \
  "SELECT outcome, COUNT(*) as n, ROUND(AVG(economic_split),3) as mean_econ
   FROM scenarios GROUP BY outcome ORDER BY n DESC"

# The two-scale structure: economic_split dominates globally
sqlite3 sweep_results.db \
  "SELECT CASE WHEN economic_split < 0.50 THEN 'E < 0.50'
               WHEN economic_split < 0.74 THEN 'E 0.50-0.74'
               WHEN economic_split < 0.82 THEN 'E 0.74-0.82'
               ELSE 'E > 0.82' END as zone,
          outcome, COUNT(*) as n
   FROM scenarios GROUP BY zone, outcome ORDER BY zone, n DESC"

# Pool committed split threshold in the contested zone
sqlite3 sweep_results.db \
  "SELECT outcome,
          ROUND(AVG(pool_committed_split),3) as mean_C,
          ROUND(MIN(pool_committed_split),3) as min_C,
          ROUND(MAX(pool_committed_split),3) as max_C
   FROM scenarios
   WHERE economic_split BETWEEN 0.55 AND 0.78
   GROUP BY outcome"
```

For a full schema and query guide, see [sweeps/DATABASE.md](sweeps/DATABASE.md).

---

## Reproducing a Specific Sweep

To re-run one of the canonical sweeps from scratch (requires Warnet + Kubernetes):

```bash
cd sweeps

# 1. Build network configs from spec
python tools/2_build_configs.py \
    --input specs/lhs_2016_full_6param.yaml \
    --output-dir /tmp/lhs_full \
    --base-network full

# 2. Run on a Kubernetes cluster (see sweep spec for namespace/parallelism settings)
python tools/3_run_sweep.py \
    --input /tmp/lhs_full/build_manifest.json \
    --results-dir /tmp/lhs_full/results \
    --duration 13000 --retarget-interval 2016 --interval 2 \
    --namespace my-sweep --startup-wait 60

# 3. Analyze results
python tools/4_analyze_results.py \
    --results-dir /tmp/lhs_full/results \
    --output-dir /tmp/lhs_full/analysis

# 4. Add to database
python tools/5_build_database.py
```

Each canonical sweep has a YAML spec in `sweeps/specs/`. See [sweeps/specs/README.md](sweeps/specs/README.md) for which specs correspond to which paper findings.

---

## Reproducing the Decision Boundary Figures

```bash
cd analysis

# Random Forest + PRIM boundary fit (requires sweep_results.db in sweeps/)
python fit_boundary.py \
    --db ../sweeps/sweep_results.db \
    --regime 2016 \
    --network full \
    --output-dir output/

# Plot decision boundary (E vs C plane)
python plot_decision_boundary.py --input output/
```

---

## Citation

```bibtex
@inproceedings{foytik2026bitcoin,
  title     = {Quantifying Bitcoin Network Resilience Through Critical Scenario Discovery},
  author    = {Foytik, Peter},
  booktitle = {Proceedings of the University of Wyoming Bitcoin Research Initiative Workshop},
  year      = {2026},
  note      = {Data and code: https://github.com/pfoytik/bitcoin-fork-governance-study}
}
```

---

## License

MIT — see [LICENSE](LICENSE)

## Acknowledgments

Simulations built on [Warnet](https://github.com/bitcoin-dev-tools/warnet) by the Bitcoin Dev Tools team.
