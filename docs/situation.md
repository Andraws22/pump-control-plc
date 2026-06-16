# Problem Statement & Methodology

## Objective

Design and implement a PLC program to fill a water tank using four pumps, where the number of active pumps decreases automatically as the tank fills — ensuring a smooth, controlled inflow rather than an abrupt cutoff.

---

## System Description

A water tank (POT 1) is equipped with four float-level sensors mounted at increasing heights (LS1 at the bottom, LS4 at the top) and fed by four pumps running in parallel. The PLC monitors the sensors and shuts pumps off one at a time as the water level rises. A drain valve activates automatically at maximum capacity to protect the tank from overflow.

---

## Control Sequence

| # | Event | Sensor | PLC Input | Response |
|---|-------|--------|-----------|----------|
| 1 | Tank is low | LS1 triggers | I0.2 ON | All 4 pumps start simultaneously |
| 2 | Water rises to mid-low | LS2 triggers | I0.3 ON | Pump 4 stops — 3 pumps remain |
| 3 | Water rises to mid-high | LS3 triggers | I0.4 ON | Pump 3 stops — 2 pumps remain |
| 4 | Tank is full | LS4 triggers | I0.5 ON | Pumps 1 & 2 stop + drain valve opens |

> **Why sequential shutdown?**  
> Shutting pumps off one at a time reduces water hammer (pressure surges) and gives the system more precise control over the final fill level.

---

## Operating Modes

| Mode | Selector | How It Works |
|------|----------|--------------|
| **Manual** | S1 | Operator activates pumps directly using pushbuttons PB3–PB6 |
| **Automatic** | S2 | PLC reads level sensors and controls all pumps autonomously |

---

## Components

| Component | Qty | Role |
|-----------|:---:|------|
| Water pumps | 4 | Fill the tank at varying combined flow rates |
| Float level sensors | 4 | Detect water level at four fixed points (LS1–LS4) |
| Drain valve | 1 | Releases excess liquid when tank reaches max capacity |
| Siemens S7-1200 PLC | 1 | Executes the control logic |
| BEDO Advanced PLC Trainer | 1 | Training platform — panel indicators, patch terminals, POT 1 tank |
