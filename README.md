# SU2 CFD Skill

This skill helps an AI agent create, inspect, and debug SU2 computational-fluid-dynamics runs. It focuses on producing valid `.cfg` files, matching them to native `.su2` meshes, and diagnosing failures using the actual solver, physics, boundary markers, numerics, and output.

The workflow begins by identifying the solver and physical problem, then checking mesh structure and marker names before selecting boundary conditions and numerical methods. Configuration options are taken from the supplied master template rather than invented. The skill also provides guidance for startup errors, divergence, stalled convergence, and results that appear plausible but require verification.

Reference files cover:

- solver and physics selection;
- native `.su2` mesh structure and boundary markers;
- boundary-condition conventions;
- numerical methods and initialization;
- debugging, convergence, and solution skepticism.

Use the debugging guidance to compare `.cfg` files, meshes, logs, official SU2 examples, GitHub issues, and relevant CFD-Online discussions. Treat convergence and physical correctness as separate questions, and make changes one hypothesis at a time.
