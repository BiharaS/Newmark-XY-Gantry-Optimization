# Precision Optimization and Control Programming of Dual-Axis Newmark XY Gantry System

An advanced motion control and programming project focused on developing custom Galil control scripts from scratch to optimize a dual-axis Newmark XY gantry system used for precision height verification and automated testing.

---

## Project Background & Objectives

At Flintec Koggala, precision and reliability are maintained using high-quality linear guide solutions like Newmark and Festo gantry systems. The system utilizes a Newmark NSC G Series motion system controller, a stepper motor-based CS-500-2 linear guide, two CS-500-1-X2 linear guides, and a DSE-ODS compact line laser triangulation distance sensor.

The primary objectives of this optimization project were:
* **Parallelism Height Checking:** To automate height verification at two specific points per unit on specialized cell fixtures, ensuring strict tolerance limits and high repeatability across repeated cycles without positional drift.
* **Readiness for Upcoming Projects:** To prove that the exact same gantry setup can reliably perform automated tasks for upcoming projects, specifically executing coordinated positioning to capture resistance measurements from a specific load sensor set attached to a mounting frame.

---

## Hardware Architecture

* **Motion Controller:** Newmark NSC G Series motion system controller.
* **Linear Guide Modules:** Stepper motor-based CS-500-2 axis and dual CS-500-1-X2 axes configured as the XY coordinate system (Axes A and B).
* **Sensor Integration:** DSE-ODS compact line laser triangulation distance sensor for high-precision height and distance measurements.

---

## My Role & Programming Scope

My core contribution to this project was **motion control programming entirely from scratch**, developing and refining the Galil `.dm` control scripts used to drive, synchronize, and error-correct the gantry axes.

---

## Programming & Error Compensation Methodology

After observing that extended operational cycles led to minor positional deviations from expected coordinates under relative positioning, the control logic was restructured using native Galil commands and encoder feedback integration:

* **Initialization & Motor Control (`SH`, `SPA`, `SPB`, `BGAB`):** Configured motor activation (`SH`), axis speeds (`SPA=20000`, `SPB=20000`), and coordinated axis motion execution (`BGAB`).
* **Homing & Limit Triggering (`JGA`, `AMAB`):** Programmed velocity jogging (`JGA=-20000`) to home axes against sensor limits, utilizing motion completion waits (`AMAB`) to sequence operations safely.
* **Absolute Positioning & Encoder Compensation (`DP`, `DE`, `PAA`):** To eliminate cumulative positioning errors inherent to relative motion, the script transitioned to absolute positioning. Real-time Cartesian coordinates (`DP 0,0`) and encoder readings (`DE 0,0`) were monitored, and movements were executed using absolute commands (such as `PAA=51550`) verified against actual encoder feedback rather than step counts alone.

---

## Firmware & Script Architecture

Sample command structure from the finalized Galil script (`.dm`):

```text
M0; Motor OFF
SH; Motor ON
SPA=20000; Speed of axis A equals 20,000
SPB=20000; Speed of axis B equals 20,000
PRA=5000; Relative position of A equals 5,000
BGAB; Begin axis motions of axes A, B
JGA=-20000; Jog A axis -20,000 until sensor limit triggers
AMAB; Wait until motions of axes A and B finish
WT 900; Wait 900 milliseconds
DP 0,0; Cartesian coordinates 0,0
DE 0,0; Encoder readings 0,0
PAA=51550; Absolute motion of Axis A to 51,550 based on encoder validation
