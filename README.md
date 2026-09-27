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

- **Certified parameter estimation.** For models fitted to noisy data, SIRPY reports the estimate, its confidence interval, and the numerical residual — so you can see whether solver error is small enough to change the answer. See the Torricelli study.

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
| 1 | [Nonlinear ODE I](https://github.com/sirpy26/SIRPY/blob/main/Problems/nonlinear_ode_1.pdf) | Boundary value, movable pole | y′′ = 2y³ on [0, 9] — solved to machine precision; pole at x = 10 located unprompted | ✅ Verified ||
| 2 | [Nonlinear ODE II](https://github.com/sirpy26/SIRPY/blob/main/Problems/nonlinear_ode_2.pdf)  | Boundary value, shifted singularity | y′′ = 2y³ on [0, 11] — the singularity moves with the boundary data | ✅ Verified ||
| 3 | [Quantum Mechanics I](https://github.com/sirpy26/SIRPY/blob/main/Problems/quantun_mechanics_1.pdf) | Nonlocal (integro-differential) | 1D Steady-State Hartree Equation | ✅ Verified ||
| 4 | [Quantum Mechanics II](https://github.com/sirpy26/SIRPY/blob/main/Problems/quantun_mechanics_2.pdf) | Local nonlinear cubic | Gross–Pitaevskii Equation | ✅ Verified ||
| 5a | [Integral Equations](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_5a.pdf)  | Nonlinear Volterra, second kind | y(t) = t(1−t) + ∫₀ᵗ (t−s) y(s)² ds on [0, 1] — no ICs given; the equation supplies its own initial data | ✅ Verified ||
| 5b | [Integral Equations](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_5b.pdf)| Nonlinear Volterra, second kind, embedded free parameter | y(t) = g·t − t² + ∫₀ᵗ (t−s) y(s)² ds, y(1) = 0 — g recovered via terminal condition, cross-checked against y′(0) | ✅ Verified ||
| 6 | [Troesch's Problem](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_6.pdf) | A classical test case originating in plasma physics |y'' = mu* sinh(mu*y), y(0) = 0, y(1) =1 | ✅ Verified ||
| 7 | [Stiff Problem I](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_7.pdf) |  A classic stiff linear initial-value problem |y'' + (101)*y' + 100*y = 0, y(0) = 1, y'(0) =-1 | ✅ Verified ||
| 8 | [Stiff Problem II](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_8.pdf) |  A classic stiff linear initial-value problem |y'' + (101)*y' + 100*y = 0, y(0) = 1, y'(0) =-1 | ✅ Verified ||
| 9 | [Thomas-Fermi (IVP and BVP)](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_9.pdf) |  It appears naturally as one of the free coefficients of the Frobenius expansion |y''  = y**(3/2)/sqrt(x) | ✅ Verified ||
| 10 | [The Blasius](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_10.pdf) |  Solving a Famous Infinite-Domain Problem |2*f''' + f * f'' =0, f(0) =0, f'(0) =0, f'(infinity)=1 | ✅ Verified ||
| 11 | [Falkner-Skan](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_11.pdf) |  Generalized Blasius |f'''  = - f * f'' -beta*(1- f'**2), f(0) =0, f'(0) =0, f'(infinity)=1 | ✅ Verified ||
| 12 | [Eigenvalue Problem](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_12.pdf) |  A classical Sturm–Liouville eigenvalue problem |y'' = -lambda*y, y(0) =0, y(1) =0 | ✅ Verified ||
| 13 | [Integro-Differential Equation](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_13.pdf) |  A hard integral, sixteen times over |y′ + y = int_0^x  sin(100(x − t)) * y(t), y(0) = 1 | ✅ Verified ||
| 14 | [Certificate of Nonexistence](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_14.pdf) |  Can you prove there is no answer at all? |y'' + 3.52 eʸ = 0, y(0) = y(1) = 0 | ✅ Verified ||
| 15 | [The Fourth-Order Bratu Problem](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_15.pdf) |  A beam under a load that grows with deflection |y'''' = λeʸ,  y(0) = y(1) = 0  | ✅ Verified ||
| 16 | [HIRES](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_16.pdf)  |  Eight nonlinear equations, rate constants spanning five orders of magnitude| Eight Coupled Equations from Photochemistry  | ✅ Verified ||
| 17 | [Van der Pol Across Three Regimes](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_17.pdf) |  Relaxation Cycles,Verified, and the Cost of Stiffness |y″ = μ(1−y²)y′ − y at μ = 10, 100, 1000  | ✅ Verified ||
| 18 |  [Lane–Emden](https://github.com/sirpy26/SIRPY/blob/main/Problems/linkedin_18.pdf)  |  Starting Where the Equation Breaks |y″ = −2y′/x − y⁵, y(0) = 1, y′(0) = 0 | ✅ Verified ||
| 19 | [Parameter estimation](https://github.com/sirpy26/SIRPY/tree/main/Problems/torricelli) | First-order ODE, non-unique at h=0 | Torricelli draining tank (SIMIODE 1-015) — b = 0.002581 [0.002562, 0.002600]; verified on [0,100] to 4.31e-12 | ✅ Verified |


*(Numbering matches the SIRPY problem series posted publicly.)*


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