# Solvers and physics

## `SOLVER`

| Family | Values | Use |
|---|---|---|
| Compressible FVM | `EULER`, `NAVIER_STOKES`, `RANS` | Inviscid; viscous laminar; viscous turbulent. |
| Incompressible FVM | `INC_EULER`, `INC_NAVIER_STOKES`, `INC_RANS` | Same progression; use the `INC_*` condition section, not compressible freestream settings. |
| Nonequilibrium | `NEMO_EULER`, `NEMO_NAVIER_STOKES` | Finite-rate, multispecies, thermal-nonequilibrium flow. |
| High-order DG | `FEM_EULER`, `FEM_NAVIER_STOKES`, `FEM_RANS`, `FEM_LES` | Inviscid, laminar, RANS, or LES (`KIND_SGS_MODEL`). |
| Other | `HEAT_EQUATION_FVM`, `ELASTICITY`, `MULTIPHYSICS` | Conduction; structural mechanics; coupled zones. |

`HEAT_EQUATION` is deprecated; use `HEAT_EQUATION_FVM`. `MULTIPHYSICS` requires `CONFIG_LIST= (configA.cfg, configB.cfg, ...)`.

## Required companions

| Solver | Also set |
|---|---|
| Euler variants | `FLUID_MODEL` and freestream or `INC_*` conditions |
| Navier-Stokes variants | Above + `VISCOSITY_MODEL`, `CONDUCTIVITY_MODEL` |
| RANS variants | Above + `KIND_TURB_MODEL`, freestream turbulence values, `CONV_NUM_METHOD_TURB` |
| NEMO | `GAS_MODEL`, `GAS_COMPOSITION`, `FREESTREAM_TEMPERATURE_VE` |
| Heat | `THERMAL_CONDUCTIVITY_CONSTANT`, `MATERIAL_DENSITY`, thermal markers |
| Elasticity | `MATERIAL_MODEL` and structural markers |

## Turbulence and adjoints

- `KIND_TURB_MODEL= NONE, SA, SST`. `SA` is a robust one-equation choice for attached/mildly separated flow; `SST` generally handles separation/adverse pressure gradients better. Use their `*_OPTIONS` only when needed.
- Transition: `KIND_TRANS_MODEL= NONE, LM`; use `LM_OPTIONS` for the required correlation.
- Hybrid RANS/LES: `HYBRID_RANSLES= SA_DES, SA_DDES, SA_ZDES, SA_EDDES`; requires RANS and `TIME_DOMAIN= YES`.
- `MATH_PROBLEM= DIRECT, CONTINUOUS_ADJOINT, DISCRETE_ADJOINT`. Use `DIRECT` normally; set an adjoint only for requested sensitivities/optimization. Prefer discrete adjoint when applicable.
