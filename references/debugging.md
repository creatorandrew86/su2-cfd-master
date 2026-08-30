# SU2 debugging

Use the exact error, `.cfg`, mesh, and solver output; a screenshot alone is not enough.

## Workflow

1. Classify: startup/runtime error, non-convergence, or suspicious result.
2. Collect SU2 version, command, full terminal output, `.cfg`, `.su2`, and relevant restart/output files.
3. Check input files before changing numerics; use the mesh, boundary, solver, and numerics references.
4. Compare with the closest official tutorial/test case.
5. Change one hypothesis at a time, rerun, and record the effect.
6. Search the exact versioned error/warning in official SU2 sources, GitHub issues, and CFD-Online. Treat forum advice as a hypothesis.

## Inspection limits

- Inspect only headers, metadata, markers, statistics, diagnostics, or target regions of `.vtu`, `.vtk`, and `.su2` files by default.
- Inspect a full file only on explicit request, or after explaining why and receiving approval. State the file, purpose, and checks; do not dump it into chat.
- A suspected mesh problem requires a report of the evidence, impact, and target region/property; it is not approval for full-file inspection.

## Startup/runtime error

| Check | Verify |
|---|---|
| Configuration | Solver, problem type, mesh filename/format, restart/output settings, and every case-sensitive marker name. Remove misspelled, obsolete, or incompatible keys; read the first configuration error literally. |
| Environment | Paths, working directory, SU2 executable/version, and serial vs parallel mode. |
| Mesh | Existence/readability, format, dimension, counts, connectivity, markers, boundary assignment, orientation, quality, invalid or disconnected regions. Use SU2 diagnostics when available. |
| Version | If failure followed an upgrade, compare matching-version templates/options and run the same tutorial with the same executable. |

## Non-convergence

Classify first: immediate `NaN` usually means invalid input/initialization/BC or an aggressive first update; oscillation suggests stability or mesh issues; a plateau may be physics, conditioning, monitors, or genuinely unsteady flow.

### Inputs and mesh

- Check skewed, stretched, tiny, inverted, negative-volume cells; abrupt size changes; and resolution of boundary layers, wakes, and shocks.
- Verify connectivity, boundary orientation, markers, BC type, units, references, freestream, turbulence, wall treatment, and initialization.
- Inspect `ORTHOGONALITY`, `ASPECT_RATIO`, and `VOLUME_RATIO` if available; correlate bad cells with local residuals or nonphysical values.

### Monitor correctly

- Do not use iteration count alone. For steady flow, monitor a relevant residual and stabilized outputs (Cauchy criteria).
- Monitor all active equations and quantities of interest: forces, moments, mass flow, heat transfer, and conservation. Falling residuals with drifting outputs are not convergence.
- For unsteady/periodic flow, check time-step independence, inner convergence, and windowed or period-averaged quantities—not steady residuals.
- Use `SU2_CFD -d <config>` to list valid output fields before selecting monitors.

### Stabilize methodically

- Lower or ramp CFL. Start with a robust, more dissipative/first-order setup, then add MUSCL, second order, and a limiter one change at a time from the last stable restart.
- Match low-Mach preconditioning, shock schemes, and limiters to the regime; do not assume the least-dissipative method is stable.
- Check linear-solver type, preconditioner, tolerance, and iterations. High CFL often needs `ILU`; too few iterations or an unsuitable tolerance can make nonlinear updates ineffective.
- Temporarily disable multigrid, advanced limiters, and accelerators to establish a baseline; re-enable after it is stable.
- Use continuation from an easier nearby case. Verify restart mesh, variables, iteration, and SU2/restart format compatibility.

Search the exact pattern with the SU2 version, solver, mesh type, and BCs. Compare similar cases and record differences.

## Validate a converged result

Convergence is evidence, not proof. Check separately whether equations converged, outputs stabilized, physics is plausible, and the answer is sufficiently insensitive to mesh, time step, initialization, and numerics.

- Review active residuals, outputs, and conservation; require physical bounds (positive density/pressure/temperature, valid turbulence quantities, no unexplained NaNs/clipping).
- Confirm boundary fluxes, integration markers, signs, axes, reference point, and dimensionalization.
- Locate extreme gradients/residuals and inspect mesh quality there; plausible contours can conceal decisive bad cells.
- Test mesh refinement, time step/inner iterations (unsteady), reasonable numerical choices, initialization/continuation, and suitable model/wall-treatment alternatives.
- Compare with analytical, experimental, published, or official SU2 cases only after matching geometry, mesh, physics, BCs, nondimensionalization, and convergence criteria.

For external evidence, prioritize official documentation, SU2 issues, and established forums. Report what is verified, what is plausible, and remaining uncertainty (numerical, mesh, model, post-processing, or version).
