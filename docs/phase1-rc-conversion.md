# Phase 1: Foam Glider RC Conversion

Convert the AliExpress foam glider (white/red “GLIDER” airframe shown in the supplier photos) into a reliable ELRS-controlled aircraft. On-hand gear now includes the airframe, adhesives, propeller, motor, pushrods, and control horns; we still need to reorder servos and pick up the main LiPo battery. This replaces the earlier Reptile S800 V2 plan.

## Overview

| Aspect | Details |
|--------|---------|
| Airframe | ≈950 mm EPP foam glider kit (AliExpress) |
| Goal | Stable manual flight with elevon mixing |
| Duration | ~1 day after parts arrive |
| Difficulty | Beginner / intermediate |
| Prereqs | Basic soldering + ELRS setup |

---

## Step 1: Receive & Inspect

| Item | Received | Inspected | Notes |
|------|----------|-----------|-------|
| Foam glider kit | ☐ | ☐ | Check for warps/dents |
| 2212 motor + 30A ESC | ☐ | ☐ | Spin test, confirm connectors |
| MG90S servos (x2) | ☐ | ☐ | Ensure smooth movement |
| 3S battery | ☐ | ☐ | Storage voltage ≈11.4 V |
| Hot glue / Foam-Tac | ☐ | ☐ | Needed for wing joins |
| Propellers | ☐ | ☐ | Balance and inspect |

---

## Step 2: Assemble Airframe

1. Dry-fit wing halves, spar, and tailplane; mark CG (~30–33% chord).
2. Glue with foam-safe adhesive; reinforce motor mount if foam is soft.
3. Route servo leads through wing channels before sealing.

### Servos & Linkages
- Mount MG90S flush with wing; servo arm perpendicular at neutral.
- Install control horns ~25 mm from elevon root; use Z-bend or clevis.
- Keep pushrods short and slop-free.

### Power System
- Bolt 2212 motor to pusher mount; verify prop clearance.
- Velcro ESC near airflow; leave access to bullet connectors.
- Install battery tray just ahead of CG with hook-and-loop strap.

---

## Step 3: Wiring & Radio

```
3S LiPo → 30A ESC → Motor
              │
              └─ 5 V BEC → ELRS receiver
ELRS CH1 → Left elevon servo
ELRS CH2 → Right elevon servo
ELRS CH3 → ESC throttle signal (signal wire only)
```

- Keep power/battery wiring tidy to prepare space for Phase 2 electronics.

---

## Step 4: Bind & Configure

1. Create new model (“Foam Glider”) on RadioMaster Pocket.
2. Internal RF: ELRS @ 250 Hz.
3. Enter receiver bind mode (button or triple power cycle) and bind from radio.
4. Verify servo movement before closing fuselage.

### Elevon Mixing
- Enable delta/flying-wing mode.
- CH1 = left elevon, CH2 = right, CH3 = throttle.
- Test directions; reverse channels if needed.

---

## Step 5: CG & Pre-Flight

- Target CG ≈30% chord; bias nose-heavy if unsure (add coins/tape up front).
- Mechanical checklist: screws tight, battery secure, control surfaces neutral, prop tight.
- Electronic checklist: receiver bound, failsafe set, throttle low = motor off, range test at 30 m.

### Maiden Flight Tips
1. Fly in calm conditions (<10 km/h wind).
2. Launch at ~70% throttle, nose slightly up; climb to 30 m before trimming.
3. Practice gentle turns and approach into the wind for landing.

---

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Rolls immediately | Elevons reversed | Reverse CH1/CH2 |
| Porpoising | CG too aft | Move battery forward |
| Motor won’t arm | Throttle not zero / ESC calibration | Recalibrate |
| Servo jitter | Loose BEC or long leads | Reseat plugs, shorten wires |

---

## Handoff to Phase 2

1. Note trim values + battery placement before adding FC hardware.
2. Identify mounting spots for Matek F405-Wing, GPS, pitot tube.
3. Leave extra wire slack for future UART connections (Phase 2 doc).
