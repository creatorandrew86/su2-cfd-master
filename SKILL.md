---
name: su2-cfd-master
description: Use this skill when creating or editing SU2 CFD configuration files (.cfg) and setting up SU2_CFD solver runs
---

# SU2 CFD Configuration

## Workflow
1. Identify problem type: EULER, NAVIER_STOKES, RANS, or another solver variant
   (incompressible, FEM/DG, heat, NEMO, etc.). See `references/solver_types.md`.
2. Identify or generate the mesh. If starting from a Gmsh `.geo` file, see
   `references/mesh_su2_format.md` for the conversion command and Physical Group rules.
3. Match `MARKER_*` entries in the `.cfg` to the Physical Group names in the mesh.
   See `references/boundary_markers.md`.
4. Set numerics (convective scheme, CFL, limiters) appropriate to the case.
   See `references/numerics.md`.
5. Build the actual `.cfg` from `assets/master.cfg` — this is the required base/source
   of truth for every option and its documentation. See `references/init.md`
   for the extraction and formatting process. Never invent option names or values.
6. Verify MESH_FILENAME / MESH_FORMAT match the actual mesh file.
7. Sanity-check the finished config: every included option is actually used by the
   chosen SOLVER, every MARKER_* matches the mesh, nothing extraneous was copied in.

## Key conventions
- Config syntax: `KEY= VALUE`, comments start with `%`.
- Always set MATH_PROBLEM (DIRECT unless doing adjoint/optimization).
- SU2 expects SI units unless REF_DIMENSIONALIZATION says otherwise — flag this explicitly.
- Boundary marker names are case-sensitive and must match the mesh exactly.
- `assets/master.cfg` is the base for ALL generated config files. Only pull in
  sections/options relevant to the case

## When debugging
Differentiate physical vs. non-physical issues first (divergence from bad BCs/mesh quality
vs. flow physics). Check convergence/residual behavior before changing physical
setup. Cross-reference and check SU2 forum threads (cfd-online.com/Forums/su2) and the official docs
(su2code.github.io/docs) for solver-specific error messages and other problems.