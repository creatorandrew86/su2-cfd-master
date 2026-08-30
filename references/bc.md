# Boundary conditions

A mesh `MARKER_TAG` is a named boundary; `MARKER_*` options assign its physics.

| Marker | Purpose |
|---|---|
| `MARKER_EULER= ( name )` | Inviscid/slip wall |
| `MARKER_HEATFLUX= ( name, q, ... )` | No-slip wall with heat flux; `q= 0.0` is adiabatic |
| `MARKER_ISOTHERMAL= ( name, T_wall, ... )` | No-slip wall at fixed temperature |
| `MARKER_FAR= ( name )`, `MARKER_SYM= ( name )` | Farfield using freestream; symmetry plane |
| `MARKER_INLET= ( name, T0, P0, dx, dy, dz, ... )` | Subsonic inlet |
| `MARKER_SUPERSONIC_INLET= ( name, T, P, vx, vy, vz, ... )` | Supersonic inlet |
| `MARKER_OUTLET= ( name, P, ... )`, `MARKER_SUPERSONIC_OUTLET= ( name )` | Subsonic static-pressure outlet; supersonic/extrapolated outlet |
| `MARKER_PERIODIC` | Periodic pairs |
| `MARKER_WALL_FUNCTIONS= ( name, STANDARD_WALL_FUNCTION, ... )` | Wall model for unresolved boundary layers (`y+ > 1`) |

Monitoring/output uses `MARKER_MONITORING`, `MARKER_PLOTTING`, and `MARKER_ANALYZE` with `MARKER_ANALYZE_AVERAGE`.

- Names are case-sensitive and must exactly match the mesh.
- Give every mesh marker one boundary condition, unless it is intentionally left at its default. Do not repeat a `MARKER_*` type.
- For RANS inlets without `MARKER_INLET_TURBULENT`, SU2 uses the freestream turbulence intensity and viscosity ratio.
