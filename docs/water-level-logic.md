# Water Level Control Logic

The tank uses four float-level sensors (LS1–LS4) positioned at increasing heights. Each sensor sends a digital ON signal to the PLC input when water reaches its position. The PLC responds by deactivating one pump at a time, tapering the fill rate as the tank approaches capacity.

---

## LS1 — Minimum Level `I0.2`

**Condition:** Water is at or below the minimum threshold. Tank is low.

**Action:** All four pumps turn ON together at maximum combined flow rate.

```
Fill level:    ░░░░░░░░░░░░░░░░░░░░  (0–25%)
Active pumps:  P1 ✅  P2 ✅  P3 ✅  P4 ✅
Drain valve:   CLOSED
```

---

## LS2 — Low-Mid Level `I0.3`

**Condition:** Water has risen to the second sensor.

**Action:** Pump 4 shuts off. Pumps 1, 2, and 3 continue.

```
Fill level:    ████░░░░░░░░░░░░░░░░  (~25–50%)
Active pumps:  P1 ✅  P2 ✅  P3 ✅  P4 ❌
Drain valve:   CLOSED
```

---

## LS3 — Mid-High Level `I0.4`

**Condition:** Water has risen to the third sensor.

**Action:** Pump 3 shuts off. Pumps 1 and 2 continue at reduced flow.

```
Fill level:    ████████████░░░░░░░░  (~50–75%)
Active pumps:  P1 ✅  P2 ✅  P3 ❌  P4 ❌
Drain valve:   CLOSED
```

---

## LS4 — Maximum Level `I0.5`

**Condition:** Tank is full.

**Action:** Pumps 1 and 2 shut off. Drain valve opens.

```
Fill level:    ████████████████████  (100%)
Active pumps:  P1 ❌  P2 ❌  P3 ❌  P4 ❌
Drain valve:   OPEN ✅
```

**Why open the drain valve at LS4?**
- Relieves overpressure caused by pump inertia
- Prevents spillage if water overshoots LS4 momentarily
- Protects the tank and piping from stress

---

## Complete State Table

| Sensor | Input | P1 | P2 | P3 | P4 | Drain Valve |
|:------:|:-----:|:--:|:--:|:--:|:--:|:-----------:|
| Below LS1 | — | ❌ | ❌ | ❌ | ❌ | CLOSED |
| LS1 | I0.2 | ✅ | ✅ | ✅ | ✅ | CLOSED |
| LS2 | I0.3 | ✅ | ✅ | ✅ | ❌ | CLOSED |
| LS3 | I0.4 | ✅ | ✅ | ❌ | ❌ | CLOSED |
| LS4 | I0.5 | ❌ | ❌ | ❌ | ❌ | **OPEN** |

---

## Design Rationale — Fail-Safe Wiring

Each pump network uses a **Normally Closed (NC) contact** on its shutoff sensor:

- While the NC contact is intact (sensor not yet triggered) → pump keeps running
- When water reaches the sensor → NC contact opens → breaks the self-latch → pump stops
- **Fail-safe:** if a sensor wire breaks or disconnects, the NC contact opens and the pump stops — it won't run uncontrolled

| Pump | Shutoff sensor | Contact type | Address |
|------|---------------|:------------:|---------|
| P4 | LS2 | NC | I0.3 |
| P3 | LS3 | NC | I0.4 |
| P1 | LS4 | NC | I0.5 |
| P2 | LS4 | NC | I0.5 |
