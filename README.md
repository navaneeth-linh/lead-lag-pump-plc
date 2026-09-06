# 🔄 Lead-Lag Pump Control System

A PLC ladder logic simulation of an industrial Lead-Lag pump control system built using **CodeSys**. The system automatically controls two pumps based on tank water levels, alternates pump leadership to balance wear, detects overload faults, and provides a blinking fault indicator using dual TON timers.

---

## 📌 Project Overview

| Field | Details |
|---|---|
| **Platform** | CodeSys (Simulation) |
| **Language** | Ladder Diagram (LD) |
| **Function** | Dual pump lead-lag control with level sensing and fault detection |
| **Pumps** | Pump 1 and Pump 2 with automatic lead alternation |
| **Timers** | TON_0 (1s), TON_1 (0.6s) — blinking fault light |
| **Safety** | Emergency stop, pump overload detection, blinking fault lamp |

---

## 🔌 I/O Table

### Inputs

| Variable | Type | Description |
|---|---|---|
| `START` | BOOL | Start button — initiates pump operation |
| `LOW_LEVEL_SENSOR` | BOOL | Detects low water level — triggers pump ON |
| `HIGH_LEVEL_SENSOR` | BOOL | Detects high water level — triggers pump OFF |
| `E_STOP` | BOOL | Emergency stop — immediately halts operation |
| `PUMP_OVERLOAD` | BOOL | Overload fault signal for either pump |
| `LEAD_PUMP` | BOOL | Selects which pump is the lead (active) pump |

### Outputs

| Variable | Type | Description |
|---|---|---|
| `PUMP_1` | BOOL | Pump 1 output |
| `PUMP_2` | BOOL | Pump 2 output |
| `GREEN_LIGHT` | BOOL | Machine running indicator |
| `FAULT_RED` | BOOL | Steady red fault lamp — Emergency stop active |
| `FAULT_YELLOW` | BOOL | Blinking yellow fault lamp — Overload detected |
| `MACHINE_OK` | BOOL | Internal flag — system healthy (no E-stop or overload) |
| `MOTOR_FAULT_1` | BOOL | Fault indicator for Motor 1 |
| `MOTOR_FAULT_2` | BOOL | Fault indicator for Motor 2 |

### Internal Flags & Timers

| Variable | Type | Setting | Description |
|---|---|---|---|
| `LOOP_CONTROL` | BOOL | — | Controls blinking loop for FAULT_YELLOW |
| `T_CONTROL_1` | BOOL | — | Activates Pump 1 when Pump 2 is faulty |
| `T_CONTROL_2` | BOOL | — | Activates Pump 2 when Pump 1 is faulty |
| `PUMP_1_STAT` | BOOL | — | Status flag for Pump 1 |
| `TON_0` | TON | T#1S | Fault blink ON duration |
| `TON_1` | TON | T#0.6S | Fault blink OFF duration |

---

## 🔄 System Sequence

```
START pressed
  → LOW_LEVEL_SENSOR triggered (water low)
      → LEAD_PUMP determines which pump activates
          → PUMP_1 or PUMP_2 turns ON
          → GREEN_LIGHT ON (MACHINE_OK active)
              → HIGH_LEVEL_SENSOR triggered (tank full)
                  → Active pump turns OFF (SET/RESET logic)
                  → System waits for next LOW_LEVEL trigger

EMERGENCY STOP pressed
  → MACHINE_OK goes FALSE
  → Both pumps stop
  → FAULT_RED lamp turns ON (steady)

PUMP_OVERLOAD detected
  → MACHINE_OK goes FALSE
  → FAULT_YELLOW starts blinking
      → TON_0 (1s ON) → LOOP_CONTROL
      → TON_1 (0.6s OFF) → FAULT_YELLOW blinks continuously
```

---

## 🧩 Ladder Logic — Rung Breakdown

| Rung | Description |
|---|---|
| 1 | START + LOW_LEVEL_SENSOR + MACHINE_OK (NC: PUMP_1 seal-in) → PUMP_1 ON when LEAD_PUMP selects Pump 1 |
| 2 | START + LOW_LEVEL_SENSOR + MACHINE_OK → PUMP_2 ON when LEAD_PUMP selects Pump 2 |
| 3 | HIGH_LEVEL_SENSOR sets LEAD_PUMP — switches lead to Pump 1 and stops Pump 1 |
| 4 | HIGH_LEVEL_SENSOR resets LEAD_PUMP — switches lead to Pump 2 and stops Pump 2 |
| 5 | E_STOP (NC) + PUMP_OVERLOAD (NC) → MACHINE_OK (system healthy flag) |
| 6 | MACHINE_OK → GREEN_LIGHT ON |
| 7 | E_STOP → FAULT_RED ON (steady red lamp) |
| 8 | PUMP_OVERLOAD + LOOP_CONTROL (NC) → TON_0 (1s) → FAULT_YELLOW blink ON |
| 9 | LOOP_CONTROL → TON_1 (0.6s) → FAULT_YELLOW blink OFF (completes blink cycle) |

---

## 🖥️ Visualization

A CodeSys HMI visualization was built with input buttons for START, LOW_LEVEL_SENSOR, HIGH_LEVEL_SENSOR, E_STOP, PUMP_OVERLOAD, and LEAD_PUMP, and output indicators for PUMP_1, PUMP_2, GREEN_LIGHT, FAULT_RED, and FAULT_YELLOW.

---

## 📁 Repository Structure

```
lead-lag-pump-plc/
├── media/
│   ├── ladder_logic.pdf
│   └── visualization_screenshot.png
├── README.md
```

---

### Ladder Logic 
   visualization(visualization_screenshot.png)

## 🛠️ Tools Used

- **CodeSys 3.5** — PLC programming and simulation
- **Ladder Diagram (LD)** — Programming language
- **Soft PLC simulation** — No physical hardware required

---

## 👤 Author

**S Navaneeth Krishna**
B.Tech — Electrical and Electronics Engineering
Muthoot Institute of Science and Technology, Kerala

[![GitHub](https://img.shields.io/badge/GitHub-navaneeth--linh-181717?style=flat&logo=github)](https://github.com/navaneeth-linh)

---

## 📜 License

This project is open source and available for educational use.
