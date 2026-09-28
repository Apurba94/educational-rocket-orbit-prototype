# Instructor Manual: Educational Rocket-Orbit Computing Prototype

**Author:** Janin A Apurba  
**Audience:** Undergraduate computer science, engineering, physics, or aerospace-software learners  
**Suggested duration:** 6–10 laboratory sessions of 60–120 minutes each  
**Software:** Python 3.10+, NumPy, Matplotlib, and pytest

## 1. Course purpose and safety boundary

This prototype teaches students how scientific software turns mathematical models into tested, inspectable code. The project is intentionally limited to idealized orbital mechanics and classroom-level rocket-performance calculations. It must not be connected to a launch vehicle, engine, actuator, guidance computer, telemetry system, or any other hardware. It does not provide launch commands, targeting logic, navigation, flight control, staging, or mission-operations functionality.

The instructor should present the software as a **modeling laboratory**, not as flight software. The central learning question is: *How do assumptions, equations, numerical methods, and tests interact to produce a trustworthy computational result?* Students should be rewarded for exposing model limitations and numerical error, not for producing a superficially precise trajectory.

> **Responsible-use rule:** The outputs are educational estimates. They are not engineering approvals, operational trajectories, safety evidence, or instructions for building or controlling a rocket.

## 2. Learning outcomes

By the end of the sequence, a student should be able to explain the difference between a physical model and an implementation; convert a mathematical equation into a validated function; use SI units consistently; propagate a two-dimensional state with a numerical integrator; test conservation properties; interpret numerical error; and communicate assumptions clearly.

Students also practice software-engineering habits that generalize beyond aerospace: small pure functions, explicit units, input validation, immutable result records, regression tests, reproducible plots, and command-line execution. The project deliberately separates deterministic calculators from the time-stepping simulator so that each layer can be tested independently.

## 3. Repository map

> The files listed below are part of the restricted source code and are not published here. Email **japurba97@gmail.com** for details.

| Path | Teaching purpose |
|---|---|
| `rocket_orbit/constants.py` | Centralizes SI reference constants and prevents magic numbers from spreading through the code. |
| `rocket_orbit/calculators.py` | Implements circular-orbit, vis-viva, escape-velocity, Hohmann-transfer, rocket-equation, and state-vector diagnostics. |
| `rocket_orbit/simulation.py` | Implements planar two-body propagation and records energy and angular-momentum diagnostics. |
| `rocket_orbit/plotting.py` | Produces trajectory and invariant-drift figures using Matplotlib. |
| `rocket_orbit/cli.py` | Runs a safe end-to-end demonstration from the command line. |
| `examples/run_demo.py` | Provides a short public-API example for student modification. |
| `tests/` | Contains calculator, integration, validation, and invariant tests. |
| `ARCHITECTURE.md` | Records scope, assumptions, and non-goals. |

## 4. Setup and first run

From the project directory, create or activate a virtual environment and install the package with its development dependencies:

> **Code restricted:** the source code for this project is not published. Email **japurba97@gmail.com** for details and access.

Run the regression suite:

> **Code restricted:** the source code for this project is not published. Email **japurba97@gmail.com** for details and access.

Run the classroom demonstration and create a plot:

> **Code restricted:** the source code for this project is not published. Email **japurba97@gmail.com** for details and access.

The successful baseline should report a circular speed near 7,668.56 m/s and a circular period near 1.543 hours for a 400 km altitude example using the Earth reference constant in the project. These numbers are checks for the example configuration, not universal mission values.

## 5. Model assumptions

The baseline orbital model treats the central body as a point mass with gravitational parameter \(\mu\), and the spacecraft as a point mass that does not affect the central body. Motion is planar, the central body does not rotate, and no atmosphere, lift, finite-duration burn, third-body gravity, oblateness, heating, or vehicle structural model is included. The optional linear drag coefficient is a deliberately artificial perturbation for demonstrating energy loss; it is not an atmospheric density model.

The core acceleration is

\[
\mathbf a = -\mu \frac{\mathbf r}{\|\mathbf r\|^3}.
\]

The specific mechanical energy and scalar specific angular momentum used as diagnostics are

\[
\epsilon = \frac{1}{2}\|\mathbf v\|^2 - \frac{\mu}{r},
\qquad
h = x v_y - y v_x.
\]

In the ideal conservative two-body problem, these quantities should remain constant. In a numerical implementation, they exhibit finite-step error. That error is a learning signal and should be measured rather than hidden.

NASA’s educational trajectory material describes Hohmann transfers as energy-changing transfers between orbital paths and emphasizes that transfer timing and additional maneuvers matter in real missions [1]. NASA’s ideal rocket-equation material derives the logarithmic dependence of ideal velocity change on the initial-to-final mass ratio [2]. NASA’s specific-impulse material defines specific impulse in relation to equivalent exhaust velocity and standard gravity [3]. The prototype uses the ideal equations only as transparent classroom examples.

## 6. Calculator catalogue

| Function | Meaning | Principal inputs | Output |
|---|---|---|---|
| `circular_orbit_velocity` | Speed for a circular two-body orbit | radius, \(\mu\) | m/s |
| `orbital_period` | Period for a circular orbit | radius, \(\mu\) | s |
| `escape_velocity` | Ideal local escape speed | radius, \(\mu\) | m/s |
| `vis_viva_velocity` | Speed at a radius on an orbit with semi-major axis \(a\) | radius, \(a\), \(\mu\) | m/s |
| `hohmann_transfer` | Two ideal impulsive burns between circular orbits | initial radius, final radius, \(\mu\) | `HohmannTransfer` |
| `delta_v_from_rocket_equation` | Ideal \(\Delta v\) | initial mass, final mass, specific impulse | m/s |
| `propellant_fraction_from_delta_v` | Ideal propellant fraction for a dry mass | \(\Delta v\), dry mass, specific impulse | fraction |
| `orbital_elements_from_state` | Energy, angular momentum, eccentricity, and apsides | Cartesian state, \(\mu\) | `OrbitalElements` |

