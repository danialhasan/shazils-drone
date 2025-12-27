# Wiring Diagrams

Complete wiring reference for all project phases.

---

## Phase 1: Basic RC Wiring

### Overview Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         POWER FLOW                               │
│                                                                  │
│   ┌──────────────┐                                              │
│   │   3S LiPo    │                                              │
│   │   Battery    │                                              │
│   │  11.1V       │                                              │
│   └──────┬───────┘                                              │
│          │ XT60                                                  │
│          ▼                                                       │
│   ┌──────────────┐         ┌─────────────┐                      │
│   │    30A ESC   │────────►│   Motor     │                      │
│   │              │ 3-wire  │   A2212     │                      │
│   │  ┌────────┐  │ bullet  │   1400kv    │                      │
│   │  │  BEC   │  │         └─────────────┘                      │
│   │  │  5V/3A │  │                                              │
│   │  └───┬────┘  │                                              │
│   └──────┼───────┘                                              │
│          │                                                       │
│          │ 5V Power                                              │
│          ▼                                                       │
│   ┌──────────────┐                                              │
│   │    ELRS      │                                              │
│   │   Receiver   │                                              │
│   │              │                                              │
│   │  CH1  CH2 CH3│                                              │
│   └───┬────┬───┬─┘                                              │
│       │    │   │                                                 │
│       │    │   └──────────────────┐                             │
│       │    │                      │                              │
│       ▼    ▼                      ▼                              │
│   ┌──────┐ ┌──────┐        ┌──────────┐                         │
│   │Servo │ │Servo │        │   ESC    │                         │
│   │ Left │ │Right │        │ Throttle │                         │
│   │Elevon│ │Elevon│        │  Signal  │                         │
│   └──────┘ └──────┘        └──────────┘                         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Connection Details

#### Battery to ESC

| From | To | Connector | Wire |
|------|-----|-----------|------|
| Battery + | ESC + | XT60 | Red (thick) |
| Battery - | ESC - | XT60 | Black (thick) |

#### ESC to Motor

| ESC Wire | Motor Wire | Notes |
|----------|------------|-------|
| Wire A | Any motor wire | |
| Wire B | Any motor wire | |
| Wire C | Any motor wire | |

**Note:** If motor spins wrong direction, swap any two wires.

#### ESC BEC to Receiver

| ESC BEC | Receiver | Wire Color |
|---------|----------|------------|
| 5V | VCC | Red |
| GND | GND | Black/Brown |
| Signal | Not connected | |

Use the servo lead from ESC. Only power pins connect to receiver.

#### Receiver to Servos

| Receiver Channel | Servo | Function |
|------------------|-------|----------|
| CH1 | Left wing servo | Left elevon |
| CH2 | Right wing servo | Right elevon |

| Servo Wire | Receiver Pin |
|------------|--------------|
| Signal (Orange/White) | Signal |
| VCC (Red) | VCC |
| GND (Brown/Black) | GND |

#### Receiver to ESC Throttle

| Receiver Channel | ESC | Notes |
|------------------|-----|-------|
| CH3 | ESC signal wire | Throttle control |

Only connect the signal wire (white/orange) from CH3 to ESC signal input.

---

## Servo Connector Pinout

```
Standard Servo Connector (looking at plug)

┌─────────────────┐
│  ┌───┬───┬───┐  │
│  │ S │ + │ - │  │
│  └───┴───┴───┘  │
└─────────────────┘
     │   │   │
     │   │   └── GND (Brown or Black)
     │   └────── VCC (Red)
     └────────── Signal (Orange, White, or Yellow)
```

---

## Phase 2: Flight Controller Wiring

