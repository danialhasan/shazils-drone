# Phase 1: RC Conversion

Convert the Reptile S800 V2 flying wing into a working remote-controlled aircraft.

## Overview

| Aspect | Details |
|--------|---------|
| Goal | Get the plane flying with remote control |
| Duration | 1-2 days after parts arrive |
| Difficulty | Beginner-Intermediate |
| Prerequisites | Basic soldering, RC experience helpful |

---

## Step 1: Receive and Inspect Parts

### Checklist

| Item | Received | Inspected | Notes |
|------|----------|-----------|-------|
| S800 V2 Airframe | ☐ | ☐ | Check for cracks in foam |
| 30A ESC | ☐ | ☐ | Verify XT60 + bullet connectors |
| MG90S Servos (x2) | ☐ | ☐ | Test movement by hand |
| 3S Battery | ☐ | ☐ | Check voltage (should be ~11.1-11.4V) |
| Balance Charger | ☐ | ☐ | Verify plug compatibility |
| Propellers | ☐ | ☐ | Check for cracks, balance |

---

## Step 2: Assemble Airframe

### 2.1 Unbox S800 V2

The kit includes:
- Main wing body (EPP foam)
- Vertical stabilizers (2x)
- Motor mount
- Control horns and pushrods
- Decals

### 2.2 Install Vertical Stabilizers

1. Dry fit stabilizers into wing slots
2. Apply hot glue to slots
3. Insert stabilizers, ensure perpendicular to wing
4. Hold until glue sets (~30 seconds)

### 2.3 Install Servos

1. Locate servo bays in wing (rectangular cutouts)
2. Place MG90S servos in bays
3. Route servo wires through internal channels toward center
4. Secure servos with hot glue or double-sided tape
5. Attach servo arms (straight arm, perpendicular to hinge line)

### 2.4 Connect Control Horns and Pushrods

1. Install control horns on elevons (outer hole for more throw)
2. Connect pushrods from servo arm to control horn
3. Adjust length so elevons are neutral when servo centered
4. Secure with Z-bend or clevis

### 2.5 Mount Motor

1. Attach A2212 motor to rear motor mount
2. Secure with M3 screws (come with motor)
3. Ensure motor shaft points straight back
4. Route motor wires into fuselage

### 2.6 Mount ESC

1. Velcro ESC inside fuselage
2. Position for airflow (near motor or air vent)
3. Connect motor wires to ESC (3 bullet connectors)
4. Route power wires (XT60) toward battery bay

### 2.7 Install Propeller

1. Attach prop adapter to motor shaft
2. Mount 8060 or 9045 propeller
3. Tighten prop nut securely
4. **Verify rotation direction** - should push air backward (pusher config)

---

## Step 3: Wire Electronics

See [Wiring Diagram](wiring.md) for detailed schematic.

### Quick Wiring Summary

```
Battery (XT60)
    │
    ▼
   ESC ─────────────► Motor (3 bullet connectors)
    │
    │ BEC (5V servo lead)
    ▼
ELRS Receiver
    │
    ├── CH1 → Left Elevon Servo
    ├── CH2 → Right Elevon Servo
    └── CH3 → ESC Throttle (white signal wire)
```

### Connections

| From | To | Wire/Connector |
|------|-----|----------------|
| Battery | ESC | XT60 |
| ESC | Motor | 3x Bullet Connectors |
| ESC BEC | Receiver | 3-pin servo lead (power) |
| Receiver CH1 | Left Servo | 3-pin servo lead |
| Receiver CH2 | Right Servo | 3-pin servo lead |
| Receiver CH3 | ESC | Signal wire only (white) |

---

## Step 4: Bind ELRS Receiver

### Prerequisites
- RadioMaster Pocket powered on
- ELRS receiver powered (via ESC BEC)

### Binding Procedure

1. **Create new model on RadioMaster:**
   - Long press MDL button
   - Create New Model
   - Name it "S800" or similar

2. **Configure Internal RF:**
   - SYS → Internal RF → ELRS
   - Set packet rate (250Hz recommended for fixed wing)

3. **Put receiver in bind mode:**
   - Method A: Press bind button on receiver 3x quickly
   - Method B: Power cycle receiver 3x within 5 seconds
   - LED should flash rapidly

4. **Initiate bind from radio:**
   - SYS → ELRS → Bind
   - Wait for receiver LED to go solid

5. **Verify connection:**
   - Move sticks, receiver LED should flicker
   - Servos should respond to stick input

---

## Step 5: Configure Elevon Mixing

Flying wings use **elevon mixing** - combined aileron and elevator function.

### RadioMaster Pocket Setup

1. **Model Setup → Aircraft Type:**
   - Select "Flying Wing" or "Delta"

