# Boundary Conditions

A Marker is a named entity from the mesh file. `MARKER_*` options assign
physics to that name.

## Common markers
- `MARKER_EULER= ( name )` — inviscid (slip) wall.
- `MARKER_HEATFLUX= ( name, q, ... )` — no-slip wall, fixed heat flux (0.0 = adiabatic).
- `MARKER_ISOTHERMAL= ( name, T_wall, ... )` — no-slip wall, fixed temperature.
- `MARKER_FAR= ( name )` — farfield, uses freestream conditions.
- `MARKER_SYM= ( name )` — symmetry plane.
- `MARKER_INLET= ( name, T_total, P_total, dir_x, dir_y, dir_z, ... )` — subsonic inlet.
- `MARKER_SUPERSONIC_INLET= ( name, T_static, P_static, vx, vy, vz, ... )` — supersonic inlet.
- `MARKER_OUTLET= ( name, P_static, ... )` — subsonic outlet.
- `MARKER_SUPERSONIC_OUTLET= ( name )` — supersonic/extrapolated outlet.
- `MARKER_PERIODIC` — periodic boundary pairs.
- `MARKER_WALL_FUNCTIONS= ( name, STANDARD_WALL_FUNCTION, ... )` — wall model for
  coarse near-wall resolution (y+ > 1) instead of resolving the boundary layer.

## Monitoring/output
- `MARKER_MONITORING= ( name )` — surfaces integrated for force/moment coefficients.
- `MARKER_PLOTTING= ( name )`
- `MARKER_ANALYZE= ( name )` + `MARKER_ANALYZE_AVERAGE=` — averaged quantities at a surface.

## General guidelines
- Names are case-sensitive and must match the mesh's Physical Group / marker names exactly.
- Every marker in the mesh should map to exactly one BC (or be intentionally left default).
- If `MARKER_INLET_TURBULENT` isn't set for RANS, SU2 falls back to
  FREESTREAM_TURBULENCEINTENSITY / FREESTREAM_TURB2LAMVISCRATIO at inlets.
- Use `MARKER_HEATFLUX= (name, 0.0)` for adiabatic (no-slip) walls unless a real wall
  temperature is known.
- Do not use more than 1 of the same MARKER_*.