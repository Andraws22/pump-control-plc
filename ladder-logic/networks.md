# LAD Networks — Main [OB1]

**Block:** Main [OB1] — "Main Program Sweep (Cycle)"  
**Language:** LAD (Ladder Diagram) · **Version:** 0.1  
**TIA Portal project:** `andrew / PLC_1 [CPU 1214C DC/DC/DC]`

Screenshots from TIA Portal:
- Networks 1–4 → [`assets/lad_networks_1-4.jpg`](../assets/lad_networks_1-4.jpg)
- Networks 5–8 → [`assets/lad_networks_5-8.jpg`](../assets/lad_networks_5-8.jpg)

---

## Network 1 — Auto Mode Latch

**Purpose:** Activates automatic mode and sets the system enable flag `M0.3`. All pump networks require this bit to be ON before they can run.

```
  %I1.4     %I0.1     %Q0.5     %M0.3     %Q1.0
  "AUTO" ──┤ ├─── "STOP" ──┤/├─── "I START" ──┤ ├──┬──( )── "Tag_1"
                                                     └──( )── "I Auto"
  %M0.3
  "Tag_1" ──┤ ├──┘   ← self-latch
```

| Contact | Type | Address | Tag | Note |
|---------|:----:|---------|-----|------|
| AUTO | NO | I1.4 | Auto mode selector | Rising edge starts the latch |
| STOP | NC | I0.1 | Stop button | Breaks latch when pressed |
| I START | NO | Q0.5 | Start latch bit | Must be ON (from Network 2) |
| Tag_1 | NO | M0.3 | Enable flag | Self-latch — holds AUTO active |

| Coil | Address | Function |
|------|---------|----------|
| `Tag_1` | M0.3 | System enable flag — used in all pump networks |
| `I Auto` | Q1.0 | Auto mode indicator LED |

---

## Network 2 — Start Latch

**Purpose:** Latches the START signal when PB1 is pressed. Remains ON until STOP is pressed.

```
  %I0.0     %I0.1     %Q0.5
  "start" ──┤ ├─── "STOP" ──┤/├──( )── "I START"

  %Q0.5
  "I START" ──┤ ├──┘   ← self-latch
```

| Contact | Type | Address | Tag |
|---------|:----:|---------|-----|
| start | NO | I0.0 | Start pushbutton |
| STOP | NC | I0.1 | Stop pushbutton |
| I START | NO | Q0.5 | Self-latch |

| Coil | Address | Function |
|------|---------|----------|
| `I START` | Q0.5 | Latched start signal + start indicator LED |

---

## Network 3 — Stop Indicator

**Purpose:** Lights the STOP indicator LED when the system is not running.

```
  %I0.1     %Q0.5     %Q1.1
  "STOP" ──┤/├─── "I START" ──┤/├──( )── "I STOP"
```

| Contact | Type | Address | Tag |
|---------|:----:|---------|-----|
| STOP | NC | I0.1 | Stop pushbutton |
| I START | NC | Q0.5 | Start latch — NC so indicator is ON when system is OFF |

| Coil | Address | Function |
|------|---------|----------|
| `I STOP` | Q1.1 | Stop indicator LED |

---

## Network 4 — Pump 1

**Purpose:** Controls Pump 1. Starts when LS1 triggers, runs until LS4 is reached.

```
  %I0.2     %I0.5     %M0.3     %Q1.6     %Q8.5
  "LS1" ──┤ ├─── "LS4" ──┤/├─── "Tag_1" ──┤ ├──┬──( )── "p1"
                                                  └──( )── "IP1"
  %Q1.6
  "p1" ──┤ ├──┘   ← self-latch
```

| Contact | Type | Address | Tag | Note |
|---------|:----:|---------|-----|------|
| LS1 | NO | I0.2 | Level Sensor 1 | Starts the latch |
| LS4 | **NC** | I0.5 | Level Sensor 4 | Opens when full → breaks latch → pump stops |
| Tag_1 | NO | M0.3 | System enable flag | System must be in Auto mode |
| p1 | NO | Q1.6 | Pump 1 coil | Self-latch |

