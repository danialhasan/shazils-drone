# Parts List (AliExpress Foam Glider Build)

The Raspberry Pi Zero 2 W has been returned, so compute hardware is TBD. Costs below are rough CAD estimates.

## Phase 1: Foam Glider RC Conversion

| Component | Specification / Link | Est. Price (CAD) | Status |
|-----------|---------------------|------------------|--------|
| Airframe | AliExpress EPP foam glider (≈950 mm span) | $40 | To Order |
| Motor + ESC | Existing 2212/1400KV + 30A ESC with 5 V BEC | $0 | Owned |
| Servos | MG90S 9 g metal gear (pair) | $12 | To Order |
| ELRS receiver | 2.4 GHz PWM-capable ELRS | $0 | Owned |
| Batteries | 3S 2200–3000 mAh LiPo | $45 | Owned |
| Propellers | 8×6 slow-fly or similar | $10 | To Order |
| Adhesives | Foam-safe hot glue / Foam-Tac | $8 | To Order |

**Phase 1 subtotal:** ≈$70 using mostly existing electronics.

**What’s already on hand:** AliExpress foam glider airframe, adhesives (Foam-Tac/tape), propeller, motor (2212/1400KV), pushrods, and control horns. **Still needed:** fresh MG90S servos and a 3S LiPo pack (2200–3000 mAh).

## Phase 2: Autonomy

| Component | Specification | Est. Price (CAD) | Status |
|-----------|---------------|------------------|--------|
| Flight controller | Matek F405-Wing (ArduPilot) | $55 | Planned |
| GPS + compass | BN-880 | $20 | Planned |
| Airspeed sensor | Optional pitot tube | $15 | Planned |
| Telemetry radios | 915 MHz or 433 MHz pair | $35 | Planned |
| Wiring harness | JST-GH / Dupont jumpers | $15 | Planned |

**Phase 2 subtotal:** ≈$140 depending on optional sensors.

## Phase 3: Edge AI Payload (TBD)

| Component | Notes | Est. Price (CAD) | Status |
|-----------|-------|------------------|--------|
| SBC (compute) | Evaluating Pi 4, Orange Pi 5, Jetson Nano, etc. | $120–$200 | TBD |
| Camera | Autofocus CSI or quality USB cam | $40 | TBD |
| Accelerator | Coral USB (preferred) or onboard NPU | $75 | TBD |
| Storage | 64 GB A2 microSD or NVMe | $20 | TBD |
| Power | Dedicated 5 V/3 A BEC + filter | $15 | TBD |
| Mounting | TPU/foam cradle for payload bay | $20 | TBD |

**Phase 3 subtotal:** ≈$250 once hardware is selected.

## Budget Snapshot

| Phase | Cost |
|-------|------|
| Phase 1 | ~$70 |
| Phase 2 | ~$140 |
| Phase 3 | ~$250 (pending) |
| **Total** | **~$460** |

## Notes
- Keep a spare foam glider on order since the airframe is inexpensive.
- Delay SBC purchase until availability/power requirements are clear.
- Revisit battery capacity after autonomy hardware is mounted to maintain CG.
