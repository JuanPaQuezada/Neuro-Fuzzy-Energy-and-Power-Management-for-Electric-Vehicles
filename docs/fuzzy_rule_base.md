# Fuzzy Rule Base – Expert Knowledge Layer

## 1. Purpose of the Rule Base

This rule base encodes expert-level heuristic knowledge used by the neuro-fuzzy controller to determine an appropriate **State of Power (SOP) limit** under varying battery and driving conditions.

Rather than enforcing hard constraints, the rules provide **interpretable and smooth supervisory decisions**, complementing deterministic protection logic implemented elsewhere in the BMS.

---

## 2. Variables Involved

### Inputs
- **State of Charge (SoC):** {Low, Normal, High}
- **Battery Temperature:** {Cold, Ideal, Hot}
- **Driving Style:** {Smooth, Normal, Aggressive}

### Output
- **State of Power (SOP) Limit:** {Limited, Normal / Nominal, Full}

---

## 3. Rule Design Principles

The fuzzy rules are designed according to the following principles:

- **Thermal dominance:** Battery temperature has priority over all other variables.
- **Progressive derating:** Power limitations are applied gradually, not abruptly.
- **Behavior-aware control:** Aggressive driving modulates power availability but does not dominate safety.
- **Interpretability:** All rules reflect intuitive expert reasoning.

---

## 4. Dominant Safety-Oriented Rules

These rules reflect conditions where battery protection takes precedence over performance.

```text
IF Temperature is Hot
THEN SOP is Limited
```
```text
IF Driving Style is Aggressive AND Temperature is Hot
THEN SOP is Limited
```
```text
IF SoC is Low AND Temperature is Ideal
THEN SOP is Limited

```
## 5. Performance-Oriented Rules

These rules enable higher power availability when operating conditions are favorable.

```text
    IF SoC is Normal AND Temperature is Ideal AND Driving Style is Smooth
    THEN SOP is Full
```
```text
    IF SoC is Normal AND Temperature is Ideal AND Driving Style is Normal
    THEN SOP is Normal
```

## 6. Modulation Rules

These rules adjust power availability based on driving behavior without triggering hard limitations.

```text
    IF Driving Style is Aggressive AND Temperature is Ideal AND SoC is Normal
    THEN SOP is Normal
```
```text
    IF Temperature is Cold AND Driving Style is Aggressive
    THEN SOP is Normal
```

## 7. Regenerative and High SoC Considerations

```text
    IF SoC is High AND Temperature is Ideal
    THEN SOP is Normal
```
This rule prevents excessive regenerative current and thermal buildup near full charge without unnecessarily restricting vehicle performance.

## 8. Scope and limitations

*Extreme fault conditions are handled outside the fuzzy inference system.

*The rule base does not override hard BMS safety constraints.

*The system focuses on power availability and degradation mitigation rather than fault detection.
