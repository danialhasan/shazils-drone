# Wiring Reference (Foam Glider)

## Phase 1: Basic RC Wiring

```
3S LiPo ──► 30A ESC ──► 2212 motor
              │
              └─ 5 V BEC ──► ELRS receiver ──► Servos / ESC signal
```

| Connection | Notes |
|------------|-------|
| Battery → ESC | XT60 connector, short leads |
| ESC → Motor | Swap any two wires to reverse spin |
| ESC BEC → Receiver | Use only +5 V/GND pins from ESC servo lead |
| Receiver CH1 | Left elevon servo |
| Receiver CH2 | Right elevon servo |
| Receiver CH3 | ESC signal (white/orange wire only) |

---

## Phase 2: Flight Controller Integration

```
ELRS SBUS  ──► Matek SBUS
Servo L    ──► Matek S1
Servo R    ──► Matek S2
ESC Signal ──► Matek S3
BN-880 GPS ──► UART1 (TX/RX + 5 V)
BN-880 Mag ──► I2C (SCL/SDA + 5 V)
UART2       ─► Reserved for Phase 3 SBC
```

- Mount Matek board at CG, arrow forward.
- Twist GPS wires and keep ≥10 cm away from ESC/motor wires.
- Label TX2/RX2 leads now for quick SBC integration.

---

## Phase 3: Edge Compute Wiring

| Signal | Destination | Notes |
|--------|-------------|-------|
| UART2 TX (FC) | SBC RX | MAVLink telemetry |
| UART2 RX (FC) | SBC TX | AI commands |
| 5 V / GND | Dedicated BEC | Never power SBC from FC BEC |
| Camera | SBC CSI or USB | Choose cable length per mount |
| Accelerator | USB 3.0 | Keep <30 cm |

```
3S LiPo
  ├─► ESC/FC harness
  └─► 5 V BEC ──► SBC ──► (optional) Coral USB
```

> **Important:** isolate power domains. Matek’s BEC = servos/GPS only; compute payload gets its own regulator.

---

## Wire Colors & Tips

- Signal: white/orange/yellow
- Power (+): red
- Ground: black/brown
- Tape wires along fuselage spine, leave slack for canopy removal, avoid rubbing against pushrods.

---

## Common Issues

| Symptom | Cause | Fix |
|---------|-------|-----|
| ESC beeps continuously | Throttle high or no signal | Recalibrate throttle / check bind |
| Servo chatter | Voltage drop or long leads | Shorten extensions, secure BEC |
| GPS noise | Too close to ESC/motor wires | Relocate to top hatch, twist wires |
| SBC brownouts | Sharing FC BEC | Install dedicated 5 V/3 A regulator |
