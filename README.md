# 3B-ADMM-UC-DQAOA

Official research code for the three-block ADMM unit commitment study using
brute-force enumeration, monolithic QAOA, and distributed QAOA (DQAOA) as
alternative solvers for the binary QUBO block.

## Paper

**A Distributed Quantum Approximate Optimization Algorithm For Unit Commitment**

Ali Rajabi, Milad Hasanzadeh, and Amin Kargarian  
Department of Electrical and Computer Engineering  
Louisiana State University

## Overview

The notebook implements a simplified unit commitment (UC) model and a
three-block ADMM solution framework:

1. **Block 1:** relaxed commitment and economic dispatch.
2. **Block 2:** binary commitment QUBO solved using one of:
   - brute-force enumeration,
   - monolithic QAOA,
   - explicit TeleGate-based distributed QAOA.
3. **Block 3:** closed-form slack-variable update.
4. **Dual update:** consensus multiplier update.

The notebook also includes capacity-feasibility safeguards, economic-dispatch
re-evaluation, comparison against an exact small-instance UC reference, and
plots/tables used to validate the three solver modes.

## Main Notebook

`UC_3Block_ADMM_DQAOA_Safeguarded_FULL.ipynb`

The notebook contains the DQAOA-on-QUBO implementation used by the UC driver,
so the study can be run directly from the notebook after installing the Python
dependencies.

## Case Study

The included numerical case has:

- 5 generating units
- 3 scheduling periods
- 15 binary commitment variables
- demand profile: `[60, 130, 280]` MW

The exact commitment recovered in the reported run is:

```text
[[0, 0, 1, 0, 0],
 [0, 1, 0, 0, 1],
 [0, 1, 1, 1, 1]]
```

Bitstring:

```text
001000100101111
```

Associated dispatch:

```text
[[ 0.00000000,  0.00000000, 60.00000000,  0.00000000,  0.00000000],
 [ 0.00000000, 50.00000000,  0.00000000,  0.00000000, 80.00000000],
 [ 0.00000000, 50.00000000, 60.00000000, 71.66666667, 98.33333333]]
```

Operating cost:

```text
12678.45
```

Brute force, monolithic QAOA, and distributed QAOA recover the same reported
UC solution for the stored experiment.

## ADMM Settings Used in the Reported Run

```python
rho = 200.0
Beta = 50.0
max_iter = 80
primal_tol = 1e-3
accept_if_better = True
accept_tol = 1e-8
```

## QAOA / DQAOA Settings

```python
p_max = 2
shots_train = 512
final_shots = 256

adam_iters = 80
learning_rate = 0.08
fd_eps0 = 0.12

num_random_starts = 8
warm_start_perturbations = 1
warm_start_sigma = 0.10

num_qpus = 3
capacities = [5, 5, 5]
```

The QAOA modes use the same UC data, ADMM settings, initialization, stopping
criterion, and iteration-dependent QUBO construction.

## Installation

Python 3.10 or newer is recommended.

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

macOS/Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

Then open the notebook in Jupyter:

```bash
jupyter notebook UC_3Block_ADMM_DQAOA_Safeguarded_FULL.ipynb
```

## Dependencies

The main numerical dependencies are:

- NumPy
- pandas
- Matplotlib
- Qiskit
- Qiskit Aer

`docplex` is optional and is only needed for optional CPLEX-based functionality.

## Solver Modes

Set `USER_SELECTED_MODE` in the notebook to one of:

```python
"bruteforce"
"monolithic_qaoa"
"telegate_explicit_qaoa"
```

The notebook also contains an optional final cell that runs all three modes and
compares their reported solutions.

## Notes

- The UC model used here is intentionally simplified: no ramping, reserve,
  startup/shutdown, or network-flow constraints are included.
- The distributed implementation assigns each logical commitment variable to
  one logical data qubit and distributes those qubits across QPUs according to
  the specified capacities.
- TeleGate operations are used when a nonzero QUBO interaction crosses a QPU
  boundary.
- The purpose of the distributed mode is multi-QPU capacity accommodation; the
  reported study does not claim quantum runtime acceleration.

## Related DQAOA Repository

The general DQAOA QUBO simulator is maintained separately by the LSU RAISE Lab
in the `DQAOA` repository.

## Authors

- Ali Rajabi
- Milad Hasanzadeh
- Amin Kargarian

Louisiana State University, RAISE Lab
