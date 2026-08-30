# Generating a case `.cfg`

`assets/master.cfg` is the authoritative option reference, not a file to copy wholesale.

## Process

1. Identify the applicable sections from the solver, flow regime, and enabled physics.
2. For each section, copy only required keys or deliberate overrides; omit inapplicable sections and unused defaults.
3. Keep the original `% ---- SECTION ----%` banners.
4. Before delivery, verify every marker exists in the mesh and every option is supported and required by the selected solver.

## Format

- Use dense logical groups: blank lines between groups, not between options.
- Do not retain `master.cfg`'s separator/comment block for every option.
- Add a short comment only for units, rationale, or a physical link.
- One `KEY= VALUE` per line; no trailing `%`.

```text
% ---------------- INCOMPRESSIBLE FLOW CONDITION DEFINITION -------------------%
INC_DENSITY_MODEL= CONSTANT
INC_ENERGY_EQUATION= NO
INC_DENSITY_INIT= 998.2
INC_VELOCITY_INIT= ( 1.0, 0.0, 0.0 )
```
