# Requirements: Chasing Zero — Digital PID Control Loop Tutorial

## Purpose

An interactive, self-contained tutorial that teaches digital PID (Proportional-Integral-Derivative) control loops through explanation and hands-on simulation.

## Constraints

- **Single file**: All HTML, CSS, and JavaScript in one `index.html` file for simple GitHub Pages hosting.
- **Vanilla JavaScript**: No frameworks, no build tools, no dependencies.
- **Self-hosted**: Must work when served statically (no backend, no external API calls at runtime).

## Functional Requirements

### Content

- [ ] Explain what a control loop is and why PID matters.
- [ ] Describe each term (P, I, D) and its effect on the system response.
- [ ] Define key concepts: setpoint, error, process variable, output, gain, windup.
- [ ] Show the discrete-time PID algorithm (the code a microcontroller or software loop would run).

### Interactive Simulation

- [ ] Visualize a simulated physical system (e.g. a mass with velocity, a temperature process, or similar).
- [ ] Allow the user to set the **setpoint**.
- [ ] Allow the user to tune **Kp**, **Ki**, and **Kd** gains with sliders or inputs.
- [ ] Plot the **process variable** and **setpoint** over time on a live chart.
- [ ] Show the current error and PID output values numerically.
- [ ] Allow the user to start, pause, and reset the simulation.
- [ ] In sections that include the integral term, display the integral's current contribution as a percentage of heater output alongside the graph, updating each cycle and clearing on reset.

### Code Transparency

- [ ] Show the PID algorithm source code inline in the page.
- [ ] Highlight which term (P, I, or D) is active/dominant at any moment, if feasible.

## Tutorial Structure & User Experience

The tutorial is a single scrolling page. The user reads top to bottom, with each section building on the last. Each section introduces one new PID term, explains the problem it solves, and provides a simulation to experiment with before the user scrolls on.

---

### Section 0 — Introduction

A brief framing section. Explains the scenario: a 3D printer hotend that needs to reach and hold a target temperature. Introduces the concept of a control loop without any math — just the idea that a program is repeatedly reading the temperature and deciding how much power to send to the heater.

No simulation in this section.

---

### Section 1 — Proportional Control

**Narrative:** Introduce the simplest possible approach — apply power proportional to how far away you are from the target. Explain why this feels intuitive but has a fundamental problem: it tends to overshoot and oscillate, or settle at a steady-state error below the target. Provide recommended Kp settings that clearly demonstrate overshoot so the user can see the problem for themselves.

**Simulation — P only:**

- **Inputs exposed to the user:**
  - Target temperature slider (0–300 °C)
  - Initial temperature slider (0–300 °C)
  - Kp slider
- **Run controls:** Start/Stop (real-time) and Step-by-Step mode
- **Output:** A live graph of temperature over time, with the target shown as a horizontal reference line

---

### Section 2 — Integral Control

**Narrative:** Explain the steady-state error problem from Section 1 and why it happens — proportional control alone backs off as error shrinks, eventually balancing before reaching the setpoint. Introduce the integral term as accumulated error over time, which provides an extra push to close that gap. Show recommended settings that demonstrate classic integral windup overshoot so the user understands the new problem the I term can introduce.

**Simulation — PI:**

- **Inputs exposed to the user:**
  - Target temperature slider (0–300 °C)
  - Initial temperature slider (0–300 °C)
  - Kp slider (carried over from Section 1)
  - Ki slider (newly introduced)
- **Run controls:** Start/Stop (real-time) and Step-by-Step mode
- **Output:** Live graph of temperature over time with target reference line, plus an **integral term display** to the right of the graph showing the I term's current contribution as a percentage of heater output — this makes the mechanism of windup visible to students

---

### Section 3 — Derivative Control

**Narrative:** Explain the overshoot/oscillation problems that can arise with PI control. Introduce the derivative term as a brake — it responds to the *rate of change* of error, damping the response before it overshoots. Explain that D is the most sensitive term and can amplify noise if tuned too aggressively. Provide recommended settings that show a well-tuned PID response as a satisfying payoff after the problems demonstrated in the previous two sections.

**Simulation — Full PID:**

- **Inputs exposed to the user:**
  - Target temperature slider (0–300 °C)
  - Initial temperature slider (0–300 °C)
  - Kp slider (carried over)
  - Ki slider (carried over)
  - Kd slider (newly introduced)
