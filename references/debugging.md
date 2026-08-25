# SU2 Debugging

Use this reference when an SU2 run fails to start, diverges or stalls, or produces a result that may be physically wrong. Diagnose from the exact error, the `.cfg`, the mesh, and the solver output; do not guess from a screenshot alone.

## Workflow

1. Identify the failure type: startup/runtime error, non-convergence, or suspicious result.
2. Collect the SU2 version, command, complete terminal output, `.cfg`, `.su2` mesh, and relevant restart/output files.
3. Check the input files before changing numerics. Use `references/mesh.md`, `references/bc.md`, `references/numerics.md`, and `references/solvers.md` as needed.
4. Compare the case with the closest official SU2 tutorial or test case.
5. Make one controlled change at a time, rerun, and record whether the symptom changed.
6. Search the exact error or warning, including the SU2 version, on the official documentation, SU2 GitHub issues, and CFD-Online SU2 forums. Treat forum suggestions as hypotheses and verify them against the case.

## Program does not run

Check the `.cfg` first:

- Confirm the solver, problem type, mesh filename, mesh format, restart settings, and required output names.
- Check every `MARKER_*` name against the mesh marker tags; names are case-sensitive.
- Remove misspelled, obsolete, or solver-incompatible options. Compare the file with the matching version's `config_template.cfg` and read the first configuration error literally.
- Confirm paths, working directory, executable version, and parallel/serial mode.

Then check the mesh:

- Confirm the file exists, is readable, and is in the expected native `.su2` or CGNS format.
- Confirm its dimension, point/element counts, connectivity, and marker blocks are internally consistent.
- Confirm every marker referenced by the configuration exists and has the intended boundary elements.
- Check for invalid geometry, missing boundaries, duplicate or disconnected regions, bad element orientation, negative volumes, or poor quality. Use SU2's printed mesh diagnostics and mesh-quality output when available.

If the error appeared after an SU2 upgrade, check version-specific option changes and whether the same tutorial case runs with the same executable.

## Convergence problems

First classify the symptom: immediate NaNs usually indicate invalid input, initialization, boundary data, or an aggressive first update; oscillating residuals suggest stability or mesh problems; a residual plateau may indicate unresolved physics, poor conditioning, an unsuitable convergence field, or a genuinely unsteady flow.

### Check mesh and physical setup

- Look for highly skewed, stretched, tiny, inverted, or negative-volume cells; abrupt size transitions; and poorly resolved boundary layers, wakes, or shocks.
- Check element connectivity and boundary orientation, and confirm that every marker has the intended elements and boundary condition.
- Check units, reference values, freestream state, turbulence inputs, wall treatment, initialization, and the physical type of every boundary.
- Export or inspect `ORTHOGONALITY`, `ASPECT_RATIO`, and `VOLUME_RATIO` when available. Correlate bad cells with local residual peaks or nonphysical values instead of relying only on global mesh statistics.

### Define and observe convergence correctly

- Do not use iteration count alone. For steady runs, choose a relevant `CONV_FIELD`: monitor residuals with `CONV_RESIDUAL_MINVAL` and monitor important coefficients with the Cauchy criteria (`CONV_CAUCHY_ELEMS`, `CONV_CAUCHY_EPS`).
- Monitor all relevant equations, not only density: momentum, energy, turbulence, species, or coupled-physics residuals as applicable.
- Track forces, moments, mass flow, heat transfer, and other quantities of interest in the history file. A falling residual with drifting coefficients is not converged for that objective.
- For unsteady or periodic problems, do not demand a steady residual. Check time-step independence, inner-iteration convergence, and windowed convergence of period-averaged quantities.
- Use `SU2_CFD -d <config>` to discover output fields supported by the selected solver before choosing convergence monitors.

### Stabilize in stages

