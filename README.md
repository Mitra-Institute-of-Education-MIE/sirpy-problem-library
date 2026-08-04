#  Luminary Quantum Institute (LQI)

## SIRPY Mathematical Problem Library

**SIRPY** is a differential-equation solver that returns solutions as functions — power series you can read, differentiate, and evaluate anywhere — each with an honest, self-measured accuracy certificate. It solves initial value, boundary value, and integral equations, and reports where it stands.

This repository is a professional, textbook-grade catalog of mathematical systems solved and verified by SIRPY. It contains the problems, worked write-ups, usage instructions, and the solver's own reported output for each result. Code appears only as short illustrative snippets showing how a problem is posed to SIRPY. **The solver engine itself is proprietary and is not distributed here.**

---

## What makes SIRPY different

Most solvers return the solution as a table of numbers sampled on a grid, and leave you to trust what happens between the points. SIRPY returns the solution as an explicit analytic object — a power series (or piecewise series, Frobenius series, or Padé rational) — that you can read, differentiate, and evaluate at any point.

Two commitments sit under every result:

- **Verification by substitution.** Because the answer is a function, SIRPY substitutes it back into the original equation and measures the defect. You are not asked to trust the result; you are shown the residual.
- **Honest reliability.** SIRPY reports an estimated reliable range for every solution and never claims an accuracy it has not measured. When a problem reaches the limits of the implemented theory, it says so explicitly rather than returning a silently degraded answer.

---

## What SIRPY solves

- **Initial value problems** — ODEs of any order, including nonlinear and transcendental right-hand sides, systems, and moderately stiff problems. Detects and localizes finite-time blow-up (movable singularities).
- **Two-point boundary value problems** — no shooting, no root-finding, no user-supplied initial guess. Missing initial data is named and recovered by the solver. Includes problems posed at a singular endpoint.
- **Nonlocal / integro-differential problems** — equations whose coefficients depend on an integral of the unknown solution (e.g. Hartree-type). The integral is handled as exact coefficient arithmetic — no quadrature.
- **Volterra integral equations of the second kind** — including weakly singular (Abel-type) kernels, and formulations carrying boundary conditions.
- **Regular singular points** — Frobenius series constructed from the indicial equation. Genuine logarithmic cases are reported honestly, not fabricated.
- **Padé post-processing** — rational approximation to extend a series beyond its radius of convergence.

Every result carries quantitative reliability diagnostics, and configurations outside the implemented theory terminate with a precise statement of what failed.

---

## How to use it

The public interface is a small set of functions:

```python
from sirpy import solve_ivp, solve_bvp, solve_volterra, solve_frobenius, pade
```

**Boundary value problem** (an unknown initial slope is named `"gamma"` and recovered by the solver):

```python
from sirpy import solve_bvp

r = solve_bvp(order=2, f_str="2*y**3", x0=0, x1=9,
              left_bc={0: -0.1, 1: "gamma"}, right_bc={0: -1},
              iterations=40, partition=16, verbose=True)

r.evaluate("y", 4.5)     # evaluate the solution anywhere
```

**Nonlocal problem** (a coefficient `M` is defined as an integral of the solution):

```python
r = solve_bvp(order=2, f_str="-M*y", x0=0, x1=1,
              left_bc={0: 1, 1: "gamma"}, right_bc={0: 0},
              nonlocal_terms={"M": {"integrand": "y**2", "from": 0, "to": 1}},
              iterations=20, verbose=True)
```

**Verify any result** by substituting the solution back into the equation and plotting both sides:

```python
from sirpy import plot_verification

plot_verification(r, save="verify.png", show=True)
```

Each problem folder contains a walk-through `README.md` showing the problem, the exact call used, the solver's own reported output, and the verification figures.

---

## 📚 Problem Catalog

| # | Domain | Equation Type | Problem | Status ||
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Nonlinear ODE | Boundary value, movable pole | y′′ = 2y³ on [0, 9] — solved to machine precision; pole at x = 10 located unprompted | ✅ Verified ||
| 2 | Nonlinear ODE | Boundary value, shifted singularity | y′′ = 2y³ on [0, 11] — the singularity moves with the boundary data | ✅ Verified ||
| 3 | Quantum Mechanics | Nonlocal (integro-differential) | 1D Steady-State Hartree Equation | ✅ Verified ||
| 4 | Quantum Mechanics | Local nonlinear cubic | Gross–Pitaevskii Equation | ✅ Verified ||
| 5 | Integral Equations | Nonlinear Volterra, second kind | y(t) = t(1−t) + ∫₀ᵗ (t−s) y(s)² ds on [0, 1] — no ICs given; the equation supplies its own initial data | ✅ Verified ||
| 6 | Integral Equations | Nonlinear Volterra, second kind, embedded free parameter | y(t) = g·t − t² + ∫₀ᵗ (t−s) y(s)² ds, y(1) = 0 — g recovered via terminal condition, cross-checked against y′(0) | ✅ Verified ||
*(Numbering matches the SIRPY problem series posted publicly.)*

---



## Submitting a problem

We invite the mathematical, scientific, and engineering communities to send us problems. If you have an ordinary differential equation, initial value problem, boundary value problem, or other ODE-related problem you believe is challenging, unusual, or simply beautiful, we would like to see it.

We will run it through SIRPY and report honestly what happened — if it solves it, how; if it struggles, why; if it reaches a current limit, we will say so openly.

With your permission, selected submissions may become entries in this library, with acknowledgment of your name, affiliation, and position (all optional). **Please only send problems you are free to share publicly** — not work under NDA or otherwise confidential.

Contact: **info@mitrainstituteofeducation.org** or the form at **https://mitrainstituteofeducation.org/contact/**

We cannot promise every problem will be solved. We can promise every submission will receive an honest evaluation.

---

## Release Status

SIRPY is in final validation prior to its first public release. This library documents verified results from that validation program—each entry is a solved problem, with the solver's reported output and an independent verification residual.

Our current focus is the first public release of SIRPY as an ordinary-differential-equation solver. The next major development phase extends the same philosophy of transparency, verification, and honest reporting to partial differential equations.

For release timing, licensing, or evaluation access, contact **info@mitrainstituteofeducation.org**.

---

## Legal

© 2026 Luminary Quantum Institute, LLC (LQI). All rights reserved.

SIRPY is a trademark of Luminary Quantum Institute, LLC.

The contents of this repository — problem write-ups, documentation, figures, and illustrative snippets — are provided for reference and evaluation. No license to the SIRPY solver engine, which is proprietary and not distributed here, is granted. See `LICENSE` for terms governing the repository contents.