| Coil | Address | Function |
|------|---------|----------|
| `p1` | Q1.6 | Pump 1 coil |
| `IP1` | Q8.5 | Pump 1 panel indicator |

---

## Network 5 — Pump 2

**Purpose:** Controls Pump 2. Identical to Pump 1 — starts at LS1, stops at LS4.

```
  %I0.2     %I0.5     %M0.3     %Q1.7     %Q8.4
  "LS1" ──┤ ├─── "LS4" ──┤/├─── "Tag_1" ──┤ ├──┬──( )── "P2"
                                                  └──( )── "IP2"
  %Q1.7
  "P2" ──┤ ├──┘
```

| Shutoff contact | Type | Address | Stops at |
|----------------|:----:|---------|----------|
| LS4 | NC | I0.5 | Maximum level — same as Pump 1 |

| Coil | Address |
|------|---------|
| `P2` | Q1.7 |
| `IP2` | Q8.4 |

---

## Network 6 — Pump 3

**Purpose:** Controls Pump 3. Starts at LS1, shuts off one level earlier — at LS3.

```
  %I0.2     %I0.4     %M0.3     %Q1.2     %Q8.0
  "LS1" ──┤ ├─── "LS3" ──┤/├─── "Tag_1" ──┤ ├──┬──( )── "P3"
                                                  └──( )── "IP3"
  %Q1.2
  "P3" ──┤ ├──┘
```

| Shutoff contact | Type | Address | Stops at |
|----------------|:----:|---------|----------|
| LS3 | NC | I0.4 | Mid-high level |

| Coil | Address |
|------|---------|
| `P3` | Q1.2 |
| `IP3` | Q8.0 |

---

## Network 7 — Pump 4

**Purpose:** Controls Pump 4. First pump to stop — shuts off at LS2.

```
  %I0.2     %I0.3     %M0.3     %Q1.3     %Q8.3
  "LS1" ──┤ ├─── "LS2" ──┤/├─── "Tag_1" ──┤ ├──┬──( )── "P4"
                                                  └──( )── "IP4"
  %Q1.3
  "P4" ──┤ ├──┘
```

| Shutoff contact | Type | Address | Stops at |
|----------------|:----:|---------|----------|
| LS2 | NC | I0.3 | Low-mid level — first pump to shut off |

| Coil | Address |
|------|---------|
| `P4` | Q1.3 |
| `IP4` | Q8.3 |

---

## Network 8 — Drain Valve

**Purpose:** Opens the drain valve when the tank reaches maximum level.

```
  %I0.5     %I0.2     %M0.3     %Q1.5            %Q8.1
  "LS4" ──┤ ├─── "LS1" ──┤/├─── "Tag_1" ──┤ ├──┬──( )── "drain_valve"
                                                  └──( )── "I drain_valve"
  %Q1.5
  "drain_valve" ──┤ ├──┘   ← self-latch
```

| Contact | Type | Address | Tag | Note |
|---------|:----:|---------|-----|------|
| LS4 | NO | I0.5 | Level Sensor 4 | Triggers at max level |
| LS1 | NC | I0.2 | Level Sensor 1 | NC: valve stays open while LS1 is still active |
| Tag_1 | NO | M0.3 | System enable | Must be in Auto mode |
| drain_valve | NO | Q1.5 | Valve coil | Self-latch |

| Coil | Address | Function |
|------|---------|----------|
| `drain_valve` | Q1.5 | Drain valve coil |
| `I drain_valve` | Q8.1 | Drain valve panel indicator |

---

## Summary

| Network | Output tag | Coil address | Starts when | Stops when |
|---------|-----------|:------------:|-------------|------------|
| 4 | `p1` | Q1.6 | LS1 (I0.2) ON | LS4 (I0.5) ON |
| 5 | `P2` | Q1.7 | LS1 (I0.2) ON | LS4 (I0.5) ON |
| 6 | `P3` | Q1.2 | LS1 (I0.2) ON | LS3 (I0.4) ON |
| 7 | `P4` | Q1.3 | LS1 (I0.2) ON | LS2 (I0.3) ON |
| 8 | `drain_valve` | Q1.5 | LS4 (I0.5) ON | — |
