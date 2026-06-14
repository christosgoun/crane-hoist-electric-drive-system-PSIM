# Design & Simulation of an Overhead Crane Hoist Electric Drive System

## Project Overview
This project presents the complete engineering design, mathematical sizing, and dynamic simulation of the hoisting motion for an industrial overhead crane ($3206\text{ kg}$ payload). The drive system is designed to achieve precise speed profiling under heavy-duty cycles, handling bidirectional energy flow (motoring during elevation, regenerating during lowering) using an induction motor driven by a frequency converter (VFD).

The system was simulated in **PSIM** using a per-unit ($pu$) equivalent circuit of a delta-connected squirrel-cage motor to validate the thermal capabilities, current metrics, and braking resistor requirements.

---

## Technical Specifications & Sizing Constraints
The drive system was engineered based on the following strict operational constraints:

| Parameter | Symbol | Design Value |
| :--- | :--- | :--- |
| **Payload Mass** | $m$ | $3206\text{ kg}$ |
| **Drum Diameter** | $D$ | $0.4\text{ m}$ |
| **Gearbox Reduction Ratio** | $i$ | $57$ |
| **Mechanical Efficiency** | $\eta$ | $0.75$ |
| **Max Linear Hoisting Speed** | $v_{max}$ | $0.3\text{ m/s}$ |
| **Max Linear Acceleration** | $a_{max}$ | $0.5\text{ m/s}^2$ |
| **Maximum Lift Height** | $h_{max}$ | $6.0\text{ m}$ |
| **Dwell Time (Rest Period)** | $t_p$ | $15\text{ s}$ |
| **DC Bus Voltage** | $V_{dc}$ | $700\text{ V}$ |

---

## System Architecture & Component Selection

### 1. Electric Motor Sizing
*   **Peak Power Demand:** Calculated at $17.39\text{ kW}$ during the acceleration phase of the hoisting motion.
*   **Steady-State Torque (Elevating):** $147.14\text{ Nm}$ (referred to the motor shaft).
*   **Selected Motor:** **ABB M2QA 200 L6A** (Cast Iron, 6 poles, Class Eff2).
    *   *Nominal Power:* $18.5\text{ kW}$
    *   *Nominal Torque:* $180\text{ Nm}$
    *   *Nominal Speed:* $980\text{ rpm}$
    *   *Rotor Inertia ($J_{rotor}$):* $0.342\text{ kg}\cdot\text{m}^2$

### 2. Variable Frequency Drive (VFD) Selection
*   **Peak Current Draw:** $40.8\text{ A}$ during accelerated elevation.
*   **Selected Drive:** **ABB ACS800-01-0040-3** (Wall-mounted, Heavy-Duty rating).
    *   *Continuous Current Rating:* $72\text{ A}$ (Over-dimensioned to safely tolerate heavy transient loads).
    *   *Short-term Current Limit:* $86\text{ A}$.

### 3. Dynamic Braking Resistor Sizing
*   **Regenerated Energy per Cycle:** $134.93\text{ kJ}$ returned to the DC link during lowering.
*   **Average Braking Power:** $6.55\text{ kW}$.
*   **Resistor Constraint:** $R_d \le 74.81\ \Omega$.
*   **Selected Resistor:** **ABB SAFUR200F500** ($R = 2.7\ \Omega$, Continuous power capacity $13.5\text{ kW}$).

---

## Dynamic Torque Profile & Duty Cycle
The motor operates in all 4 quadrants of the torque-speed plane. Mechanical brakes are engaged during the dwell periods ($t_p$) to prevent motor overheating at stall ($s=1$).

The analytical torque profile ($T_m$) across the $71.2\text{ s}$ total cycle time is governed by:

$$
T(t) = \begin{cases} 
203.38\text{ Nm}, & 0 < t \le 0.6\text{ s} \quad \text{(Accelerated Elevating)} \\ 
147.14\text{ Nm}, & 0.6 < t \le 20.0\text{ s} \quad \text{(Steady Elevating)} \\ 
90.9\text{ Nm}, & 20.0 < t \le 20.6\text{ s} \quad \text{(Decelerated Elevating)} \\
0\text{ Nm}, & 20.6 < t \le 35.6\text{ s} \quad \text{(Dwell/Mechanical Brake)} \\
29.8\text{ Nm}, & 35.6 < t \le 36.2\text{ s} \quad \text{(Accelerated Lowering - Regenerative)} \\
82.76\text{ Nm}, & 36.2 < t \le 55.6\text{ s} \quad \text{(Steady Lowering - Regenerative)} \\
135.72\text{ Nm}, & 55.6 < t \le 56.2\text{ s} \quad \text{(Decelerated Lowering)} \\
0\text{ Nm}, & 56.2 < t \le 71.2\text{ s} \quad \text{(Dwell/Mechanical Brake)}
\end{cases}
$$

### Thermal Verification
*   **Calculated Root-Mean-Square Torque ($T_{rms}$):** $91.35\text{ Nm}$.
*   Since $T_{rms} = 91.35\text{ Nm} \ll T_{N} = 180\text{ Nm}$, the motor is thermally safe against long-term breakdown and excessive copper losses.

---

## PSIM Simulation & Model Validation

### Motor Parameters Conversion
The induction motor was modeled using a delta-connected schematic in PSIM. Per-unit values given by the specification were converted to physical SI units using the base impedance $Z_b = 8.65\ \Omega$:

*   $R_s = 0.0865\ \Omega$, $L_s = 1.927\text{ mH}$
*   $R_r = 0.519\ \Omega$, $L_r = 1.377\text{ mH}$
*   $L_m = 0.1652\text{ H}$
*   **Referred Load Inertia:** $J'_{load} = \frac{J_{load}}{i^2} = 0.039\text{ kg}\cdot\text{m}^2$

### Slip Compensation & Tuning
Initial simulation runs revealed a discrepancy in steady-state speed ($784\text{ rpm}$ vs target $817\text{ rpm}$). This was identified as a modeling artifact: the PSIM template forced a high rotor resistance ($R_r = 0.06\ pu$) resulting in a $6\%$ nominal slip compared to the $1.63\%$ slip of the real ABB motor.

A **trial-and-error slip compensation** was implemented by adjusting the VFD frequency references to match the target linear velocity ($0.3\text{ m/s}$):
*   **Hoisting Phase:** Tuned to $43.4\text{ Hz}$ ($m_a = 0.99$) $\rightarrow$ Reached **$818\text{ rpm}$**.
*   **Lowering Phase:** Tuned to $39.45\text{ Hz}$ ($m_a = 0.90$) $\rightarrow$ Symmetric profile achieved.

### Key Simulation Results
*   **Electromagnetic Torque $T_{em}^{rms}$:** $92\text{ Nm}$ (Validating the analytical calculation of $91.35\text{ Nm}$).
*   **Line Current $I_{d1}^{rms}$:** $15.63\text{ A}$ (Well within the VFD’s $72\text{ A}$ thermal limit).
*   **DC Link Average Current:** $8.19\text{ A}$.

---

## Repository Structure
