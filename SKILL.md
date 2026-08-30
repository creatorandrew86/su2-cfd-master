---
name: su2-cfd-master
description: Create, edit, and debug SU2 CFD configuration files (.cfg) and SU2_CFD runs.
---

# SU2 CFD configuration

## Build a case

1. Choose `SOLVER` and `MATH_PROBLEM` (`DIRECT` unless optimization/adjoints are requested). See `references/solvers.md`.
2. Inspect the mesh dimension and marker names. See `references/mesh.md`.
3. Assign each mesh marker the appropriate `MARKER_*` boundary condition. Names are case-sensitive. See `references/bc.md`.
4. Choose applicable flow, turbulence, and numerical settings. See `references/numerics.md`.
5. Use `assets/master.cfg` as the only option-name/value reference; never invent options. Extract only the needed sections and keys as described in `references/init.md`.
6. Verify `MESH_FILENAME` and `MESH_FORMAT`, marker names, solver compatibility, and that no irrelevant options remain.

## Rules

- Use `KEY= VALUE`; `%` starts a comment.
- Use SI units unless `REF_DIMENSIONALIZATION` changes them; state this explicitly.
- Include only settings required by the solver or needed to override a sensible default.

## Debugging

For startup errors, convergence issues, or questionable results, follow `references/debugging.md`.
