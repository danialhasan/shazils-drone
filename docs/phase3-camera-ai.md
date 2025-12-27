# Phase 3: Edge AI Payload (Compute TBD)

The Pi Zero 2 W was returned, so this phase documents requirements for whichever SBC we can actually source (Pi 4, Orange Pi 5, Jetson Nano, etc.).

## Overview

| Aspect | Details |
|--------|---------|
| Goal | Real-time detection + MAVLink actions |
| Prereqs | Phase 2 (autonomy) complete |
| Compute | TBD single-board computer + camera + accelerator |
| Duration | 3–5 days once hardware arrives |
| Difficulty | Advanced |

---

## Hardware Requirements (draft)

| Component | Notes |
|-----------|-------|
| SBC (Pi 4 / Orange Pi / Jetson) | Needs >4 GB RAM, USB3, UART access |
| Camera | Autofocus CSI or high-quality USB |
| Accelerator | Coral USB or onboard NPU |
| Storage | 64 GB A2 microSD or NVMe |
| Power | Dedicated 5 V/3 A BEC + LC filter |
| UART harness | Matek UART2 ↔ SBC GPIO/UART |
| Mounts | Foam/TPU cradle with vibration isolation |

Optional: USB hub, heatsinks/fans, quick-release canopy.

---

## Architecture

```
Camera → SBC (capture) → Accelerator (inference) → Decision logic → MAVLink (UART2)
                                      │                                     │
                                      └─────────────> Flight Controller <───┘
```

Power: 3S LiPo → dedicated 5 V BEC → SBC + accelerator (never share Matek BEC).

---

## Software Stack (to be finalized)

1. Headless OS (Raspberry Pi OS Lite, Armbian, JetPack, etc.).
2. Camera drivers (`libcamera`, `picamera2`, or V4L2 depending on hardware).
3. Inference runtime (TensorFlow Lite + pycoral, ONNX Runtime, OpenVINO, etc.).
4. MAVLink interface (`pymavlink` / `dronekit`).
5. Suggested project layout:
   ```
   src/
   ├── main.py
   ├── capture.py
   ├── detection.py
   ├── behaviors.py
   └── mavlink_bridge.py
   ```

---

## Behaviors

- **Track:** keep detected object centered via yaw adjustments.
- **Orbit:** compute approximate GPS coordinates and send `MAV_CMD_DO_ORBIT`.
- **Follow:** maintain distance corridor (GUIDED mode commands).
- **Logging:** record detections + GPS for review.

All behaviors must obey a manual override/kill switch and drop out when the pilot switches to MANUAL or flips the disable channel.

---

## Power Management

```
3S LiPo ─┬─► ESC/FC (stock wiring)
         └─► 5 V/3 A BEC ──► SBC ──► (optional) Coral USB
```

- LC filter near SBC input to tame ESC noise.
- Budget ≈1.5 A peak for SBC + camera + accelerator.

---

## Open Questions

1. Which SBC is actually in stock locally?
2. Can we rely on the SBC’s built-in NPU if Coral USB is unavailable?
3. How much payload mass can the foam glider handle with Phase 2 gear installed?
4. Do we need active cooling inside the canopy?

Document decisions here before purchasing to keep the BOM accurate.

---

## Next Steps

1. Select SBC + accelerator and update this doc with concrete install steps.
2. Prototype detection on recorded footage using a desktop.
3. Design/print a payload bay and ensure CG stays forward.
4. Integrate once Phase 2 flights are rock-solid with extra weight margin.
