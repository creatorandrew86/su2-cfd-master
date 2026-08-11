# Generating a Case .cfg from master.cfg

`assets/master.cfg` is the source of truth: a comprehensive SU2 config file
listing every available option, grouped into commented sections, with each
option preceded by an explanatory comment block. It is NOT meant to be copied.

## Process
1. Read `assets/master.cfg` in full.
2. Determine which sections apply to the case (based on SOLVER type, flow regime
   — compressible vs incompressible, physics involved — heat transfer, turbulence,
   moving mesh, etc.). Skip entire sections that don't apply
   (e.g. skip INCOMPRESSIBLE FLOW CONDITION DEFINITION if SOLVER is compressible RANS).
3. Within a relevant section, only take the specific keys the case actually needs.
   Do not include an option just because it exists in master.cfg — only include it
   if it changes from a sensible default or is required for the solver/BC/physics chosen.
4. Preserve the section header banners (the `% ---- SECTION NAME ----%` lines) from
   master.cfg to keep files recognizable/navigable.


## Output formatting rules (differs from master.cfg's writings style)
- Do NOT keep the `%` separator lines between every option (master.cfg uses these
  for readability as a reference document — the generated case file should be denser).
- Keep section header banners.
- Add a short inline comment only where the value needs explanation (units, why this
  choice, what it links to physically).
- Group related options together with a blank line between logical clusters, not
  between every single line.
- One value per KEY= line, no trailing `%`.

## Example transformation

master.cfg style (reference):

% Density model within the incompressible flow solver.
% Options are CONSTANT (default), BOUSSINESQ, VARIABLE, or FLAMELET.
INC_DENSITY_MODEL= CONSTANT
%
% Solve the energy equation in the incompressible flow solver
INC_ENERGY_EQUATION = NO
%


Generated case .cfg style (denser):

% ---------------- INCOMPRESSIBLE FLOW CONDITION DEFINITION -------------------%
INC_DENSITY_MODEL= CONSTANT
INC_ENERGY_EQUATION= NO
INC_DENSITY_INIT= 998.2
INC_VELOCITY_INIT= ( 1.0, 0.0, 0.0 )


## Final check
Before presenting the generated .cfg, confirm:
- Every MARKER_* references a name that exists in the case's mesh.
- Every option included is actually consumed by the chosen SOLVER (cross-check
  against `references/solvers.md`).
- No option was pulled into the file "just in case" — trim anything not doing work.