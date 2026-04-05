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

## Technical notes

- Single self-contained `index.html` file — no build step, no dependencies
- Vanilla JavaScript only
- Hosted via GitHub Pages
