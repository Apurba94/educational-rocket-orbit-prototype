# Educational Rocket-Orbit Prototype: Architecture and Safety Boundary

## Purpose

This project is a self-contained educational prototype for teaching orbital mechanics, numerical integration, software testing, and scientific visualization. It models idealized two-body motion and selected rocket-performance equations in a transparent, inspectable way.

## Explicit non-goals

The prototype does **not** implement real launch control, vehicle flight software, engine control, guidance, navigation, targeting, telemetry command, staging control, hazard analysis, or operational mission planning. It is not suitable for controlling hardware or making safety-critical decisions.

## Proposed components

> The files listed below are part of the restricted source code and are not published here. Email **japurba97@gmail.com** for details.

| Component | Responsibility | Main outputs |
|---|---|---|
| `rocket_orbit/constants.py` | SI units and central-body constants | Earth reference constants |
| `rocket_orbit/calculators.py` | Deterministic textbook equations | orbital and rocket-performance quantities |
| `rocket_orbit/simulation.py` | Educational 2D two-body numerical integration | time history of position, velocity, energy, and angular momentum |
| `rocket_orbit/plotting.py` | Reproducible scientific plots | trajectory and invariant-drift figures |
| `rocket_orbit/cli.py` | Command-line demonstrations | human-readable calculator and simulation output |
| `tests/` | Regression and invariant checks | automated correctness signals |
| `examples/` | Small runnable experiments | notebooks/scripts without operational controls |
| `instructor_manual.md` | Teaching sequence and coding guidance | lesson plans, exercises, rubrics, limitations |

## Mathematical scope

The initial version will include:

1. Circular-orbit velocity, orbital period, and escape velocity.
2. Vis-viva velocity for an elliptical orbit.
3. Hohmann-transfer delta-v between two circular orbits.
4. Tsiolkovsky ideal rocket-equation delta-v and propellant fraction.
5. A basic 2D Newtonian two-body propagator using velocity-Verlet integration.
6. Diagnostic quantities: specific orbital energy, specific angular momentum, and eccentricity.
7. Optional pedagogical perturbation toggles such as a simple linear drag term, clearly labeled as non-operational approximations.

## Numerical design

The default propagator is velocity-Verlet because it is compact, deterministic, and useful for illustrating conservation behavior in conservative systems. The simulator accepts initial Cartesian state, gravitational parameter, time step, and duration. It returns arrays and diagnostics; it does not emit actuator commands or guidance decisions.

## Units and conventions

All public calculator functions use SI units: metres, seconds, kilograms, and radians. Angles are converted at the interface only when an example explicitly requests degrees. Inputs are validated for finite values and physically meaningful positive parameters.

## Verification strategy

The test suite will verify limiting cases and invariants rather than compare against operational flight software. Examples include:

- circular orbit speed and period consistency;
- escape velocity equals `sqrt(2)` times circular velocity at the same radius;
- Hohmann transfer symmetry under reversed radii;
- rocket-equation round trips between delta-v and propellant fraction;
- bounded relative drift of specific energy and angular momentum for a small-step circular orbit.

## Responsible-use statement

The code is intended for classrooms, software-engineering practice, and conceptual research. Users must not connect it to hardware, use it to derive operational launch commands, or treat its simplified atmosphere, gravity, and vehicle models as validated engineering models.
