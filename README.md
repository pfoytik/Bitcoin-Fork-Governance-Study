# Bitcoin Soft Fork Governance — Simulation Study

**Paper:** Quantifying Bitcoin Network Resilience Through Critical Scenario Discovery  
**Workshop:** University of Wyoming Bitcoin Research Initiative, July 2026  
**Author:** Peter Foytik, Old Dominion University

> This repository contains the simulation scenario, network configurations, parameter sweep tooling, and full results database accompanying the paper. All 21 quantitative findings in the paper are reproducible from the data and tools here.

---

## What This Is

This study models a contested Bitcoin soft fork — a ficticious new consensus rule set that is limiting the previous rules (labeled v27) competing against the existing rule set (labeled v26) — and maps the conditions under which each side wins, loses, or produces a sustained chain split. Simulations run real `bitcoind` nodes on Kubernetes using [Warnet](https://github.com/bitcoin-dev-tools/warnet), with agents representing mining pools, exchanges, institutions, and users making independent, economically-motivated decisions.

The central question: **under what combinations of miner commitment, economic adoption, and pool ideology does v27 succeed or fail?**

The answer is a two-scale decision boundary:
- At the global level, `economic_split` (fraction of BTC custody supporting v27) determines whether the scenario falls into the contested zone at all
- Within the contested zone, `pool_committed_split` (committed mining hashrate fraction) and the 2016-block difficulty retarget mechanism determine the winner

---

## What the Study Examines

[#what-the-study-examines](#what-the-study-examines)

The simulation maps fork outcomes across a 6-parameter space. The analysis
addresses four questions, each answered quantitatively in the paper:

- **Where is the economic threshold?** At what level of economic adoption does
  the outcome stop depending on mining behavior — in either direction?
- **What role does committed hashrate play?** Within the contested region, how
  much ideologically-committed mining hashrate is needed to shift the outcome?
- **What is the cascade mechanism?** How does the 2016-block difficulty retarget
  function in resolving (or failing to resolve) a contested fork?
- **Does pool identity matter, or only aggregate hashrate?** Is committed
  hashrate a simple scalar, or does *which* pools commit change the outcome?

The headline result is a two-scale decision boundary: a global scale governed by
economic adoption, and a local scale within the contested zone governed by mining
dynamics. Full thresholds, mechanisms, and feature-importance results are
presented in the paper and at the UW BRI workshop (July 2026).
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

[#exploring-the-results](#exploring-the-results)

All 2,694 scenarios are in the SQLite results database. No Kubernetes or warnet
installation required. The schema and full column list are in
[sweeps/DATABASE.md](sweeps/DATABASE.md).

```bash
cd sweeps

# What's in the database — table and column overview
sqlite3 sweep_results.db ".schema scenarios"

# Total scenario count and the outcome categories used
sqlite3 sweep_results.db \
  "SELECT outcome, COUNT(*) AS n FROM scenarios GROUP BY outcome"

# Range of each input parameter sampled across the sweep program
sqlite3 sweep_results.db \
  "SELECT ROUND(MIN(economic_split),3) AS min_E,
          ROUND(MAX(economic_split),3) AS max_E,
          ROUND(MIN(pool_committed_split),3) AS min_C,
          ROUND(MAX(pool_committed_split),3) AS max_C
   FROM scenarios"
```

The database supports the full analysis pipeline. See
[sweeps/DATABASE.md](sweeps/DATABASE.md) for the schema and
[analysis/](analysis/) for the boundary-fitting and figure-generation scripts.

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
  booktitle = {Proceedings of the University of Wyoming Bitcoin Research Institute Workshop},
  year      = {2026},
  note      = {Data and code: https://github.com/pfoytik/bitcoin-fork-governance-study}
}
```

---

## License

MIT — see [LICENSE](LICENSE)

## Acknowledgments

Simulations built on [Warnet](https://github.com/bitcoin-dev-tools/warnet) by the Bitcoin Dev Tools team.

Bitcoin Consensus Analysis (BCAP) [BCAP](https://github.com/bitcoin-cap/bcap)
