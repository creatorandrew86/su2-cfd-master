# Numerics

## Convective scheme (CONV_NUM_METHOD_FLOW)
Central schemes are more robust; upwind schemes are more accurate/less dissipative
but need MUSCL reconstruction for 2nd order in space.

- `ROE` — standard upwind default, good general-purpose choice.
- `AUSM` — robust at low Mach / mixed subsonic-supersonic. Not compatible with unsteady sims.
- `JST` — central, classic choice for Euler; tune via `JST_SENSOR_COEFF= (2nd_order, 4th_order)`.
- `JST_MAT` — JST with matrix dissipation, scaled by flux Jacobian; better viscous drag
  prediction on low-Re meshes than plain JST. Tune `ENTROPY_FIX_COEFF` (0.05-0.2 typical;
  larger = more dissipation).
- `LAX-FRIEDRICH`, `HLLC`, `TURKEL_PREC`, `MSW`, `CUSP` — also available, more specialized.



## MUSCL / reconstruction
- `MUSCL_FLOW= YES` enables 2nd-order upwind reconstruction. No effect on central
  schemes or coarse multigrid levels.
- Slope limiter only matters if MUSCL_FLOW= YES; with NO there's nothing to limit.

## Slope limiters (SLOPE_LIMITER_FLOW)
Damp 2nd-order terms near shocks/sharp gradients, keep full accuracy where smooth.
- `NONE` — no limiting; fine for smooth low-speed flows (e.g. laminar cylinder tutorial
  uses NONE successfully).
- `VENKATAKRISHNAN` — most common; tune `VENKAT_LIMITER_COEFF` (larger = less limiting,
  closer to unlimited; smaller = more limiting/diffusive). Default ~0.05, experiment per case.
- `VENKATAKRISHNAN_WANG`, `BARTH_JESPERSEN`, `VAN_ALBADA_EDGE` — alternatives.



## CFL
- `CFL_NUMBER=` starting value; keep low (~1-5) for RANS on stretched meshes or poor
  initial conditions.
- `CFL_ADAPT= YES` with `CFL_ADAPT_PARAM= (down, up, min, max, acceptable_lin_res)`
  grows CFL automatically as residuals drop — safer than picking one fixed high value.
- What counts as "high" CFL is case-dependent: for RANS/upwind on stretched meshes,
  high is >100 and low is <20; central schemes and time-domain problems shift these
  limits; Euler/Navier-Stokes on less-stretched meshes can go higher.



## Time discretization
- `EULER_IMPLICIT` — standard for steady-state, uses the linear solver every step.
- `EULER_EXPLICIT` / `RUNGE-KUTTA_EXPLICIT` — only for simple/small unsteady cases.



## Linear solver (used by any IMPLICIT/DUAL-TIME scheme)
- `FGMRES` + `ILU` preconditioner is the robust default; no universal "best" setting exists,
  since linear-system behavior is case-dependent.
- Elliptic-natured systems (e.g. incompressible, high CFL) are hardest to solve; still
  start with ILU.
- If convergence is poor, increase `LINEAR_SOLVER_ITER` — sustained cases needing >100
  linear iterations should switch to `RESTARTED_FGMRES` or `CONJUGATE_GRADIENT`.



## Turbulence numerics (CONV_NUM_METHOD_TURB)
- `SCALAR_UPWIND` is standard for SA/SST.
- `CFL_REDUCTION_TURB` scales CFL down for turbulence equations relative to flow CFL
  (they're often stiffer) — 1.0 default, reduce if turbulence residuals stall/diverge.



## Convergence
- `CONV_FIELD=` monitored residual field (e.g. RMS_DENSITY) or an output like DRAG.
- `CONV_RESIDUAL_MINVAL=` target log10 residual drop (e.g. -6 to -8).
- `CONV_CAUCHY_ELEMS` / `CONV_CAUCHY_EPS` — alternative: stop once a monitored output
  (e.g. force coefficients) stabilizes, useful when residuals plateau before coefficients do.