- Lower `CFL_NUMBER` or use CFL ramping when the first updates are unstable. Increase CFL only after the flow is stable, and stop increasing when linear solves become expensive or instability returns.
- Begin with a robust first-order or more dissipative scheme, then enable MUSCL/second-order reconstruction and an appropriate limiter after a stable solution exists. If a second-order switch causes divergence, restart from the last stable solution and change one numerical option at a time.
- For low-Mach problems, check whether low-speed/preconditioning options are appropriate. For shocks or strong gradients, compare compatible upwind schemes and limiters rather than assuming the most accurate scheme is the most robust.
- For implicit methods, inspect `LINEAR_SOLVER`, `LINEAR_SOLVER_PREC`, `LINEAR_SOLVER_ERROR`, and `LINEAR_SOLVER_ITER`. A high CFL may require a stronger preconditioner such as `ILU`; a very tight linear tolerance or too few linear iterations can make each nonlinear update ineffective.
- Temporarily disable multigrid, advanced limiters, or other acceleration features to isolate whether the base solver converges. Re-enable them after a stable baseline is established.
- Use continuation when the target case is difficult: solve a simpler nearby case first, then change Mach number, angle, Reynolds number, geometry, boundary data, turbulence model, or other difficult parameter in small steps, restarting from the previous solution.
- Verify restarts use the same mesh, compatible variable set, correct iteration, and a compatible SU2/restart format. A bad restart can hide the original problem or create a new one.

Search the exact warning/error and pattern (for example, `NaN detected`, residual stall, divergence after switching to second order, or failure only with multigrid), together with the SU2 version, solver, mesh type, and boundary conditions. Compare similar cases while recording what differs. Treat web advice as a hypothesis and make each change test one hypothesis.

## Solution skepticism

Treat a converged run as a candidate result, not proof. Separate these questions:

- Did the iterative solver reduce the equations' residuals?
- Did the requested outputs, such as lift, drag, heat flux, or mass flow, stabilize?
- Is the flow physically plausible for the stated model and boundary conditions?
- Is the result insensitive enough to mesh, time step, initialization, and numerical method?

Check all of the following:

- Residual histories for every active equation, relevant integrated coefficients, and conservation of mass, momentum, energy, or species as applicable.
- Physical bounds: positive density/pressure/temperature, reasonable Mach and velocity fields, nonnegative turbulence quantities where required, and no unexplained NaNs or clipped values.
- Boundary fluxes and marker selection. Confirm that forces or heat fluxes are integrated on the intended marker, with the correct sign, reference point, axes, and dimensionalization.
- Mesh-quality fields and locations of extreme residuals, gradients, pressure, temperature, or turbulence values. A plausible contour can hide a few cells that dominate the answer.
- Mesh sensitivity using systematically refined meshes or local refinement near the feature controlling the result. A result that changes materially is not mesh-independent.
- Time-step and inner-iteration sensitivity for unsteady cases. Distinguish a converged periodic average from a false steady solution.
- Numerical sensitivity: compare reasonable CFL values, first- versus second-order schemes, limiters, and compatible solvers. Large changes in the reported quantity indicate numerical error or an under-resolved flow.
- Initialization and continuation sensitivity. Run from freestream and, where possible, from a nearby converged case; different final states may indicate multiple physical states, hysteresis, or a setup problem.
- Model sensitivity: compare laminar/RANS models, wall treatment, turbulence resolution, or other appropriate models without presenting model agreement as validation.
- Agreement with an analytical result, experiment, published case, or official SU2 tutorial/test case. Match geometry, mesh resolution, physics, boundary conditions, nondimensionalization, and convergence criteria before comparing numbers.

Search the internet for the exact phenomenon and comparable geometry/physics, prioritizing official SU2 documentation, SU2 GitHub issues, and established SU2 forum discussions. Look for repeated reports, version-specific behavior, and evidence that a proposed fix worked on a comparable case. Report what is verified, what is only plausible, and whether the remaining uncertainty is numerical, mesh-related, physical-model-related, post-processing-related, or an SU2/version problem.
