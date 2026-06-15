# Industrial Overhead Crane Hoisting Drive System: Design, Sizing, and PSIM Simulation

This repository contains the complete engineering design, mathematical sizing, and dynamic simulation of the hoisting motion for an industrial overhead crane with a **3206 kg payload**. The drive system handles bidirectional energy flow (motoring during elevation, regenerating during lowering) using a 3-phase induction motor driven by a Variable Frequency Drive (VFD). 

The complete transient behavior, thermal characteristics, and power electronics metrics were modeled and validated using **PSIM software**.

---

## 1. Technical Specifications & Sizing Constraints

The drive system was engineered to meet the following strict operational parameters:

| Parameter | Symbol | Design Value |
| :--- | :--- | :--- |
| **Payload Mass** | $m$ | 3206 kg |
| **Drum Diameter** | $D$ | 0.4 m |
| **Gearbox Reduction Ratio** | $i$ | 57 |
| **Mechanical Efficiency** | $\eta$ | 0.75 |
| **Max Linear Hoisting Speed** | $v_{max}$ | 0.3 m/s |
| **Max Linear Acceleration** | $a_{max}$ | 0.5 m/s² |
| **Maximum Lift Height** | $h_{max}$ | 6.0 m |
| **Dwell Time (Rest Period)** | $t_p$ | 15 s |
| **DC Bus Voltage** | $V_{dc}$ | 700 V |

---

## 2. System Architecture & Component Selection

### Electric Motor Sizing
* **Peak Power Demand:** 17.39 kW during the acceleration phase of the hoisting motion.
* **Steady-State Torque (Elevating):** 147.14 Nm (referred to the motor shaft).
* **Selected Motor:** **ABB M2QA 200 L6A** (Cast Iron, 6 poles, Class Eff2).
  * *Nominal Power:* 18.5 kW
  * *Nominal Torque:* 180 Nm
  * *Nominal Speed:* 980 rpm
  * *Rotor Inertia ($J_{rotor}$):* 0.342 kg·m²

### Variable Frequency Drive (VFD) Selection
* **Peak Current Draw:** 40.8 A during accelerated elevation.
* **Selected Drive:** **ABB ACS800-01-0040-3** (Heavy-Duty rating).
  * *Continuous Current Rating:* 72 A (Over-dimensioned to safely tolerate heavy transient loads).
  * *Short-term Current Limit:* 86 A.

### Dynamic Braking Resistor Sizing
* **Regenerated Energy per Cycle:** 134.93 kJ returned to the DC link during lowering.
* **Average Braking Power:** 6.55 kW.
* **Resistor Constraint:** $R_d \le 74.81\ \Omega$.
* **Selected Resistor:** **ABB SAFUR200F500** ($R = 2.7\ \Omega$, Continuous power capacity 13.5 kW).

---

## 3. Dynamic Torque Profile & Duty Cycle

The motor operates in all 4 quadrants of the torque-speed plane. Mechanical brakes are engaged during the dwell periods ($t_p$) to prevent motor overheating at stall ($s = 1$). 

The analytical torque profile ($T_m$) across the **71.2 s total cycle time** is governed by:
