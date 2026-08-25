# SU2 Solvers — Full Reference

## SOLVER keyword — all possible values
SOLVER= EULER, NAVIER_STOKES, RANS,
INC_EULER, INC_NAVIER_STOKES, INC_RANS,
NEMO_EULER, NEMO_NAVIER_STOKES,
FEM_EULER, FEM_NAVIER_STOKES, FEM_RANS, FEM_LES,
HEAT_EQUATION_FVM, ELASTICITY

### Compressible
- `EULER` — inviscid, no boundary layer, no turbulence model.
- `NAVIER_STOKES` — viscous, laminar. Requires VISCOSITY_MODEL, CONDUCTIVITY_MODEL.
- `RANS` — viscous + turbulence closure. Requires KIND_TURB_MODEL.

### Incompressible (INC_ prefix)
- `INC_EULER` — inviscid, incompressible.
- `INC_NAVIER_STOKES` — viscous, laminar, incompressible.
- `INC_RANS` — viscous + turbulence, incompressible.
- All three use the INCOMPRESSIBLE FLOW CONDITION DEFINITION section
  (INC_DENSITY_MODEL, INC_VELOCITY_INIT, INC_NONDIM, etc.) instead of the
  compressible free-stream block.

### Nonequilibrium / hypersonic (NEMO)
- `NEMO_EULER` / `NEMO_NAVIER_STOKES` — finite-rate chemistry, multi-species,
  thermal nonequilibrium flows. Needs GAS_MODEL, GAS_COMPOSITION,
  FREESTREAM_TEMPERATURE_VE (vibrational temp), IONIZATION.

### High-order FEM / DG
- `FEM_EULER`, `FEM_NAVIER_STOKES` — discontinuous Galerkin, inviscid/viscous.
- `FEM_RANS` — DG + turbulence.
- `FEM_LES` — DG large-eddy simulation. Uses KIND_SGS_MODEL
  (NONE, IMPLICIT_LES, SMAGORINSKY, WALE, VREMAN).

### Other physics
- `HEAT_EQUATION_FVM` — standalone conduction/heat solver (finite volume only,
  no flow). Older tutorials may show `HEAT_EQUATION` — that name is deprecated,
  current SU2 expects `HEAT_EQUATION_FVM`.
- `ELASTICITY` — structural solid mechanics (linear/nonlinear elasticity),
  used standalone or coupled in FSI.
- `MULTIPHYSICS` — driver for coupled multizone problems (e.g. CHT, FSI).
  Requires `CONFIG_LIST= (configA.cfg, configB.cfg, ...)`, one sub-config per zone.

## Turbulence models (RANS / INC_RANS / FEM_RANS)
`KIND_TURB_MODEL= NONE, SA, SST`
- `SA` — Spalart-Allmaras, one-equation, robust default for attached/mildly
  separated flow. Modifiers via `SA_OPTIONS`: NEGATIVE, EDWARDS, WITHFT2,
  QCR2000, COMPRESSIBILITY, ROTATION, BCM, EXPERIMENTAL.
- `SST` — Menter k-omega SST, two-equation, better for separation/adverse
  pressure gradient. Modifiers via `SST_OPTIONS`: V2003m, V1994m, VORTICITY,
  KATO_LAUNDER, UQ, SUSTAINING, COMPRESSIBILITY-WILCOX, COMPRESSIBILITY-SARKAR,
  DIMENSIONLESS_LIMIT.

### Transition
`KIND_TRANS_MODEL= NONE, LM` (Langtry-Menter), with `LM_OPTIONS`: LM2015,
MALAN, SULUKSNA, KRAUSE, KRAUSE_HYPER, MEDIDA, MEDIDA_BAEDER, MENTER_LANGTRY.

### Hybrid RANS/LES
`HYBRID_RANSLES= SA_DES, SA_DDES, SA_ZDES, SA_EDDES` — only meaningful when
combined with a RANS solver and TIME_DOMAIN= YES (unsteady).

## Required companion settings by solver family
| SOLVER family | Needs |
|---|---|
| EULER / INC_EULER / FEM_EULER | FLUID_MODEL, freestream or INC_* conditions |
| NAVIER_STOKES / INC_NAVIER_STOKES / FEM_NAVIER_STOKES | + VISCOSITY_MODEL, CONDUCTIVITY_MODEL |
| RANS / INC_RANS / FEM_RANS | + KIND_TURB_MODEL, FREESTREAM_TURBULENCEINTENSITY, FREESTREAM_TURB2LAMVISCRATIO, CONV_NUM_METHOD_TURB |
| NEMO_EULER / NEMO_NAVIER_STOKES | + GAS_MODEL, GAS_COMPOSITION, FREESTREAM_TEMPERATURE_VE |
| HEAT_EQUATION_FVM | THERMAL_CONDUCTIVITY_CONSTANT, MATERIAL_DENSITY, MARKER_ISOTHERMAL/HEATFLUX |
| ELASTICITY | MATERIAL_MODEL, structural BCs (MARKER_CLAMPED, etc. — not covered here) |
| MULTIPHYSICS | CONFIG_LIST with one .cfg per zone |

## MATH_PROBLEM
`MATH_PROBLEM= DIRECT, CONTINUOUS_ADJOINT, DISCRETE_ADJOINT`
- `DIRECT` — standard forward simulation (default for normal runs).
- `CONTINUOUS_ADJOINT` / `DISCRETE_ADJOINT` — gradient/sensitivity computation
  for shape optimization. Every direct solver has a corresponding adjoint
  formulation; set only if the user explicitly needs optimization/sensitivities.
  Discrete adjoint is the modern/recommended approach over continuous adjoint.