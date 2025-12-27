# Phase 3: Camera & AI Integration

Integrate Raspberry Pi Zero 2 W with Coral TPU for real-time object detection and AI-guided autonomous flight.

## Overview

| Aspect | Details |
|--------|---------|
| Goal | Real-time object detection + AI-guided flight commands |
| Prerequisites | Phase 2 complete, stable autonomous flight |
| Duration | 3-5 days for setup + tuning |
| Difficulty | Advanced |

---

## Hardware Requirements

### Required Components

| Component | Specification | Purpose |
|-----------|---------------|---------|
| Pi Zero 2 W | Already owned | AI processing, camera interface |
| Pi Camera v3 | Autofocus, 12MP | Visual input |
| Coral USB Accelerator | Edge TPU | 30+ FPS inference |
| MicroSD Card | 64GB A2 | Storage, fast write |
| 5V 3A BEC | Dedicated | Pi power (separate from FC) |
| UART Cable | Dupont wires | Pi to FC communication |

### Optional Components

| Component | Purpose |
|-----------|---------|
| USB Hub | If adding more peripherals |
| Heat sink | Pi thermal management |
| Camera mount | Vibration dampening |

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    RASPBERRY PI ZERO 2 W                    │
│                                                             │
│  ┌───────────────┐      ┌─────────────────────────────────┐│
│  │  Pi Camera v3 │      │        SOFTWARE STACK           ││
│  │  (CSI Input)  │      │                                 ││
│  └───────┬───────┘      │  ┌─────────────────────────┐   ││
│          │              │  │   picamera2             │   ││
│          ▼              │  │   (Camera Capture)      │   ││
│  ┌───────────────┐      │  └───────────┬─────────────┘   ││
│  │ Coral USB TPU │      │              │                  ││
│  │ (Edge TPU)    │      │              ▼                  ││
│  └───────┬───────┘      │  ┌─────────────────────────┐   ││
│          │              │  │   TFLite + pycoral      │   ││
│          │              │  │   (Object Detection)    │   ││
│          ▼              │  │   - COCO SSD MobileNet  │   ││
│  ┌───────────────┐      │  │   - 30+ FPS             │   ││
│  │   Detected    │      │  └───────────┬─────────────┘   ││
│  │   Objects     │      │              │                  ││
│  │   - Person    │      │              ▼                  ││
│  │   - Vehicle   │      │  ┌─────────────────────────┐   ││
│  │   - Animal    │      │  │   Decision Engine       │   ││
│  └───────────────┘      │  │   (Python)              │   ││
│                         │  │   - Track objects       │   ││
│                         │  │   - Decide actions      │   ││
│                         │  │   - Log detections      │   ││
│                         │  └───────────┬─────────────┘   ││
│                         │              │                  ││
│                         │              ▼                  ││
│                         │  ┌─────────────────────────┐   ││
│                         │  │   pymavlink / dronekit  │   ││
│                         │  │   (FC Communication)    │   ││
│                         │  └───────────┬─────────────┘   ││
│                         └──────────────┼─────────────────┘│
└────────────────────────────────────────┼──────────────────┘
                                         │ UART (MAVLink)
                                         ▼
                              ┌─────────────────────┐
                              │   Flight Controller │
                              │   (ArduPilot)       │
                              │   - Execute commands│
                              │   - Fly to target   │
                              │   - Orbit object    │
                              └─────────────────────┘
```

---

## Pi Zero 2 W Specifications

| Spec | Value | Notes |
|------|-------|-------|
| CPU | Quad-core 1GHz ARM Cortex-A53 | 64-bit |
| RAM | 512MB | Shared with GPU |
| USB | 1x Micro USB OTG | For Coral TPU |
| Camera | CSI port | Pi Camera compatible |
| GPIO | 40-pin | UART for FC |
| Weight | 15g | Lightweight |

### Limitations

| Limitation | Mitigation |
|------------|------------|
| 512MB RAM | Use lite OS, minimal services |
| Single USB | Coral TPU only, no hub needed |
| No hardware AI | Coral USB Accelerator |
| Limited CPU | Offload inference to TPU |

---

## Software Stack

### Operating System

```bash
# Raspberry Pi OS Lite (64-bit)
# Headless, no desktop
# Minimal footprint
```

### Required Packages

```bash
# System
sudo apt update && sudo apt upgrade -y
sudo apt install -y python3-pip python3-venv git

# Camera
sudo apt install -y libcamera-apps python3-picamera2

# Coral TPU
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | \
  sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt update
sudo apt install -y libedgetpu1-std python3-pycoral

# MAVLink
pip3 install pymavlink dronekit
```

### Python Dependencies

```
# requirements.txt
numpy
opencv-python-headless
picamera2
tflite-runtime
pycoral
pymavlink
dronekit
```

---

## Object Detection

### Model: COCO SSD MobileNet v2

| Spec | Value |
|------|-------|
| Input Size | 300x300 |
| Classes | 90 (COCO dataset) |
| Speed (Coral) | 30-40 FPS |
| Speed (CPU) | 2-5 FPS |

### Detectable Objects

| Category | Objects |
|----------|---------|
| People | Person |
| Vehicles | Car, truck, bus, motorcycle, bicycle |
| Animals | Dog, cat, bird, horse, cow, sheep |
| Objects | Backpack, umbrella, handbag, suitcase |

### Detection Pipeline

```python
# Simplified detection loop
from pycoral.utils import edgetpu
from pycoral.adapters import detect
from picamera2 import Picamera2