2. **Channel Assignment:**
   - CH1 = Elevon Left
   - CH2 = Elevon Right
   - CH3 = Throttle

3. **Test Control Directions:**

| Stick Input | Left Elevon | Right Elevon |
|-------------|-------------|--------------|
| Elevator Up (pull back) | UP | UP |
| Elevator Down (push forward) | DOWN | DOWN |
| Aileron Left | UP | DOWN |
| Aileron Right | DOWN | UP |

4. **Reverse channels if needed:**
   - If a direction is wrong, reverse that channel in radio settings
   - MDL → Outputs → Select channel → Reverse

5. **Adjust throws:**
   - Start with 70% throw (dual rates)
   - Increase to 100% once comfortable

---

## Step 6: Balance Center of Gravity (CG)

**CRITICAL: Incorrect CG will cause crash on first flight.**

### S800 V2 CG Specification

- **CG Point: 125mm from leading edge of wing (nose)**
- Tolerance: +/- 5mm
- **Nose-heavy is safer** than tail-heavy

### Balancing Procedure

1. Fully assemble aircraft (battery installed)
2. Mark CG point on both wings (125mm from nose)
3. Support aircraft by fingertips at CG marks
4. Observe balance:
   - **Nose tips down slightly** = Good
   - **Tail tips down** = Too tail-heavy (DANGEROUS)
   - **Level** = Slightly nose-heavy (OK)

### Adjusting CG

| Problem | Solution |
|---------|----------|
| Tail heavy | Move battery forward |
| Nose heavy | Move battery backward |
| Still wrong | Add weight to nose or tail |

---

## Step 7: Pre-Flight Checklist

Complete all checks before first flight.

### Mechanical Checks

| Check | Pass |
|-------|------|
| All screws tight (motor, prop) | ☐ |
| Prop secure, no wobble | ☐ |
| Control surfaces move freely | ☐ |
| No loose wires near prop | ☐ |
| Battery secured with strap | ☐ |
| CG verified at 125mm | ☐ |

### Electronic Checks

| Check | Pass |
|-------|------|
| Receiver bound to transmitter | ☐ |
| Servos respond correctly to sticks | ☐ |
| Throttle stick down = motor off | ☐ |
| Throttle responds smoothly | ☐ |
| No servo jitter at idle | ☐ |

### Range Check

1. Power on aircraft
2. Walk 30 meters away
3. Move all control surfaces
4. Verify smooth response
5. No glitching or dropouts

---

## Step 8: First Flight

### Conditions

| Factor | Ideal | Acceptable |
|--------|-------|------------|
| Wind | Calm | <10 km/h |
| Location | Large open field | Clear of obstacles |
| Time | Morning/Evening | Low wind periods |

### Launch Procedure (Hand Launch)

1. Set throttle to ~70%
2. Hold aircraft by fuselage, nose slightly up (10-15°)
3. Run forward 3-4 steps
4. Release with firm forward throw
5. **Immediately** apply slight up elevator
6. Climb to safe altitude (30+ meters) before turning

### First Flight Goals

1. Achieve stable climb
2. Trim for level flight (use trim buttons)
3. Make gentle turns
4. Practice approaches
5. Land into wind

### Landing Procedure

1. Approach into wind
2. Reduce throttle gradually
3. Maintain slight nose-up attitude
4. Flare just before touchdown
5. Cut throttle on contact

---

## Troubleshooting

### Servos Not Responding

| Check | Solution |
|-------|----------|
| Receiver LED solid? | If blinking, re-bind |
| ESC powering receiver? | Check BEC connection |
| Correct channel assigned? | Verify in radio setup |

### Plane Rolls/Pitches Unexpectedly

| Problem | Cause | Fix |
|---------|-------|-----|
| Rolls one direction | Ailerons reversed | Reverse channels |
| Dives on launch | Elevator reversed | Reverse elevator |
| Climbs aggressively | Too much up trim | Reduce elevator trim |

### Motor Not Spinning

| Check | Solution |
|-------|----------|
| Throttle at zero? | ESC needs zero throttle to arm |
| ESC beeping? | Listen for error codes |
| Battery charged? | Check voltage |
| Connections secure? | Inspect bullet connectors |

### CG Issues in Flight

| Behavior | Cause | Fix |
|----------|-------|-----|
| Nose dives, hard to pull up | Too nose heavy | Move battery back |
| Bobs up and down (porpoising) | Too tail heavy | Move battery forward |
| Requires constant up elevator | Tail heavy | Add nose weight |

---

## Next Steps

Once flying reliably:
1. Practice flight maneuvers
2. Log flight times
3. Proceed to [Phase 2: Autonomy](phase2-autonomy.md)