- **Run controls:** Start/Stop (real-time) and Step-by-Step mode
- **Output:** Live graph of temperature over time with target reference line

---

### Simulation Details (all sections)

**Run modes:**
- *Real-time:* The simulation runs continuously, updating the graph at a fixed rate. The user can stop it and reset.
- *Step-by-step:* Each press of a button advances the simulation by one control loop iteration, updating the graph. Useful for understanding exactly what the algorithm is computing at each tick.

**Hidden / fixed simulation parameters:**
The following are set to reasonable values modelling an average FDM printer hotend and are not exposed to the user:
- Hotend thermal mass / heating coefficient (how fast the heater raises temperature per unit power)
- Ambient cooling coefficient (passive heat loss to the environment)
- Heater output range (0–100% duty cycle, clamped)
- Control loop timestep

---

## Code Architecture Requirements

### Classes

**`PIDSimulation`** — one class, one instance per tutorial section
- `setKp(value)`, `setKi(value)`, `setKd(value)` — update gain parameters
- `setTargetTemp(value)`, `setInitialTemp(value)` — configure the run
- `step()` — advance the simulation by exactly one control loop cycle and update the graph
- `run()` — run the simulation continuously in real-time until stopped or the cycle limit is reached
- `reset()` — return the simulation to its initial state and clear the graph
- Owns an instance of `TempGraph`
- All simulations share a single constant `MAX_CYCLES` (pick a value appropriate for seeing a hotend reach and stabilise at temperature, e.g. 500 cycles)
- Hidden physical constants (heating coefficient, cooling coefficient, timestep, heater clamp range) are defined as constants inside this class and never surfaced to the user

**`TempGraph`** — one class, multiple instances (one per simulation section)
- `addPoint(temp)` — append a new temperature reading to the chart
- `clear()` — remove all plotted points

**`IntegralDisplay`** — one class, instances on Section 2 and Section 3 only
- Displays the integral term's live contribution as a percentage of heater output, shown to the right of the graph
- `update(percentage)` — set the displayed value (called by `PIDSimulation.step()` each cycle)
- `clear()` — reset the display (called on reset)
- `PIDSimulation` holds an optional reference to an `IntegralDisplay`; if set, `step()` calls `update()` and `reset()` calls `clear()` automatically

**`SliderInput`** — one class, multiple instances (one per tunable parameter per section)
- Encapsulates a vertical slider paired with a numerical readout below it
- Changing the slider updates the number; editing the number updates the slider
- Constructed with a label, min, max, default value, and a callback invoked on change

**`SectionN`** (one per tutorial section: `Section1`, `Section2`, `Section3`) — thin wiring classes that link the above together
- Creates the `PIDSimulation`, `TempGraph`, and the appropriate `SliderInput` instances for that section
- Wires slider callbacks to the simulation setters
- Handles the Start/Stop and Step button events for that section
- Keeps each section's logic self-contained and easy to read in isolation

---

### File Organisation (`index.html`)

The single file is divided into collapsible `#region` blocks so any editor that supports region folding (VS Code, etc.) can navigate it cleanly:

```
#region CSS
  ... all styles ...
#endregion

#region HTML
  ... all markup ...
#endregion

#region JavaScript — TempGraph
  ...
#endregion

#region JavaScript — IntegralDisplay
  ...
#endregion

#region JavaScript — SliderInput
  ...
#endregion

#region JavaScript — PIDSimulation
  ...
#endregion

#region JavaScript — Section1 (P only)
  ...
#endregion

#region JavaScript — Section2 (PI)
  ...
#endregion

#region JavaScript — Section3 (PID)
  ...
#endregion
```

CSS and HTML regions appear in `<style>` and the document `<body>` respectively. JavaScript regions all live inside a single `<script>` tag at the bottom of the file.

---

## Non-Functional Requirements

- The page must load and run fully offline after the initial download.
- The simulation must run smoothly in modern desktop browsers (Chrome, Firefox, Safari, Edge).
- The tutorial should be approachable for someone with basic programming knowledge but no control systems background.
- No minification required — code should remain readable as part of the learning experience.

## Out of Scope

- Multi-axis or cascaded PID control.
- Real hardware integration.
- User accounts, persistence, or analytics.
- Mobile-optimised layout (nice to have, not required).
