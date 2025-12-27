# Phase 2: Autonomy

Add a flight controller and GPS to enable autonomous flight capabilities.

## Overview

| Aspect | Details |
|--------|---------|
| Goal | Enable GPS waypoint navigation and autonomous flight modes |
| Prerequisites | Phase 1 complete, stable RC flight achieved |
| Duration | 2-3 days for hardware + software setup |
| Difficulty | Intermediate |

---

## Hardware Requirements

### Required Components

| Component | Specification | Purpose |
|-----------|---------------|---------|
| Flight Controller | Matek F405-Wing | ArduPilot, sensor fusion, MAVLink |
| GPS Module | BN-880 (GPS + Compass) | Position, navigation, heading |
| UART Cable | JST-GH or Dupont | FC to Pi communication |

### Optional Components

| Component | Specification | Purpose |
|-----------|---------------|---------|
| Airspeed Sensor | Pitot tube + sensor | Better throttle control |
| Telemetry Radio | 433MHz / 915MHz | Ground station link |
| Current Sensor | Hall effect | Battery monitoring |

---

## System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   Ground Station                        │
│    Mission Planner / QGroundControl                     │
│    (Laptop with telemetry receiver)                     │
└────────────────────────┬────────────────────────────────┘
                         │ 433/915 MHz Telemetry
                         │ (Optional)
                         ▼
┌─────────────────────────────────────────────────────────┐
│                  Matek F405-Wing                        │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │  ArduPilot  │  │   Sensors    │  │   Outputs     │  │
│  │  Firmware   │  │  - IMU       │  │  - Servo 1    │  │
│  │             │  │  - Baro      │  │  - Servo 2    │  │
│  │  Modes:     │  │  - GPS       │  │  - ESC/Motor  │  │
│  │  - Manual   │  │  - Compass   │  │               │  │
│  │  - FBWA     │  │  - Airspeed  │  │  UART2 ────────────► Pi Zero 2 W
│  │  - Auto     │  │              │  │  (MAVLink)    │  │
│  │  - RTL      │  │              │  │               │  │
│  │  - Loiter   │  │              │  │               │  │
│  └─────────────┘  └──────────────┘  └───────────────┘  │
└─────────────────────────────────────────────────────────┘
         ▲                    ▲
         │                    │
    ELRS Receiver        BN-880 GPS
    (Manual Control)     (I2C + Serial)
```

---

## Flight Controller: Matek F405-Wing

### Why Matek F405-Wing?

| Feature | Benefit |
|---------|---------|
| ArduPilot native support | Full autonomous features |
| Dual UART available | FC + Pi communication |
| Built-in BEC (5V) | Powers GPS, receiver |
| Current sensor input | Battery monitoring |
| Designed for fixed wing | Appropriate I/O mapping |

### Pinout (Key Connections)

| Pin | Function | Connect To |
|-----|----------|------------|
| SBUS | RC Input | ELRS Receiver |
| S1 | Servo 1 | Left Elevon |
| S2 | Servo 2 | Right Elevon |
| S3 | Motor | ESC Signal |
| TX2/RX2 | UART2 | Pi Zero 2 W (MAVLink) |
| 5V/GND | Power | GPS Module |
| SCL/SDA | I2C | GPS Compass |

---

## GPS Module: BN-880

### Features

| Spec | Value |
|------|-------|
| GPS Chip | Ublox M8N |
| Compass | HMC5883L |
| Update Rate | 10Hz |
| Accuracy | 2.5m CEP |
| Interface | Serial (GPS) + I2C (Compass) |

### Mounting Requirements

- Mount GPS on top of aircraft
- Flat, level surface
- Arrow pointing forward
- Away from ESC/motor wires (EMI)
- Minimum 10cm from power wires

---

## Software Stack

### ArduPilot (Flight Controller)

```
ArduPlane Firmware
├── Stabilization (IMU + PID loops)
├── Navigation (GPS waypoints)
├── Flight Modes
│   ├── MANUAL - Direct RC control
│   ├── FBWA - Fly-by-wire (stabilized)
│   ├── AUTO - Follow waypoint mission
│   ├── RTL - Return to launch
│   ├── LOITER - Circle at position
│   └── CRUISE - Heading + altitude hold
└── MAVLink Protocol (Communication)
```

### Mission Planner (Ground Station)

- Windows application
- Upload waypoint missions
- Real-time telemetry display
- Parameter tuning
- Log analysis

---

## Wiring Diagram

```
                    ┌─────────────────────┐
                    │    BN-880 GPS       │
                    │  ┌───┐  ┌───────┐   │
                    │  │GPS│  │Compass│   │
                    │  └─┬─┘  └───┬───┘   │
                    │    │        │       │
                    └────┼────────┼───────┘
                         │        │
                    TX/RX│   SCL/SDA
                         │        │
