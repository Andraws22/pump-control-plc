# PLC Tag Table

**Controller:** Siemens S7-1200 CPU 1214C DC/DC/DC  
**TIA Portal project:** `andrew / PLC_1`  
**Program block:** Main [OB1]

---

## Digital Inputs

| Tag | Data Type | Address | Physical Device | Function |
|-----|:---------:|---------|-----------------|----------|
| `start` | Bool | I0.0 | Start pushbutton — PB1 | Latches the system ON |
| `STOP` | Bool | I0.1 | Stop pushbutton — PB2 | Breaks all latches, stops system |
| `LS1` | Bool | I0.2 | Level Sensor 1 — PB3 | Minimum water level |
| `LS2` | Bool | I0.3 | Level Sensor 2 — PB4 | Low-mid water level |
| `LS3` | Bool | I0.4 | Level Sensor 3 — PB5 | Mid-high water level |
| `LS4` | Bool | I0.5 | Level Sensor 4 — PB6 | Maximum water level |
| `AUTO` | Bool | I1.4 | Auto mode selector — S2 | Enables automatic control |

---

## Digital Outputs

### Pump coils

| Tag | Data Type | Address | Panel Indicator | Starts at | Stops at |
|-----|:---------:|---------|:--------------:|-----------|----------|
| `p1` | Bool | Q1.6 | IP1 — Q8.5 | LS1 (I0.2) | LS4 (I0.5) |
| `P2` | Bool | Q1.7 | IP2 — Q8.4 | LS1 (I0.2) | LS4 (I0.5) |
| `P3` | Bool | Q1.2 | IP3 — Q8.0 | LS1 (I0.2) | LS3 (I0.4) |
| `P4` | Bool | Q1.3 | IP4 — Q8.3 | LS1 (I0.2) | LS2 (I0.3) |

### Drain valve

| Tag | Data Type | Address | Panel Indicator | Activates at |
|-----|:---------:|---------|:--------------:|--------------|
| `drain_valve` | Bool | Q1.5 | `I drain_valve` — Q8.1 | LS4 (I0.5) |

### Status indicator LEDs

| Tag | Data Type | Address | Indicates |
|-----|:---------:|---------|-----------|
| `I START` | Bool | Q0.5 | System is running |
| `I STOP` | Bool | Q1.1 | System is stopped |
| `I Auto` | Bool | Q1.0 | Automatic mode is active |
| `I-LS1` | Bool | Q0.6 | LS1 has been triggered |
| `I-LS2` | Bool | Q0.7 | LS2 has been triggered |
| `I-LS3` | Bool | Q0.3 | LS3 has been triggered |
| `I-LS4` | Bool | Q0.2 | LS4 has been triggered |

---

## Internal Memory Bits

| Tag | Data Type | Address | Function |
|-----|:---------:|---------|----------|
| `Tag_1` | Bool | M0.3 | **System enable flag.** Latched ON when Auto mode is activated. Used as a series NO contact in every pump and drain valve network — no pumps can run unless this bit is SET. |
