# Wiring Guide — BEDO Advanced PLC Trainer

This document describes how to connect the PLC inputs and outputs to the BEDO trainer patch panel using banana plug cables.

---

## Panel Layout Overview

The BEDO trainer has three terminal blocks at the bottom:

| Block | Location | Contains |
|-------|----------|----------|
| **Analog Inputs** | Bottom-left | POT1, POT2, POT3 (not used in this project) |
| **Digital Inputs** | Bottom-center | PB1–PB11, S1–S6, GND |
| **Digital Outputs** | Bottom-right | D1–D16, GND |

The PLC's physical I/O ports are wired internally to these terminals on the BEDO trainer. You connect signals between them using **banana plug patch cables**.

---

## Digital Input Connections

Connect each pushbutton/selector on the right-hand panel to its PLC input address using a cable from the `Digital Inputs` terminal block:

| Signal | Panel button | Terminal | PLC Address |
|--------|-------------|----------|:-----------:|
| Start | PB1 | PB1 | I0.0 |
| Stop | PB2 | PB2 | I0.1 |
| Level Sensor 1 | PB3 | PB3 | I0.2 |
| Level Sensor 2 | PB4 | PB4 | I0.3 |
| Level Sensor 3 | PB5 | PB5 | I0.4 |
| Level Sensor 4 | PB6 | PB6 | I0.5 |
| Auto mode | S2 | S2 | I1.4 |

> On the actual tank, LS1–LS4 are float sensors wired to PB3–PB6 respectively. During testing on the trainer, pressing PB3–PB6 manually simulates water reaching each level.

---

## Digital Output Connections

Connect each PLC output to the corresponding pump indicator or actuator terminal:

### Pump coils

| Signal | PLC Address | Terminal |
|--------|:-----------:|----------|
| Pump 1 | Q1.6 | D-output matching Q1.6 |
| Pump 2 | Q1.7 | D-output matching Q1.7 |
| Pump 3 | Q1.2 | D-output matching Q1.2 |
| Pump 4 | Q1.3 | D-output matching Q1.3 |
| Drain valve | Q1.5 | D-output matching Q1.5 |

### Panel indicators (BEDO graphic overlay LEDs)

| Signal | PLC Address | Lights up |
|--------|:-----------:|-----------|
| IP1 | Q8.5 | Pump 1 LED on trainer graphic |
| IP2 | Q8.4 | Pump 2 LED on trainer graphic |
| IP3 | Q8.0 | Pump 3 LED on trainer graphic |
| IP4 | Q8.3 | Pump 4 LED on trainer graphic |
| I drain_valve | Q8.1 | Drain valve LED on trainer graphic |
| I START | Q0.5 | Start LED (right panel) |
| I STOP | Q1.1 | Stop LED (right panel) |
| I Auto | Q1.0 | Auto mode LED (right panel) |

---

## Test Procedure

Once wired, follow this sequence to verify correct operation:

```
Step 1 → Press S2 (Auto mode)       — I Auto LED lights up
Step 2 → Press PB1 (Start)          — I START LED lights up
Step 3 → Press PB3 (simulate LS1)   — All 4 pump LEDs (IP1–IP4) light up
Step 4 → Press PB4 (simulate LS2)   — IP4 turns OFF (P4 stops)
Step 5 → Press PB5 (simulate LS3)   — IP3 turns OFF (P3 stops)
Step 6 → Press PB6 (simulate LS4)   — IP1 + IP2 turn OFF, drain valve LED turns ON
Step 7 → Press PB2 (Stop)           — Everything de-energizes
```

If any step does not behave as expected, check:
- Patch cable connections on the terminal block
- Tag addresses in TIA Portal match this document
- The PLC is in RUN mode (green RUN LED on the CPU)
