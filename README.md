# Shazil's Drone - AI-Powered UAV Project

An AI-powered UAV built from a converted RC flying wing, featuring object detection and autonomous flight capabilities.

## Project Overview

This project converts a Reptile S800 V2 flying wing into an autonomous UAV with:
- Remote control flight (Phase 1)
- Autonomous flight via ArduPilot (Phase 2)
- AI-powered object detection using Raspberry Pi + Coral TPU (Phase 3)

## Project Phases

| Phase | Description | Status |
|-------|-------------|--------|
| [Phase 1: RC Conversion](docs/phase1-rc-conversion.md) | Get the plane flying with remote control | In Progress |
| [Phase 2: Autonomy](docs/phase2-autonomy.md) | Add flight controller + GPS for autonomous flight | Planned |
| [Phase 3: Camera/AI](docs/phase3-camera-ai.md) | Integrate Pi Zero 2 W + Coral TPU for object detection | Planned |

## Hardware Summary

### Already Owned
- A2212 1400kv brushless motor
- ELRS receiver
- RadioMaster Pocket transmitter
- Raspberry Pi Zero 2 W

### To Acquire
See [Parts List](docs/parts-list.md) for complete list with purchase links.

## Quick Links

- [Parts List with Amazon.ca Links](docs/parts-list.md)
- [Wiring Diagram](docs/wiring.md)
- [Phase 1: RC Conversion Guide](docs/phase1-rc-conversion.md)
- [Phase 2: Autonomy Software](docs/phase2-autonomy.md)
- [Phase 3: Camera & AI Integration](docs/phase3-camera-ai.md)

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      PHASE 3: AI LAYER                      │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Raspberry Pi Zero 2 W                   │   │
│  │  ┌──────────────┐    ┌─────────────────────────┐   │   │
│  │  │  Pi Camera   │───►│  Coral TPU (TFLite)     │   │   │
│  │  │  Module v3   │    │  Object Detection 30FPS │   │   │
│  │  └──────────────┘    └───────────┬─────────────┘   │   │
│  │                                  │                  │   │
│  │                    ┌─────────────▼─────────────┐   │   │
│  │                    │  DroneKit / pymavlink     │   │   │
│  │                    │  (Send commands to FC)    │   │   │
│  │                    └─────────────┬─────────────┘   │   │
│  └──────────────────────────────────┼─────────────────┘   │
└─────────────────────────────────────┼─────────────────────┘
                                      │ UART (MAVLink)
┌─────────────────────────────────────┼─────────────────────┐
│                    PHASE 2: AUTONOMY LAYER                 │
│  ┌──────────────────────────────────▼─────────────────┐   │
│  │          ArduPilot Flight Controller               │   │
│  │          (Matek F405-Wing)                         │   │
│  │  - GPS Navigation (BN-880)                         │   │
│  │  - Stabilization                                   │   │
│  │  - Waypoint Missions                               │   │
│  │  - Return to Home                                  │   │
│  └────────────────────────┬───────────────────────────┘   │
└───────────────────────────┼───────────────────────────────┘
                            │
┌───────────────────────────┼───────────────────────────────┐
│                    PHASE 1: RC LAYER                       │
│  ┌────────────────────────▼───────────────────────────┐   │
│  │              ELRS Receiver                          │   │
│  │              ◄──── RadioMaster Pocket               │   │
│  └─────┬─────────────┬─────────────┬──────────────────┘   │
│        │             │             │                       │
│        ▼             ▼             ▼                       │
│   ┌────────┐   ┌────────┐   ┌──────────┐                  │
│   │ Servo  │   │ Servo  │   │   ESC    │                  │
│   │ (Left) │   │(Right) │   │  (30A)   │                  │
│   └───┬────┘   └───┬────┘   └────┬─────┘                  │
│       │            │             │                         │
│       ▼            ▼             ▼                         │
│   ┌────────────────────┐   ┌──────────┐                   │
│   │  Elevons (Wings)   │   │  Motor   │                   │
│   │  Left    Right     │   │ A2212    │                   │
│   └────────────────────┘   └──────────┘                   │
│                                                            │
│   ┌────────────────────────────────────────────────────┐  │
│   │              Reptile S800 V2 Airframe              │  │
│   │              820mm Wingspan Flying Wing            │  │
│   └────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
```

## Team

- **Hardware Engineer**: Responsible for physical assembly, wiring, flight testing
- **Software/Planning**: Claude AI - Documentation, software architecture, troubleshooting guidance

## License

MIT
