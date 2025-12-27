# Shazil's Drone – AI-Ready Foam Glider Project

Upgrading an inexpensive AliExpress foam glider into an AI-assisted UAV. The build is broken into three phases so we can fly quickly, keep the airframe simple, and postpone compute hardware decisions until we replace the returned Raspberry Pi Zero 2 W.

## Project Overview

| Phase | Description | Status |
|-------|-------------|--------|
| [Phase 1: Foam Glider RC Conversion](docs/phase1-rc-conversion.md) | Turn the foam glider into a dependable ELRS-controlled airplane. | In Progress |
| [Phase 2: Autonomy](docs/phase2-autonomy.md) | Install ArduPilot FC + GPS for waypoint and RTL flight. | Planned |
| [Phase 3: Edge AI Payload](docs/phase3-camera-ai.md) | Add a new SBC + camera + accelerator (TBD) for detection-driven missions. | Planned |

## Hardware Summary

### Already Owned
- 2212/1400KV brushless motor + 30A ESC
- ELRS receiver + RadioMaster Pocket transmitter
- 3S LiPo packs and charger

### To Acquire
- AliExpress foam glider kit (≈900–1000 mm span)
- Matek F405-Wing (or similar) + BN-880 GPS
- SBC replacement for the returned Pi Zero 2 W (Pi 4, Orange Pi, Jetson Nano, etc.) + camera
- Coral USB accelerator (optional, based on SBC choice)

## Quick Links
- [Updated parts list](docs/parts-list.md)
- [Phase 1 build & checkout guide](docs/phase1-rc-conversion.md)
- [Phase 2 autonomy plan](docs/phase2-autonomy.md)
- [Phase 3 edge-AI payload plan](docs/phase3-camera-ai.md)
- [Wiring reference](docs/wiring.md)

## Architecture Snapshot

```
┌─────────────────────────────────────────────────────────────┐
│                FUTURE: EDGE AI PAYLOAD (PHASE 3)            │
│  ┌──────────────────────────┐   ┌────────────────────────┐  │
│  │  SBC (TBD)               │   │  Accelerator (Coral?)  │  │
│  │  - Camera driver         │   │  - Real-time inference │  │
│  │  - Detection software    │   │  - 30+ FPS             │  │
│  └───────────┬──────────────┘   └──────────┬─────────────┘  │
│              │ UART (MAVLink)              │ USB            │
└──────────────┼─────────────────────────────┼────────────────┘
               │                             │
┌──────────────▼─────────────────────────────▼───────────────┐
│              PHASE 2: AUTONOMY CORE                        │
│  Matek F405-Wing (ArduPilot) + BN-880 GPS/compass          │
│  - Manual, FBWA, AUTO, RTL, Loiter                         │
│  - GPS waypoint nav + failsafes                            │
└──────────────┬─────────────────────────────┬───────────────┘
               │                             │
┌──────────────▼─────────────────────────────▼───────────────┐
│              PHASE 1: RC LAYER                             │
│  RadioMaster Pocket  ──►  ELRS Receiver                    │
│  Elevon servos        ──►  Foam glider control surfaces    │
│  30A ESC + 2212 motor ──►  Propulsion                      │
└────────────────────────────────────────────────────────────┘
```

## Team & Ownership
- **Hardware / Flight Testing:** Shazil (airframe assembly, wiring, flight logs)
- **Software / Planning:** Documentation + AI assistant support

## License
MIT