A representative call is:

> **Code restricted:** the source code for this project is not published. Email **japurba97@gmail.com** for details and access.

The ideal rocket equation implemented in the prototype is

\[
\Delta v = I_{sp} g_0 \ln\left(\frac{m_0}{m_f}\right).
\]

The instructor should explicitly ask students which real effects are absent before allowing them to interpret a calculated propellant fraction. A high numerical precision does not compensate for a simplified physical model.

## 7. Suggested teaching sequence

### Session 1: From requirements to model

Begin with a whiteboard discussion of the safety boundary and the difference between an educational simulation and operational flight software. Ask students to identify which requirements belong in a classroom model and which would require certified engineering processes. Read `ARCHITECTURE.md`, then have each student write three assumptions and three non-goals in their own words.

**Exercise:** Add a new paragraph to the architecture document explaining why an ideal two-body model cannot predict a launch vehicle’s atmospheric ascent.

### Session 2: Pure functions and units

Introduce `constants.py` and the deterministic functions in `calculators.py`. Students should trace one function from input validation to output, annotate every variable with units, and derive the circular-orbit equation from centripetal acceleration.

**Exercise:** Implement a `radius_from_altitude` helper in a student branch. Add tests for zero altitude and a negative altitude policy. Discuss why a negative altitude may be mathematically valid for an abstract body but inappropriate for a surface-based classroom example.

### Session 3: Transfers and the ideal rocket equation

Use `hohmann_transfer` to compare a low circular orbit and a higher circular orbit. Students should inspect both burns and the transfer time, then reverse the initial and final radii. The goal is not to memorize a mission recipe; it is to observe how energy and velocity change at different radii.

**Exercise:** Build a small table over several final radii and plot total ideal \(\Delta v\). Require students to state that the result ignores atmosphere, inclination changes, finite burns, and vehicle constraints.

### Session 4: Numerical integration

Introduce `simulate_two_body`. Explain the state \((\mathbf r, \mathbf v)\), the acceleration function, the time step, and the velocity-Verlet update. Students should predict what happens when the time step is made larger, then test the prediction with invariant-drift plots.

**Exercise:** Change the time step by factors of 2, 5, and 10. Record maximum relative energy drift and final radius error. Students should describe the observed trend without claiming that one experiment proves a general convergence theorem.

### Session 5: Diagnostics and testing

Read the tests before modifying the implementation. Ask students to classify each test as a limiting-case test, algebraic round-trip test, input-validation test, or invariant test. Discuss why tests of energy and angular momentum complement ordinary example-based tests.

**Exercise:** Add a test for a non-circular elliptical state and verify that the extracted eccentricity is between zero and one. Add a test that a nonzero linear drag coefficient decreases specific energy over a chosen interval.

### Session 6: Reproducibility and communication

Students run the CLI, inspect `outputs/orbit.png`, and write a short scientific note containing the configuration, assumptions, result, and numerical diagnostics. The note must distinguish observation from interpretation and must not use operational language such as “guidance solution” or “launch command.”

**Exercise:** Reproduce the plot on another machine or environment. Students should record Python version, package versions, time step, duration, and output path.

## 8. Assessment rubric

| Criterion | Excellent | Developing |
|---|---|---|
| Mathematical translation | Correct equations, units, and edge-case reasoning | Formula is present but units or assumptions are unclear |
| Software design | Small validated functions with clear interfaces | Logic is duplicated or validation is incomplete |
| Numerical reasoning | Measures step-size sensitivity and invariants | Reports a trajectory without error analysis |
| Testing | Adds meaningful limiting-case and regression tests | Tests only the nominal example |
| Scientific communication | Clearly separates model, result, uncertainty, and limitation | Presents calculated values as if they were operational facts |
| Responsible use | Maintains the project’s non-operational boundary | Introduces control, targeting, or hardware interfaces |

## 9. Extension ideas that remain safe

Safe extensions include a dimensionless-units version of the two-body problem, an adaptive-step comparison, a fourth-order Runge–Kutta implementation for numerical-method comparison, a Monte Carlo study of initial-condition perturbations, a parameter-sweep plot of ideal transfer cost, or a browser dashboard that only visualizes prerecorded simulation results. Students may also compare linear drag with a clearly labeled toy density law, provided the result is not described as a validated atmosphere model.

Extensions that should be rejected for this course include hardware interfaces, engine-valve commands, actuator commands, real-time guidance, launch-window targeting, vehicle-specific flight control, or code intended to operate an actual propulsion or navigation system. Such topics require specialist supervision, formal verification, regulated test infrastructure, and safety review beyond this teaching prototype.

## 10. Instructor troubleshooting

If imports fail while running an example directly, install the project in editable mode or set the project root on `PYTHONPATH`. If plots do not render on a headless machine, set `MPLBACKEND=Agg`. If invariant drift is unexpectedly large, reduce the time step and confirm that the initial velocity corresponds to the same radius and gravitational parameter used in the calculator. If a test fails after a student modification, compare the changed assumption with the test’s stated invariant rather than weakening the test immediately.

## References

[1]: https://science.nasa.gov/learn/basics-of-space-flight/chapter4-1/ "NASA Science, Chapter 4: Trajectories"

[2]: https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/ideal-rocket-equation/ "NASA Glenn Research Center, Ideal Rocket Equation"

[3]: https://www.grc.nasa.gov/www/k-12/airplane/specimp.html "NASA Glenn Research Center, Specific Impulse"