# Initialize
interpreter = edgetpu.make_interpreter('model.tflite')
interpreter.allocate_tensors()
camera = Picamera2()
camera.start()

while True:
    # Capture frame
    frame = camera.capture_array()

    # Preprocess
    input_data = preprocess(frame)

    # Run inference on Coral TPU
    interpreter.set_tensor(input_index, input_data)
    interpreter.invoke()

    # Get detections
    detections = detect.get_objects(interpreter, threshold=0.5)

    # Process detections
    for obj in detections:
        label = labels[obj.id]
        confidence = obj.score
        bbox = obj.bbox

        # Decision logic
        if label == 'person' and confidence > 0.7:
            send_command_to_fc('orbit', obj.bbox)
```

---

## FC Communication (MAVLink)

### UART Configuration

```python
# Pi GPIO UART
# GPIO 14 (TXD) → FC RX
# GPIO 15 (RXD) → FC TX
# GND → GND

# Enable UART on Pi
# /boot/config.txt
enable_uart=1

# Disable serial console
sudo raspi-config → Interface Options → Serial Port
# Login shell: No
# Serial hardware: Yes
```

### MAVLink Connection

```python
from dronekit import connect, VehicleMode
from pymavlink import mavutil

# Connect to FC
vehicle = connect('/dev/serial0', baud=57600, wait_ready=True)

# Read telemetry
print(f"Location: {vehicle.location.global_frame}")
print(f"Altitude: {vehicle.location.global_relative_frame.alt}m")
print(f"Heading: {vehicle.heading}")
print(f"Battery: {vehicle.battery.voltage}V")

# Change mode
vehicle.mode = VehicleMode("LOITER")

# Go to location
from dronekit import LocationGlobalRelative
target = LocationGlobalRelative(lat, lon, alt)
vehicle.simple_goto(target)
```

---

## AI-Guided Flight Behaviors

### Behavior: Object Tracking

```python
def track_object(detection, vehicle):
    """Adjust heading to keep object centered in frame"""
    frame_center_x = 150  # Half of 300px input
    object_center_x = (detection.bbox.xmin + detection.bbox.xmax) / 2

    error = object_center_x - frame_center_x

    if abs(error) > 20:  # Dead zone
        # Adjust heading
        heading_adjustment = error * 0.1  # Gain
        new_heading = vehicle.heading + heading_adjustment

        # Send heading command
        condition_yaw(vehicle, new_heading)
```

### Behavior: Orbit Object

```python
def orbit_object(detection, vehicle):
    """Circle around detected object"""
    # Estimate object GPS position from detection
    obj_lat, obj_lon = estimate_gps_from_detection(
        detection,
        vehicle.location.global_frame,
        vehicle.heading,
        vehicle.location.global_relative_frame.alt
    )

    # Set loiter point
    vehicle.mode = VehicleMode("GUIDED")
    orbit_point = LocationGlobalRelative(obj_lat, obj_lon, alt)

    # ArduPilot orbit command
    msg = vehicle.message_factory.command_long_encode(
        0, 0,
        mavutil.mavlink.MAV_CMD_DO_ORBIT,
        0,
        50,      # Radius (meters)
        1,       # Velocity (m/s)
        0,       # Yaw behavior (0=face center)
        0, 0, 0, 0
    )
    vehicle.send_mavlink(msg)
```

### Behavior: Follow Object

```python
def follow_object(detection, vehicle):
    """Follow detected object maintaining distance"""
    # Calculate object position relative to drone
    distance, bearing = calculate_relative_position(detection)

    if distance > 30:  # Too far
        # Move closer
        vehicle.simple_goto(target_position)
    elif distance < 15:  # Too close
        # Move back
        vehicle.simple_goto(retreat_position)
    else:
        # Maintain position, adjust heading
        condition_yaw(vehicle, bearing)
```

---

## Power Management

### Dedicated Pi Power

**DO NOT power Pi from FC BEC** - Use separate 5V 3A BEC

```
Battery (XT60)
    │
    ├──► ESC (to motor + FC BEC)
    │
    └──► 5V 3A BEC ──► Pi Zero 2 W
                          │
                          └──► Coral USB TPU
