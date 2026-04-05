# Chasing Zero

An interactive tutorial on digital PID control loops, built for a classroom setting.

**[Open the tutorial →](https://ediamondscience.github.io/chasing-zero/)**

---

## What is this?

PID controllers are everywhere — 3D printers, thermostats, cruise control, industrial equipment. They solve a deceptively simple problem: given a target value and a current measurement, how much should you adjust the output to close the gap?

This tutorial walks through the answer one step at a time, using a simulated 3D printer hotend as the example system. Each section introduces one term of the PID algorithm, explains the problem it solves, and gives you a live simulation to experiment with before moving on.

## What's covered

| Section | Topic | What you'll see |
|---------|-------|-----------------|
| 1 | Proportional control | Steady-state error — why P alone never quite gets there |
| 2 | Integral control | Integral windup — why I can overshoot dramatically |
| 3 | Derivative control | Damping — how D smooths the response out |

## Who is it for?

Written for a secondary school audience (roughly grades 8–10) with basic programming knowledge but no background in control systems.

## Tuning guide

Not sure where to start or why your graph looks wrong? Match what you're seeing to the symptoms below.

**The temperature barely moves / does nothing**
> Kp is too low. Raise it. Proportional gain is what gets the heater moving in the first place — without it, the other two terms can't do much either.

**The temperature climbs but stops short of the target and stays there**
> Classic steady-state error. This is proportional control doing exactly what it's designed to do — and hitting its limit. Add a small Ki to close the gap.

**The temperature shoots past the target and oscillates up and down**
> Either Kp is too high, or Ki has caused windup. Try lowering Kp first. If the I term display was in the red during the run, lower Ki too. Once oscillation is under control, add a small Kd to dampen the swings.

**The temperature overshoots once, then settles — but slowly**
> This is actually fine behaviour. If you want it to settle faster without more overshoot, raise Kd slightly. If it starts oscillating again, you've gone too far.

**The I term display turns red immediately**
> Ki is too large. The integral is accumulating faster than the heater can act on it. Bring Ki down until the display stays below 100% during a normal run.

**The temperature oscillates rapidly with very short peaks and valleys**
> Kd is too high. The derivative term is overreacting to small changes in error and fighting itself. Reduce Kd, or set it to zero and re-tune Kp and Ki before adding it back.

**General approach (if starting from scratch)**
1. Set Ki and Kd to zero.
2. Raise Kp until the temperature reaches the setpoint — accept some overshoot for now.
3. Add a small Ki to eliminate any remaining gap below the target.
4. Add a small Kd to smooth out overshoot. Increase gradually.
5. Fine-tune all three until the response looks clean.

---

## Technical notes

- Single self-contained `index.html` file — no build step, no dependencies
- Vanilla JavaScript only
- Hosted via GitHub Pages