### Overview Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    MATEK F405-WING CONNECTIONS                   │
│                                                                  │
│                      ┌─────────────────┐                        │
│                      │    BN-880 GPS   │                        │
│                      │   ┌─────────┐   │                        │
│                      │   │   GPS   │   │                        │
│                      │   │ Module  │   │                        │
│                      │   └────┬────┘   │                        │
│                      │        │TX/RX   │                        │
│                      │   ┌────┴────┐   │                        │
│                      │   │ Compass │   │                        │
│                      │   │ (I2C)   │   │                        │
│                      │   └────┬────┘   │                        │
│                      └────────┼────────┘                        │
│                               │                                  │
│   ┌──────────┐         ┌──────┴──────┐         ┌──────────┐    │
│   │   ELRS   │         │    MATEK    │         │    ESC   │    │
│   │ Receiver │◄───────►│  F405-WING  │◄───────►│   30A    │    │
│   └──────────┘  SBUS   │             │  Motor  └────┬─────┘    │
│                        │  S1  S2  S3 │               │          │
│                        └───┬───┬───┬─┘               │          │
│                            │   │   │                 │          │
│                            ▼   ▼   │                 ▼          │
│                        ┌──────────┐│           ┌──────────┐    │
│                        │  Servos  ││           │  Motor   │    │
│                        │ L    R   ││           │  A2212   │    │
│                        └──────────┘│           └──────────┘    │
│                                    │                            │
│                                    └──► ESC Signal              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Matek F405-Wing Pinout

```
┌────────────────────────────────────────────────────────────┐
│                     MATEK F405-WING                        │
│                      (Top View)                            │
│                                                            │
│   ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐       │
│   │ S1  │ S2  │ S3  │ S4  │ S5  │ S6  │ LED │ BZ  │       │
│   └──┬──┴──┬──┴──┬──┴─────┴─────┴─────┴─────┴─────┘       │
│      │     │     │                                         │
│      │     │     └── ESC Throttle                         │
│      │     └──────── Right Elevon Servo                   │
│      └────────────── Left Elevon Servo                    │
│                                                            │
│   ┌─────┬─────┬─────┬─────┐                               │
│   │SBUS │ 5V  │ GND │ 4V5 │  ◄── Receiver Input          │
│   └─────┴─────┴─────┴─────┘                               │
│                                                            │
│   ┌─────┬─────┬─────┬─────┐                               │
│   │ TX1 │ RX1 │ 5V  │ GND │  ◄── GPS UART                │
│   └─────┴─────┴─────┴─────┘                               │
│                                                            │
│   ┌─────┬─────┬─────┬─────┐                               │
│   │ TX2 │ RX2 │ 5V  │ GND │  ◄── Pi UART (MAVLink)       │
│   └─────┴─────┴─────┴─────┘                               │
│                                                            │
│   ┌─────┬─────┐                                           │
│   │ SCL │ SDA │  ◄── I2C (GPS Compass)                   │
│   └─────┴─────┘                                           │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### GPS Connection

| GPS Module | FC Pin | Notes |
|------------|--------|-------|
| VCC | 5V | Power |
| GND | GND | Ground |
| TX | RX1 | GPS data to FC |
| RX | TX1 | FC to GPS (optional) |
| SCL | SCL | Compass I2C |
| SDA | SDA | Compass I2C |

---

## Phase 3: Pi Zero 2 W Integration

### Overview Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      COMPLETE SYSTEM                             │
│                                                                  │
│   ┌──────────────┐                                              │
│   │   3S LiPo    │                                              │
│   │   Battery    │                                              │
│   └──────┬───────┘                                              │
│          │                                                       │
│          ├─────────────────────────────┐                        │
│          │                             │                         │
│          ▼                             ▼                         │
│   ┌──────────────┐              ┌──────────────┐                │
│   │    30A ESC   │              │   5V 3A BEC  │                │
│   │              │              │  (Dedicated) │                │
│   └──────┬───────┘              └──────┬───────┘                │
│          │                             │                         │
│          ▼                             ▼                         │
│   ┌──────────────┐              ┌──────────────┐                │
│   │    Motor     │              │ Pi Zero 2 W  │◄── Pi Camera   │
│   │    A2212     │              │              │                 │
│   └──────────────┘              │         USB ─┼──► Coral TPU   │
│                                 │              │                 │
│   ┌──────────────┐              │    GPIO ─────┼──┐             │
│   │   MATEK FC   │◄─────────────┼──────────────┘  │ UART       │
│   │              │   MAVLink    │                  │             │
│   └──────────────┘              └──────────────────┘             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Pi Zero 2 W GPIO Pinout

```
┌─────────────────────────────────────────────────────────────────┐
│                    Pi Zero 2 W GPIO Header                       │
│                      (40-pin, top view)                          │
│                                                                  │
│   ┌────┬────┐                                                   │
│   │3.3V│ 5V │◄── Pin 2: Connect to 5V BEC output               │
│   ├────┼────┤                                                   │
│   │SDA │ 5V │                                                   │
│   ├────┼────┤                                                   │
│   │SCL │GND │◄── Pin 6: Connect to FC GND + BEC GND            │
│   ├────┼────┤                                                   │
│   │GP4 │TXD │◄── Pin 8 (GPIO14): Connect to FC UART2 RX        │
│   ├────┼────┤                                                   │
│   │GND │RXD │◄── Pin 10 (GPIO15): Connect to FC UART2 TX       │
│   ├────┼────┤                                                   │
│   │GP17│GP18│                                                   │
│   ├────┼────┤                                                   │
│   │    │    │                                                   │
│   │ .. │ .. │  (remaining pins)                                 │
│   │    │    │                                                   │
│   └────┴────┘                                                   │
│                                                                  │
│   Pin 1 is at top-left (3.3V)                                   │
│   Odd pins on left, even pins on right                          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### UART Connection (Pi to FC)

