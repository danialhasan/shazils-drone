# Phase 2: Autonomy

Swap in an ArduPilot-capable flight controller and GPS so the foam glider can fly missions, return-to-home, and talk to a future compute payload.

## Overview

| Aspect | Details |
|--------|---------|
| Goal | GPS-guided flight via Matek F405-Wing + BN-880 |
| Airframe | Foam glider from Phase 1 |
| Dependencies | Phase 1 trimmed and logged |
| Duration | 2–3 days |
| Difficulty | Intermediate |

---

## Hardware Requirements

| Component | Purpose | Notes |
|-----------|---------|-------|
| Matek F405-Wing (ArduPilot) | Flight controller | Native ArduPlane support |
| BN-880 GPS/compass | Nav + heading | Mount away from ESC wires |
| Pitot / airspeed (optional) | Better throttle control | Optional |
| Telemetry radio (optional) | Ground station link | 915 MHz or 433 MHz |
| Vibration tape / velcro | Mount FC on foam | Easy removal |

---

## System Architecture

```
Ground Station ──(optional telemetry)──► Matek F405-Wing
                               │
ELRS Receiver ──► SBUS          │          BN-880 GPS/Compass
Servos / ESC  ──► S1/S2/S3     │          UART1 + I2C
UART2 (unused) ────────────────┘ reserved for Phase 3 SBC
```

---

## Mounting & Wiring

| FC Pin | Connects To | Notes |
|--------|-------------|-------|
| SBUS | ELRS receiver | Shield if long run |
| S1 | Left elevon | `SERVO1_FUNCTION = 77` |
| S2 | Right elevon | `SERVO2_FUNCTION = 78` |
| S3 | ESC throttle | `SERVO3_FUNCTION = 70` |
| TX1/RX1 + 5 V | BN-880 GPS | Serial |
| SCL/SDA + 5 V | BN-880 compass | I2C |
| TX2/RX2 | Reserved | Route to canopy for future SBC |

- Mount FC at CG, arrow forward.
- GPS on top hatch or mast, 10 cm away from ESC/motor wires.

---

## Firmware & Setup

1. Flash ArduPlane for Matek F405-Wing using Mission Planner.
2. Configure basics:
   ```
   SERVO1_FUNCTION = 77
   SERVO2_FUNCTION = 78
   SERVO3_FUNCTION = 70

   RC_MAP_ROLL = 1
   RC_MAP_PITCH = 2
   RC_MAP_THROTTLE = 3
   RC_MAP_YAW = 4
   ```
3. Assign flight modes (MANUAL, FBWA, AUTO, RTL) to a switch.
4. Run accel, compass, and radio calibrations.
5. Bench-test control surfaces with prop removed.

---

## Failsafes & Geofence

```
BATT_FS_LOW_ACT = 1      # RTL on low voltage
BATT_LOW_VOLT    = 10.5  # 3.5 V/cell
FS_SHORT_ACTN    = 0     # Continue mission on brief loss
FS_LONG_ACTN     = 1     # RTL after timeout
FS_LONG_TIMEOUT  = 20

FENCE_ENABLE = 1
FENCE_TYPE   = 7
FENCE_ACTION = 1
FENCE_ALT_MAX = 120
FENCE_RADIUS  = 500
```

---

## Flight Testing

1. **Manual/FBWA shakedown:** confirm FC inline doesn’t change trims.
2. **RTL test:** set RTL altitude ≥60 m, trigger switch, verify homing.
3. **AUTO mission:** upload a short waypoint route in clear airspace.
4. Check logs for vibration/current issues and adjust mounting if needed.

---

## Prep for Phase 3

- Label TX2/RX2 leads and leave accessible for the future SBC harness.
- Reserve canopy space/top hatch for the compute payload.
- Capture CG + trim data before adding extra weight.
