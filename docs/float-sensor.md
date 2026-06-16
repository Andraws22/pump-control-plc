# Float Level Sensor

## Overview

A float level sensor detects the liquid level inside a tank by using a buoyant float — typically a ball or cylinder — that rises and falls with the liquid surface. When the float reaches a set height, it triggers an electrical switch, sending a digital signal to the control system.

Float sensors are widely used in industrial and water systems because they are mechanically simple, reliable, and far cheaper than ultrasonic or capacitive alternatives.

---

## How It Works

### 1 — Float Mechanism

The float is buoyant and always rests on the liquid surface. As the level rises or falls, the float moves with it. It is attached to a rod, stem, or magnet assembly that transmits its movement to a switching element.

### 2 — Switching Mechanisms

| Type | Mechanism | Characteristics |
|------|-----------|----------------|
| **Magnetic** (modern) | Float contains a magnet; reed switch or Hall-effect sensor nearby closes when magnet passes | No physical contact → less wear, longer life |
| **Mechanical** (traditional) | Float physically presses a lever or contact at set height | Simple but subject to mechanical wear |

### 3 — Output Signal

When the liquid reaches the float's set height, the switch triggers and sends a digital ON/OFF signal to the PLC input. In this project each sensor behaves as a simple digital input — OFF below its position, ON when water reaches or exceeds it.

---

## Electrical Behaviour

| Type | Float at low position | Float rises to set point |
|------|-----------------------|--------------------------|
| **Normally Open (NO)** | Circuit OPEN — signal OFF | Circuit CLOSES — signal ON |
| **Normally Closed (NC)** | Circuit CLOSED — signal ON | Circuit OPENS — signal OFF |

> In this project the level sensors are wired as **Normally Open (NO)** inputs. The PLC ladder logic then uses **NC contacts** on the shutoff sensors to create a fail-safe design — see [`water-level-logic.md`](water-level-logic.md).

---

## Component Parts

| Part | Function |
|------|----------|
| **Float** | Buoyant element that rides the liquid surface |
| **Stem / Rod** | Guides the float's vertical travel |
| **Switching mechanism** | Reed switch or mechanical contact activated by float movement |
| **Casing** | Protects internal components from the liquid and from corrosion |

---

## Applications

| Field | Use |
|-------|-----|
| Water tanks | Monitor level and control pumps ← *this project* |
| Sumps and wells | Start/stop pumps automatically |
| Fuel tanks | Monitor and manage fuel levels |
| Industrial systems | Control fluid in boilers, cooling circuits, and process machinery |

---

## Advantages & Disadvantages

| ✅ Advantages | ❌ Disadvantages |
|--------------|-----------------|
| Simple design — easy to install and maintain | Mechanical parts wear over time, especially in high-cycle applications |
| Inexpensive compared to ultrasonic or capacitive sensors | Point-level detection only — cannot measure continuously |
| Available in many materials (stainless steel, PP, PVC) | Float can jam in fluids with debris or high viscosity |
| Long lifespan with basic maintenance | Needs recalibration if tank geometry changes |

---

## Sensor Placement in This Project

Four float sensors are mounted at increasing heights inside POT 1 on the BEDO trainer:

```
  ─── LS4 (I0.5) ───  ← Maximum level — drain valve trigger
  ─── LS3 (I0.4) ───  ← Mid-high level — P3 shutoff
  ─── LS2 (I0.3) ───  ← Low-mid level  — P4 shutoff
  ─── LS1 (I0.2) ───  ← Minimum level  — all pumps start
```

| Sensor | PLC Address | Position | Trigger action |
|--------|:-----------:|----------|----------------|
| LS1 | I0.2 | Bottom | Starts P1, P2, P3, P4 |
| LS2 | I0.3 | Low-mid | Stops P4 |
| LS3 | I0.4 | Mid-high | Stops P3 |
| LS4 | I0.5 | Top | Stops P1 + P2, opens drain valve |
