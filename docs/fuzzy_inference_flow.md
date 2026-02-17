# Fuzzy Inference Flow – Neuro-Fuzzy SOP Supervisor

## 1. Purpose of the Inference Flow

This document describes the **fuzzy inference pipeline** used to compute the **State of Power (SOP) limit** based on battery state and driving conditions.

The objective of the inference flow is to:
- Translate continuous vehicle and battery measurements into **interpretable linguistic assessments**
- Apply **human-expert reasoning** through a structured rule base
- Produce a **smooth, bounded, and explainable SOP command** suitable for real-time BMS supervision

This fuzzy layer acts as a **soft supervisory controller**, complementing deterministic safety mechanisms implemented elsewhere in the Battery Management System.

---

## 2. Input Signal Acquisition

At each control cycle, the fuzzy system receives the following normalized inputs:

| Variable | Description | Range |
|--------|------------|-------|
| State of Charge (SoC) | Battery charge level | 0–100 % |
| Battery Temperature | Average cell temperature | °C (normalized) |
| Driving Style Index | Driver aggressiveness metric | 0–1 |

These inputs are assumed to be pre-filtered and validated by upstream estimation modules.

---

## 3. Fuzzification Stage

Each crisp input is mapped into **linguistic variables** using predefined membership functions.

### Linguistic Mapping

| Variable | Linguistic Sets |
|--------|----------------|
| SoC | Low, Normal, High |
| Temperature | Cold, Ideal, Hot |
| Driving Style | Smooth, Normal, Aggressive |

Membership functions are designed to:
- Overlap smoothly to avoid discontinuities
- Reflect empirical battery behavior and expert intuition
- Enable gradual transitions between operating regimes

The output of this stage is a set of **membership degrees** in the range [0, 1] for each linguistic label.

---

## 4. Rule Evaluation (Expert Reasoning Layer)

The fuzzy rule base represents **human-expert reasoning** commonly applied by BMS engineers when balancing performance and battery protection.

Each rule follows the canonical fuzzy control structure:

```text
IF (SoC is X) AND (Temperature is Y) AND (Driving Style is Z)
THEN (SOP Limit is W)
```

### Inference Method
- **Mamdani inference**
- Logical AND implemented using the **minimum (min)** operator

The firing strength of each rule is computed as the minimum membership degree of its antecedents.

---

## 5. Output Aggregation

All activated rules contribute to the final SOP decision.

- Consequent membership functions are **clipped** according to their firing strength
- Outputs corresponding to the same SOP label are **aggregated using the maximum (max)** operator

This step produces a **combined fuzzy output distribution** representing the system’s overall power recommendation.

---

## 6. Defuzzification

The aggregated fuzzy output is converted into a single crisp SOP value using:

- **Centroid (Center of Gravity) defuzzification**

This method is selected because it:
- Produces smooth and continuous outputs
- Preserves proportional influence of partially activated rules
- Is widely accepted in industrial fuzzy control applications

The resulting scalar SOP value is subsequently mapped to the linguistic categories:
- Limited
- Nominal
- Full

for interpretability and reporting purposes.

---

## 7. Control Interpretation and Execution

The defuzzified SOP value is interpreted as a **soft power availability limit**, which may be used to:
- Scale torque commands
- Limit regenerative braking current
- Constrain inverter power requests

Final enforcement remains subject to **hard safety constraints** such as overcurrent, overvoltage, and fault handling logic.

---

## 8. Design Rationale and Benefits

The fuzzy inference flow provides:

- **Explainability:** Each decision can be traced back to human-readable rules
- **Robustness:** Gradual responses instead of threshold-based switching
- **Adaptability:** Compatibility with ANFIS-based tuning or learning
- **Battery-friendly behavior:** Reduced stress under marginal conditions

This structure enables safe power management while preserving drivability and system longevity.