┌────────────────────────┼────────┼────────────────────┐
│                  MATEK F405-WING                     │
│                        │        │                    │
│   GPS ◄────────────────┘        └────────► I2C      │
│                                                      │
│   ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐   │
│   │  SBUS  │  │   S1   │  │   S2   │  │   S3   │   │
│   └───┬────┘  └───┬────┘  └───┬────┘  └───┬────┘   │
│       │           │           │           │         │
└───────┼───────────┼───────────┼───────────┼─────────┘
        │           │           │           │
        ▼           ▼           ▼           ▼
   ELRS RX     Left Servo  Right Servo   ESC
```

---

## Setup Procedure

### Step 1: Flash ArduPilot Firmware

1. Download ArduPlane firmware for Matek F405-Wing
2. Connect FC via USB
3. Use Mission Planner → Install Firmware
4. Select "MatekF405-Wing" target
5. Flash and wait for reboot

### Step 2: Connect Hardware

1. Connect GPS to UART1 (or dedicated GPS port)
2. Connect compass to I2C (SCL/SDA)
3. Connect ELRS receiver to SBUS input
4. Connect servos to S1, S2
5. Connect ESC to S3

### Step 3: Configure ArduPilot

#### Basic Parameters

```
SERVO1_FUNCTION = 77  (Elevon Left)
SERVO2_FUNCTION = 78  (Elevon Right)
SERVO3_FUNCTION = 70  (Throttle)

RC_MAP_ROLL = 1
RC_MAP_PITCH = 2
RC_MAP_THROTTLE = 3

ARMING_CHECK = 1      (All checks enabled)
```

#### Flight Modes (via RC switch)

```
FLTMODE1 = 0   (MANUAL)
FLTMODE2 = 5   (FBWA)
FLTMODE3 = 10  (AUTO)
FLTMODE4 = 11  (RTL)
```

### Step 4: Calibrate Sensors

1. **Accelerometer Calibration**
   - Mission Planner → Setup → Accel Calibration
   - Follow 6-position calibration

2. **Compass Calibration**
   - Mission Planner → Setup → Compass
   - Rotate aircraft in all axes

3. **Radio Calibration**
   - Mission Planner → Setup → Radio Calibration
   - Move all sticks to extremes

### Step 5: Test Flight Modes

1. **MANUAL Mode**
   - Verify all controls work correctly
   - Fly as normal RC

2. **FBWA Mode (Fly-by-Wire)**
   - Aircraft self-levels when sticks centered
   - Test roll/pitch limits

3. **RTL Mode**
   - Set RTL_ALTITUDE parameter
   - Test return-to-home behavior

4. **AUTO Mode**
   - Upload simple waypoint mission
   - Test autonomous waypoint following

---

## Autonomous Capabilities

### Available Flight Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| MANUAL | Direct RC control | Takeoff, landing, emergencies |
| FBWA | Stabilized, no altitude hold | Learning to fly |
| CRUISE | Heading + altitude hold | Long range cruising |
| AUTO | Waypoint following | Survey, patrol missions |
| RTL | Return to launch | Failsafe, recall |
| LOITER | Circle at GPS point | Observation |

### Mission Types

| Mission | Description |
|---------|-------------|
| Survey Grid | Fly grid pattern over area |
| Waypoint Path | Visit points in sequence |
| Loiter Circle | Orbit around point of interest |
| Return Home | Automatic landing at home point |

---

## Failsafe Configuration

### Radio Failsafe (Signal Lost)

```
FS_SHORT_ACTN = 0  (Continue mission)
FS_LONG_ACTN = 1   (RTL after 20 seconds)
FS_LONG_TIMEOUT = 20
```

### Battery Failsafe

```
BATT_FS_LOW_ACT = 1   (RTL on low battery)
BATT_LOW_VOLT = 10.5  (3.5V per cell)
```

### Geofence

```
FENCE_ENABLE = 1
FENCE_TYPE = 7        (Altitude + Circle + Polygon)
FENCE_ACTION = 1      (RTL on breach)
FENCE_ALT_MAX = 120   (Max 120m altitude)
FENCE_RADIUS = 500    (Max 500m from home)
```

---

## Integration with Phase 3 (Pi Communication)

The FC exposes MAVLink over UART2 for Pi Zero 2 W communication.

### UART2 Configuration

```
SERIAL2_PROTOCOL = 2   (MAVLink2)
SERIAL2_BAUD = 57600   (or 115200)
```

### Physical Connection

```
FC UART2 TX  →  Pi GPIO 15 (RX)
FC UART2 RX  →  Pi GPIO 14 (TX)
FC GND       →  Pi GND
```

The Pi can then:
- Read telemetry (position, altitude, battery)
- Send commands (change mode, goto waypoint)
- Trigger actions based on AI detection

---

## Next Steps

Once autonomous flight is reliable:
1. Tune PID parameters for smooth flight
2. Test all failsafe scenarios
3. Proceed to [Phase 3: Camera & AI](phase3-camera-ai.md)