| Pi Zero 2 W | Matek F405-Wing | Notes |
|-------------|-----------------|-------|
| Pin 8 (GPIO14/TX) | UART2 RX | Pi TX to FC RX |
| Pin 10 (GPIO15/RX) | UART2 TX | FC TX to Pi RX |
| Pin 6 (GND) | GND | Common ground |

**IMPORTANT:** Do not connect 5V between Pi and FC. Power Pi from dedicated BEC only.

### Dedicated Pi Power (5V BEC)

```
Battery ────► 5V 3A BEC ────► Pi Zero 2 W
                │                  │
                │                  ├──► Pin 2 (5V)
                │                  └──► Pin 6 (GND)
                │
                └──► Coral USB TPU (via Pi USB)
```

| BEC Output | Pi Pin | Notes |
|------------|--------|-------|
| 5V | Pin 2 | Power input |
| GND | Pin 6 | Ground |

---

## Wire Color Standards

### Servo/Signal Wires

| Color | Function |
|-------|----------|
| Red | VCC (+5V) |
| Black/Brown | GND |
| Orange/White/Yellow | Signal |

### Power Wires

| Color | Function |
|-------|----------|
| Red | Positive (+) |
| Black | Negative (-) |

### Motor Wires

| Wire | Function |
|------|----------|
| Any 3 wires | 3-phase brushless |
| Swap any 2 | Reverse direction |

---

## Common Issues

### ESC Not Arming

| Symptom | Cause | Fix |
|---------|-------|-----|
| ESC beeps continuously | Throttle not at zero | Move throttle stick to minimum |
| ESC single beep | No signal | Check receiver connection |
| ESC doesn't beep | No power | Check battery connection |

### Servo Jitter

| Symptom | Cause | Fix |
|---------|-------|-----|
| Servos twitch at idle | Voltage drop | Use adequate BEC |
| Random movements | Signal interference | Route wires away from ESC |
| Slow response | Long servo leads | Keep leads short |

### No FC Connection to Pi

| Symptom | Cause | Fix |
|---------|-------|-----|
| No data on Pi | TX/RX swapped | Swap TX and RX wires |
| Garbled data | Baud rate mismatch | Set same baud on both |
| Intermittent | Loose connection | Resolder/secure wires |
