# Torricelli draining tank — certified parameter estimation

A worked example of SIRPY fitting a parameter to noisy classroom data and reporting what the data can and cannot pin down.

## Source

Data and model from the SIMIODE community, Modeling Scenario 1-015, courtesy of Brian Winkel (Director of SIMIODE). The original lesson focuses on building the model; here we ask what a verified solver can add.

## Data

``` python
import numpy as np, math
t = np.array([0, 2.187, 6.933, 9.717, 17.102, 22.968, 30.603, 39.503, 47.663])
h = np.array([11.1, 10.6, 9.3, 8.6, 7.0, 5.75, 4.4, 3.0, 2.0])
```

## Model

Torricelli's law for the height $h(t)$ of water draining from a tank:

$$dh/dt = -b\sqrt{gh}$$

The single unknown constant $b$ absorbs the hole geometry and discharge coefficient.

## Result

**Estimate and interval.**

$b = 0.002581$, 95% interval $[0.002562, 0.002600]$

SSE $= 0.0104$. The data pins the constant to about ±0.7%.

**Model check.** The misfits have mixed signs with no pattern: `--++-+-+-`. The largest is 0.081 cm.

**A prediction the data cannot make.** The tank empties at $t = 82.47$ s (95%: 81.87 to 83.08 s), about 35 s after the last measurement.

**Verified continuation past a non-unique point.** At $h = 0$ the equation loses the property that guarantees a unique solution, so a solver can misreport this point as a blow-up or simply fail. SIRPY did neither. It identified a branch point at $t = 82.47478538$ s, said so in plain words, and continued with the physical answer, $h = 0$. It then verified the whole solution on $[0, 100]$ to a residual of

$$4.31 \times 10^{-12}.$$

## Exact call and output

``` python
sirpy.solve_ivp(order=1, f_str="-%r*sqrt(y)" % float(b*sg),
                x0=0, x1=100, ic={0: 11.1})
```

Solver output, unedited:

```         
Piecewise power-series solution (30 segments of degree 20 covering [0, 100])

  segment 1: [0, 28.0463]  centered at 0
  segment 2: [28.0463, 45.3166]  centered at 28.0463
  segment 3: [45.3166, 56.3802]  centered at 45.3166
  ... (27 more segments)
Access: r.solution (SymPy Piecewise), r.evaluate(var, x), r['series'][var]['coefficients'][segment_index]

INFO: run summary — series degree 20, iterations=20 (sirpy default), 30 series segment(s).
INFO: verification by substitution — VERIFIED: satisfies the equation to 4.31e-12 on [0, 100]. Checked by exact coefficient differentiation at interior points, not at the interface nodes. Interface continuity across 29 node(s): max one-sided jump in (y..y0) = 3.0e-13 — each side's own coefficients evaluated exactly at the node. Also checked: initial data to 0.00e+00.
INFO: stiffness EMERGED mid-interval — the local Jacobian |eigenvalue| grew from 0.0242 at x=0 to 267 near x ~ 82.47 (ratio ~ 1.1e+04). The segment marcher adapted its step automatically; for extreme stiffness consider stiff_method='scipy'.
INFO: an earlier signal near 82.47478538 (the steps shrank toward a point) was a BRANCH POINT of the equation, not a pole: the solution stays finite there. Superseded by the verdict below.
INFO: BRANCH POINT of the equation at x = 82.47478538: the square root's radicand vanishes at y = 0, where the equation is not Lipschitz. The classical maximal solution continues as the CONSTANT y = 0 up to x = 100, and that glued solution is what is returned and verified.

branch point at t = 82.47478538   status: solved+verified   h(90) = 0.0
```

## Figures

- `torricelli_fig1.png` — continuation past empty; the shaded region is the extrapolation beyond the last measurement.
- `torricelli_fig2_sirpy_words.png` — SIRPY's own unedited output.

## Reproducibility

Every number above is reproducible from the delivered artifact. The solver engine is proprietary and not distributed here; the reported output and verification figures are the solver's own.

## Credit

Thanks to Brian Winkel and SIMIODE (Modeling Scenario 1-015) for the data and the inspiration.
