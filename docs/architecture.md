# System Architecture and Design Decisions

## 1. Architectural Overview

The proposed system is designed as a **decision-level power limiting module**, intended to operate alongside conventional Battery Management System (BMS) components.

The neuro-fuzzy controller does not directly replace low-level protection mechanisms, but rather complements them by providing **adaptive and interpretable power limitation decisions** under normal operating conditions.

---

## 2. Separation Between Safety and Intelligence Layers

In industrial BMS architectures, **critical battery conditions** are typically handled through **hard safety constraints** implemented outside adaptive control logic.

In this project, such conditions are explicitly excluded from the neuro-fuzzy inference system:

> *Critical battery conditions are handled as hard safety constraints outside the neuro-fuzzy inference system, reflecting common industrial BMS architectures.*

This design choice ensures that:

* Safety-critical decisions remain deterministic.
* The neuro-fuzzy system operates only within safe and meaningful regions.
* Learning-based behavior does not override protection logic.

---

## 3. Role of the Neuro-Fuzzy System

The neuro-fuzzy module operates as a **supervisory decision layer**, estimating a **State of Power (SOP) limit** based on battery state and driving conditions.

Its purpose is to:

* Smooth power limitation transitions.
* Adapt to non-linear operating patterns.
* Maintain interpretability through fuzzy rule structures.

---

## 4. Input Space Modeling & Engineering Rationale

The input variables are modeled using fuzzy sets designed to reflect physical battery constraints and optimize ANFIS learning convergence (using 3 sets instead of 4 to reduce parameter explosion).

### 4.1 State of Charge (SoC) Design
The SoC variable uses a mix of Trapezoidal and Triangular membership functions to model distinct operational zones:

* **Low (0-40%) - Trapezoidal:**
    * **Rationale:** Represents the **Deep Discharge Protection** zone.
    * **Design:** A trapezoidal shape saturates at 1 (max membership) between 0-20% to enforce strict power limiting, while the slope from 20-40% ensures a smooth transition to normal operation, preventing control oscillation.

* **Normal (25-85%) - Triangular:**
    * **Rationale:** Represents the **Chemical Stability** zone.
    * **Design:** Peaks at **55%**, which typically correlates with optimal internal resistance and minimal thermal stress in Li-ion chemistries. The triangular shape implies that "normality" decreases as the system moves away from this thermodynamic sweet spot.

* **High (70-100%) - Trapezoidal:**
    * **Rationale:** Represents the **Regenerative Braking Saturation** zone.
    * **Design:** The function activates early (at 70%) to allow the system to anticipate saturation. As SoC approaches 100%, the battery's ability to accept current (regenerative braking) drops to zero; the neuro-fuzzy system uses this region to derate input power limits progressively.

---

## 5. Scope and Assumptions

* The system assumes that all inputs are pre-validated by the BMS.
* Extreme fault conditions (thermal runaway, short circuits) are outside the scope.
* The model focuses on energy efficiency and degradation mitigation rather than fault detection.

---

## 6. Implications for Future Integration

By separating safety constraints from adaptive logic, the proposed architecture is compatible with:

* Hardware-in-the-loop (HIL) testing.
* Real-time embedded deployment.
* Integration with existing BMS software stacks.
* Offline simulation-based validation
* Rule surface inspection