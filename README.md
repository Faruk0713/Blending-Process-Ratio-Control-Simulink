# Blending Process Ratio Control System Simulation

This repository contains the MATLAB/Simulink implementation and comparative performance analysis of process control strategies for a chemical blending system. 

The primary objective is to design, model, and simulate a **Ratio Control System** to maintain a fixed composition between two inlet process streams ($F_A$ and $F_B$) under external flow disturbances, and rigorously compare its disturbance-rejection capabilities against an **Independent Flow Control** configuration.

---

## 📋 Problem Statement

> *"Design and simulate a ratio control system for a blending process, where two inlet streams must maintain a fixed composition despite flow disturbances, and compare its performance with independent flow control loops."*

---

## 🛠️ Control System Architectures

### 1. Ratio Control Loop (Feedback + Feedforward)
* **Strategy:** Tracks a "wild flow" stream ($F_A$) and manipulates the "controlled flow" stream ($F_B$) to enforce a set target ratio ($R = F_B / F_A$).
* **Implementation:** Employs a parallel loop structure integrating a Proportional-Integral (PI) feedback controller with a feedforward controller block ($G_{cf}$) for dynamic disturbance rejection.
* **Control Law:** $\text{Controlled Flow Setpoint} = \text{Ratio} \times \text{Wild Flow}$.

### 2. Independent Flow Control Loop
* **Strategy:** Stream $F_A$ and Stream $F_B$ are regulated independently using separate, decoupled closed-loop controllers.
* **Implementation:** The setpoint for Stream $F_A$ is statically defined, while the setpoint for Stream $F_B$ is continuously calculated using the scalar algebraic relation $F_B = F_A \times R$.

---

## 📉 Mathematical Modeling & Transfer Functions

The specialized linear time-invariant (LTI) transfer functions derived for the feedback, process, and disturbance pathways are defined as follows:

* **Controller ($G_c$):** Proportional-Integral (PI) configuration
  $$g_c(s) = 7.5 + \frac{10}{s} = \frac{7.5s + 10}{s}$$
* **Process Plant ($G_p$):** Lead-lag phase behavior
  $$g_p(s) = \frac{0.563s + 2.3\times10^{-5}}{s + 9.09\times10^{-5}}$$
* **Feedforward/Valve Actuator ($G_{CF}$):** First-order lag
  $$g_{cf}(s) = \frac{-0.002195}{s + 8.6\times10^{-4}}$$
* **Disturbance Pathway ($G_d$):** First-order lag
  $$g_d(s) = \frac{0.001237}{s + 3.6\times10^{-4}}$$
* **Measurement Sensor ($G_{mf}$):** Ideal unity gain ($G_{mf}(s) = 1$).

### Final Closed-Loop System Equation
The complete output response ($y$) mapping the combined influences of the command reference ($r$) and the line disturbance ($d$) is governed by the following unified equation:

$$y = \left[ \frac{g_d + g_p \cdot g_{cf} \cdot g_{mf}}{1 + g_p \cdot g_c \cdot g_{mf}} \right] d + \left[ \frac{g_p \cdot g_c}{1 + g_p \cdot g_c \cdot g_{mf}} \right] r$$

---

## 📊 Simulation Parameters & Steady-State Outputs

### Ratio Controller Performance
* **Target Blend Ratio:** 5.000
* **Obtained Ratio:** 4.999
* **Wild Flow Rate ($F_A$):** $66.175 \text{ L/s}$
* **Controlled Flow Rate ($F_B$):** $330.795 \text{ L/s}$ (Desired: $330.9 \text{ L/s}$)
* **Steady-State Control Signal ($u$):** 0.321

### Independent Controller Performance
* **Target Blend Ratio:** 5.00
* **Obtained Ratio:** 4.912
* **Stream 1 Output ($F_A$):** $490.50 \text{ L/s}$ (Control Signal $u_1 = 0.7050$)
* **Stream 2 Output ($F_B$):** $99.29 \text{ L/s}$ (Control Signal $u_2 = 0.0115$)

**Conclusion:** The dedicated **Ratio Control System** provides significantly tighter composition bounds, lower tracking offset, and superior disturbance rejection compared to the Independent Flow layout.

---

## ⚠️ Design Challenges & Constraints

* **Actuator Saturation:** Disturbance rejection capabilities are heavily constrained by physical valve limits, restricting the control signal strictly between the $[0, 1]$ saturation boundaries.
* **Plant Nonlinearity:** Accurate system linearization is essential; design variations or unmodeled flow dynamics directly degrade PI tuning stability.
* **Limited Operational Window:** Large, rapid fluctuations in the wild flow stream run into operational capacity limits of the secondary control valve.
