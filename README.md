# Quantum and Classical Cubic-Order Binary Optimization Benchmarking for Supply-Chain Problems

# Project Overview

This repo benchmarks a simple 3rd-order (HUBO) supply-chain optimization problem for selecting **one supplier, one product, and one carrier** to minimize a cost score on a relatively modest search space (2^20 terms) of binary decisions.

Solvers used for benchmarking involve a **photonic quantum computer** (DIRAC-3) and a state-of-the-art **classical solvers** (Gurobi)

Given that Gurobi does not natively solve HUBO problems, we applied **AND-Gadget penalty** to reduce the HUBO (3rd or higher order) formulation to QUBO (2nd-order), enabling apples-to-apples runs on the same objective function.

---

## Key Features

* **Higher-Order Modeling (HUBO):** Objective includes linear, quadratic, and cubic terms derived from supply-chain features (manufacturing cost, lead time, defect rate).
* **HUBO → QUBO Reduction:** AND-gadget compresses 3-way interactions to pairs so classical solvers can run the problem.
* **Dual Solver Benchmark:** **DIRAC-3** (photonic quantum computer) vs **Gurobi** (classical solver) on the same data and weights.
* **One-Hot Feasibility:** Optional “choose exactly one of each” penalties enforce a single selection per category.
* **Reproducible Workflow:** Clear build → reduce → solve pipeline with configurable weights and solver settings.

---

## Technical Implementation

### Part 1: Problem & Data

* **Decision Task:** Pick exactly one {supplier, product, carrier} to minimize the objective.
* **Feature Weighting:** Coefficients set via configurable weights to form the higher-order objective.
* **Data Source:** Kaggle dataset (supplier defects, lead time, manufacturing cost), expanded to **100k** rows with synthetic samples within **±3σ** of feature means.

Link: https://www.kaggle.com/datasets/harshsingh2209/supply-chain-analysis/discussion?sort=hotness

### Part 2: HUBO Construction

* **Objective Assembly:** Linear, pairwise, and cubic interactions aggregated from data features.
* **Feasibility Encoding:** Optional one-hot penalties (per category) to enforce single selection.

### Part 3: AND-Gadget (HUBO → QUBO)

* **Reduction:** Introduces auxiliary variables and consistency penalties to replace cubic terms with quadratic equivalents.
* **Outcome:** A single symmetric **Q** matrix suitable for classical QUBO solvers while preserving original intent.

### Part 4: Solvers & Runs

* **Gurobi (Classical):** Relax-and-polish approach on the QUBO; solves KKT-type systems via matrix factorizations and rounds to 0/1.
* **DIRAC-3 (Quantum/Quantum-Inspired):** Iterative bit-flip search with cached (y=Qw) updates; configurable sampling and relaxation schedules.
* **Metrics:** Objective value (lower is better), wall-clock time, and the chosen {supplier, product, carrier}.

---

## Technologies Used

* **Language:** Python 3.x
* **Libraries:** `numpy`, `pandas`
* **Solvers:** **Gurobi** (`gurobipy`; license required), **DIRAC-3** SDK (with credentials)
* **Environment:** Jupyter Notebooks or CLI scripts

---

## Key Results

* **Comparative Performance:** DIRAC-3 achieved a **lower objective** (-12759.5415 vs -12529.6998) on our test instance; Gurobi solved **faster** on wall-clock (0.637s vs 37.145s).
* **Feasibility:** One-hot constraints satisfied; penalties near zero at the solution.
* **Solver Disagreement:** Different selected combos and objective values highlight method sensitivity to schedules, relaxations, and reductions.

| Solver   | Objective (↓ better) | Time (s) |
|----------|----------------------:|---------:|
| Gurobi   |          -12529.6998 |    0.637 |
| DIRAC-3  |          -12759.5415 |   37.145 |
| **Delta (DIRAC-3 − Gurobi)** | **-229.8417** | **+36.508** |


---

## Skills Demonstrated

* Optimization Modeling (HUBO/QUBO)
* Linear Algebra for Formulation & Solving
* Algorithmic Benchmarking
* Data Engineering & Feature Weighting
* Python Packaging & Reproducibility

---

## Future Improvements

* **Scale & Realism:** Larger (n), more categories, and real-world datasets beyond synthetic augmentation.
* **Tuning:** Systematic sweeps over DIRAC-3 sampling/schedule and Gurobi polishing/relaxation settings.
* **Reporting:** Richer diagnostics (penalty breakdowns, flip traces, factorization stats).
* **Extensions:** Alternative reduction gadgets; constraint-preserving rounding/refinement.

---

## Installation

### Prerequisites

* **Python:** 3.x
* **Package Manager:** `pip`
* **Licenses/Tokens:** Gurobi license; DIRAC-3 credentials

---

## License

This project is released under the **MIT License** (or your preferred license). Please cite appropriately if you use any part of this work.
