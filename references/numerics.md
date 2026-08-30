# Numerics

## Flow discretization

| Setting | Guidance |
|---|---|
| `CONV_NUM_METHOD_FLOW` | `ROE` is a practical general upwind choice. `AUSM` suits low-Mach/mixed flow but not unsteady cases. `JST` is a classic central Euler scheme; `JST_MAT` can improve viscous drag prediction. Other supported choices are case-specific. |
| `MUSCL_FLOW= YES` | Enables second-order upwind reconstruction; has no effect for central schemes or coarse multigrid levels. |
| `SLOPE_LIMITER_FLOW` | Only matters with MUSCL. Use `NONE` for sufficiently smooth cases; `VENKATAKRISHNAN` is common. Larger `VENKAT_LIMITER_COEFF` limits less; tune per case. |

Central schemes are generally more robust; upwind schemes are less dissipative and need MUSCL for second-order space. For `JST`, tune `JST_SENSOR_COEFF`; for `JST_MAT`, `ENTROPY_FIX_COEFF` is typically `0.05–0.2` (larger is more dissipative).

## Marching and linear solve

- Start `CFL_NUMBER` around `1–5` for RANS with stretched meshes or weak initialization. `CFL_ADAPT= YES` is safer than a high fixed CFL; its useful range depends on solver, mesh, and time mode.
- Use `EULER_IMPLICIT` for steady cases. `EULER_EXPLICIT` or `RUNGE-KUTTA_EXPLICIT` suit only simple/small unsteady cases.
- For implicit or dual-time schemes, start with `FGMRES` + `ILU`. If convergence remains poor, increase `LINEAR_SOLVER_ITER`; sustained need above ~100 iterations suggests `RESTARTED_FGMRES` or `CONJUGATE_GRADIENT`.
- For SA/SST, use `CONV_NUM_METHOD_TURB= SCALAR_UPWIND`. Reduce `CFL_REDUCTION_TURB` below `1.0` if turbulence equations stall or diverge.

## Convergence

- Choose `CONV_FIELD` for a meaningful residual or output.
- Use `CONV_RESIDUAL_MINVAL` for a residual target (often `-6` to `-8`) and Cauchy criteria for stabilized outputs.
- A residual drop alone is insufficient: also check relevant forces, flows, heat transfer, and active equations.