```

### Power Consumption

| Component | Current | Notes |
|-----------|---------|-------|
| Pi Zero 2 W | 300-500mA | Under load |
| Coral USB | 500mA | During inference |
| Pi Camera v3 | 200mA | Recording |
| **Total** | ~1.2A | Peak |

**Minimum BEC requirement: 5V 2A (3A recommended)**

---

## Wiring Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    POWER DISTRIBUTION                        │
│                                                              │
│   Battery ──┬──► ESC ──► Motor                              │
│             │      │                                         │
│             │      └──► FC (via ESC BEC)                    │
│             │                                                │
│             └──► 5V BEC ──► Pi Zero 2 W ──► Coral TPU       │
│                                                              │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    DATA CONNECTIONS                          │
│                                                              │
│   Pi Camera v3 ───[CSI Ribbon]───► Pi Zero 2 W              │
│                                         │                    │
│   Coral USB TPU ───[USB OTG]────────────┤                   │
│                                         │                    │
│                                    [UART GPIO]               │
│                                         │                    │
│                                         ▼                    │
│                                   Flight Controller          │
│                                   (UART2 - MAVLink)          │
│                                                              │
└─────────────────────────────────────────────────────────────┘

GPIO Pinout:
┌─────────────────────┐
│  Pi Zero 2 W GPIO   │
├─────────────────────┤
│  Pin 1  (3.3V)      │ - Do not use for FC
│  Pin 2  (5V)        │ ◄── From BEC
│  Pin 6  (GND)       │ ──► FC GND
│  Pin 8  (GPIO14/TX) │ ──► FC UART2 RX
│  Pin 10 (GPIO15/RX) │ ◄── FC UART2 TX
└─────────────────────┘
```

---

## Software Installation

### Step 1: Flash Raspberry Pi OS

```bash
# Use Raspberry Pi Imager
# Select: Raspberry Pi OS Lite (64-bit)
# Configure: WiFi, SSH, hostname
```

### Step 2: Initial Setup

```bash
# SSH into Pi
ssh pi@shazil-drone.local

# Update system
sudo apt update && sudo apt upgrade -y

# Enable camera
sudo raspi-config
# → Interface Options → Camera → Enable

# Enable UART
sudo raspi-config
# → Interface Options → Serial Port
# → Login shell: No
# → Serial hardware: Yes

# Reboot
sudo reboot
```

### Step 3: Install Dependencies

```bash
# Install Coral TPU packages
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | \
  sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt update
sudo apt install -y libedgetpu1-std python3-pycoral

# Install camera packages
sudo apt install -y python3-picamera2

# Create virtual environment
python3 -m venv ~/drone-env
source ~/drone-env/bin/activate

# Install Python packages
pip install numpy opencv-python-headless pymavlink dronekit
```

### Step 4: Download Model

```bash
# Create models directory
mkdir -p ~/models

# Download COCO SSD MobileNet v2 (Edge TPU)
wget https://github.com/google-coral/test_data/raw/master/ssd_mobilenet_v2_coco_quant_postprocess_edgetpu.tflite \
  -O ~/models/detect.tflite

wget https://raw.githubusercontent.com/google-coral/test_data/master/coco_labels.txt \
  -O ~/models/labels.txt
```

### Step 5: Create Main Script

See `src/` directory for full implementation.

---

## Project Structure

```
shazils-drone/
├── README.md
├── docs/
│   ├── phase1-rc-conversion.md
│   ├── phase2-autonomy.md
│   ├── phase3-camera-ai.md
│   ├── parts-list.md
│   └── wiring.md
├── src/
│   ├── main.py              # Main entry point
│   ├── detection.py         # Object detection module
│   ├── mavlink_bridge.py    # FC communication
│   ├── behaviors.py         # AI flight behaviors
│   └── config.py            # Configuration
├── models/
│   ├── detect.tflite        # Edge TPU model
│   └── labels.txt           # Class labels
└── requirements.txt
```

---

## Testing Procedure

### Step 1: Test Camera

```bash
# Capture test image
libcamera-still -o test.jpg
```

### Step 2: Test Coral TPU

```python
from pycoral.utils import edgetpu
interpreter = edgetpu.make_interpreter('models/detect.tflite')
print("Coral TPU initialized successfully")
```

### Step 3: Test MAVLink (Ground Test)

```python
from dronekit import connect
vehicle = connect('/dev/serial0', baud=57600)
print(f"Connected: {vehicle.is_armable}")
```

### Step 4: Test Detection (Ground Test)

```bash
python src/main.py --test-mode
```

### Step 5: Flight Test

1. Fly in MANUAL mode
2. Enable AI system
3. Point camera at objects
4. Verify detections logged
5. Test autonomous behaviors

---

## Safety Considerations

| Risk | Mitigation |
|------|------------|
| AI commands wrong action | Kill switch overrides all AI |
| Pi crashes mid-flight | FC continues last command / RTL |
| Object misidentification | High confidence threshold (>0.7) |
| Erratic AI behavior | Geofence + altitude limits |

### Kill Switch Configuration

```python
# Always check for manual override
if vehicle.mode == VehicleMode("MANUAL"):
    # Pilot has control, AI inactive
    return

# Check RC kill switch channel
if rc_channel_6 > 1500:
    # AI disabled by pilot
    return
```

---

## Next Steps (Beyond Phase 3)

1. **Custom Model Training**
   - Train on specific objects of interest
   - Improve detection accuracy

2. **Advanced Behaviors**
   - Search patterns
   - Multi-object tracking
   - Autonomous landing on target

3. **Data Logging**
   - Record detections with GPS
   - Video recording with overlays
   - Post-flight analysis

4. **Multi-Drone Coordination**
   - Swarm behaviors
   - Distributed search
