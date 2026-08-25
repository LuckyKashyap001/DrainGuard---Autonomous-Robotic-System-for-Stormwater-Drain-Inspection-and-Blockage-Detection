# DrainGuard---Autonomous-Robotic-System-for-Stormwater-Drain-Inspection-and-Blockage-Detection
# DrainGuard

### Autonomous Robotic System for Stormwater Drain Inspection and Blockage Detection

> **DrainGuard** is an autonomous robotic inspection platform designed to monitor stormwater drains, detect potential blockages and hazardous conditions, and provide real-time visual and sensor-based information through a wireless web dashboard.

---

## 🚧 Overview

Stormwater drains are difficult and hazardous environments to inspect manually. Blockages caused by plastic, mud, leaves, sediment, and other debris can restrict water flow and contribute to urban flooding.

**DrainGuard** addresses this problem using a compact robotic platform equipped with environmental sensors, distance measurement, motion sensing, motor-current monitoring, and an onboard camera.

The system combines:

* 🤖 Autonomous/remote rover movement
* 📷 Real-time drain inspection camera
* 🌊 Water-level monitoring
* ☠️ Methane/gas monitoring
* 🌡️ Temperature and humidity monitoring
* 📏 Obstacle and blockage-distance measurement
* 📐 IMU-based rover orientation monitoring
* ⚡ Motor-current monitoring
* 🌐 Wireless web dashboard
* 🚨 Multi-sensor blockage detection
* 💾 Inspection-event logging
* 🧠 Future AI-based debris/blockage recognition

---

# 🎯 Objectives

DrainGuard is designed to:

1. Inspect stormwater drains without requiring immediate human entry.
2. Detect potential physical blockages.
3. Monitor water accumulation and environmental conditions.
4. Identify potentially hazardous methane/gas levels.
5. Detect abnormal motor load caused by obstacles or debris.
6. Provide live camera and sensor information.
7. Generate a blockage/severity score using multiple sensor inputs.
8. Maintain inspection records for later analysis.
9. Provide a scalable foundation for autonomous drain mapping and navigation.

---

# 🏗️ System Architecture

```text
                         DRAINGUARD
                              │
             ┌────────────────┴────────────────┐
             │                                 │
             ▼                                 ▼
      MAIN ESP32                         XIAO ESP32S3 Sense
             │                                 │
     ┌───────┼────────┐                 ┌──────┼───────┐
     │       │        │                 │      │       │
     ▼       ▼        ▼                 ▼      ▼       ▼
  Sensors  Motors   Wi-Fi            Camera  AI      SD
     │       │        │                 │      │       │
     └───────┴────────┴─────────┬───────┴──────┴───────┘
                                │
                                ▼
                       Web Dashboard
                                │
                    ┌───────────┴───────────┐
                    │                       │
                    ▼                       ▼
             Live Monitoring          Alerts / Status
```

---

# 🔩 Hardware

## Main Controller

* ESP32 development board
* Motor driver
* DC geared motors
* Battery
* 5V buck converter
* 3.3V regulator where required

## Sensor Suite

| Component          | Purpose                                |
| ------------------ | -------------------------------------- |
| DHT22              | Temperature and humidity               |
| MQ-4               | Methane/gas indication                 |
| Water-level sensor | Water accumulation monitoring          |
| JSN-SR04T          | Waterproof distance measurement        |
| MPU6050            | Acceleration, orientation and tilt     |
| INA219             | Motor/bus current monitoring           |
| XIAO ESP32S3 Sense | Camera, web server and edge-processing |
| OV2640             | Visual drain inspection                |
| microSD            | Inspection/event logging               |

---

# 🔌 Electrical Connections

## Main ESP32

| Device          | ESP32 Pin | Function       |
| --------------- | --------: | -------------- |
| DHT22 DATA      |     GPIO4 | Digital        |
| MQ-4 AO         |    GPIO34 | Analog         |
| Water Sensor AO |    GPIO35 | Analog         |
| JSN-SR04T TRIG  |    GPIO26 | Digital Output |
| JSN-SR04T ECHO  |    GPIO27 | Digital Input  |
| MPU6050 SDA     |    GPIO21 | I²C            |
| MPU6050 SCL     |    GPIO22 | I²C            |
| INA219 SDA      |    GPIO21 | I²C            |
| INA219 SCL      |    GPIO22 | I²C            |

The MPU6050 and INA219 share the same I²C bus.

```text
GPIO21 ───────── SDA ─── MPU6050
                 └────── INA219

GPIO22 ───────── SCL ─── MPU6050
                 └────── INA219
```

---

# ⚠️ Voltage Considerations

The ESP32 uses **3.3 V logic**.

Do not connect a 5 V signal directly to an ESP32 GPIO.

For sensors such as the JSN-SR04T where the ECHO output may be 5 V, use a voltage divider.

```text
JSN-SR04T ECHO
       │
      10KΩ
       │
       ├────────── ESP32 GPIO27
       │
      20KΩ
       │
      GND
```

The exact divider should be selected according to the module's actual output voltage.

---

# 🔋 Power Architecture

The motors should **not** be powered from the ESP32.

Recommended architecture:

```text
                    Battery
                       │
                Main Power Switch
                       │
            ┌──────────┴──────────┐
            │                     │
            ▼                     ▼
      Motor Driver          Buck Converter
            │                     │
            ▼                     ▼
         Motors                5 V Rail
                                  │
                       ┌──────────┴──────────┐
                       │                     │
                       ▼                     ▼
                    ESP32              XIAO Sense
                       │
                       ▼
                 3.3 V Sensors
```

All components should share a **common ground**.

---

# 📷 XIAO ESP32S3 Sense

The XIAO ESP32S3 Sense is responsible for the visual and web-facing portion of DrainGuard.

### Responsibilities

* Camera capture
* Live video stream
* Web dashboard
* Sensor visualization
* Blockage-score visualization
* Event logging
* Future edge-AI inference

The camera uses dedicated GPIOs on the XIAO ESP32S3 Sense, so external wiring should avoid pins occupied by the camera and onboard peripherals.

---

# 💻 Software

DrainGuard is developed using the **Arduino IDE**.

### Main ESP32

The controller firmware handles:

```text
Sensor acquisition
       ↓
Sensor filtering
       ↓
Motor control
       ↓
Blockage parameters
       ↓
Wi-Fi communication
       ↓
XIAO / Dashboard
```

### XIAO ESP32S3 Sense

The camera server handles:

```text
Camera
  ↓
Image capture
  ↓
Web server
  ↓
Dashboard
  ↓
Visual monitoring
```

---

# 📦 Arduino Libraries

Install the following libraries through:

**Arduino IDE → Library Manager**

```text
DHT sensor library
Adafruit Unified Sensor
Adafruit MPU6050
Adafruit INA219
```

The ESP32 board package is required for both ESP32 boards.

---

# 🚀 Installation

## 1. Install Arduino IDE

Install the latest Arduino IDE and add the ESP32 board package.

## 2. Select Main ESP32

```text
Tools
→ Board
→ ESP32 Arduino
→ ESP32 Dev Module
```

## 3. Upload Controller Firmware

Open:

```text
MainController/
└── DrainGuard_Controller.ino
```

Select the appropriate COM port and upload.

---

## 4. Select XIAO ESP32S3 Sense

Select the XIAO ESP32S3 Sense board from the ESP32 Arduino boards menu.

Enable PSRAM when required for camera operation.

Open:

```text
CameraServer/
└── DrainGuard_Camera_Server.ino
```

Upload the firmware.

---

# 📡 Communication

The two controllers communicate wirelessly.

```text
Main ESP32
     │
     │ Sensor data
     │
     │ Wi-Fi
     ▼
XIAO ESP32S3 Sense
     │
     │ HTTP/WebSocket
     ▼
Web Dashboard
```

Example sensor packet:

```json
{
  "temperature": 28.4,
  "humidity": 82.0,
  "methane": 1450,
  "waterLevel": 67,
  "distance": 18.2,
  "motorCurrent": 1.82,
  "tilt": 4.3
}
```

---

# 🌐 Web Dashboard

The dashboard is designed to provide a single inspection interface.

```text
┌────────────────────────────────────────────┐
│              DRAINGUARD                    │
│       STORMWATER INSPECTION SYSTEM         │
├────────────────────────────────────────────┤
│                                            │
│              LIVE CAMERA                   │
│                                            │
├──────────────┬──────────────┬──────────────┤
│ Temperature  │ Humidity     │ Methane      │
│ 28.4 °C      │ 82 %         │ NORMAL       │
├──────────────┼──────────────┼──────────────┤
│ Water Level  │ Distance     │ Motor Current│
│ 67 %         │ 18.2 cm      │ 1.82 A       │
├──────────────┴──────────────┴──────────────┤
│                                            │
│ BLOCKAGE LEVEL                             │
│ ███████████████░░░░░ 78%                   │
│                                            │
│ STATUS: HIGH                               │
└────────────────────────────────────────────┘
```

---

# 🚧 Blockage Detection

DrainGuard does not rely on a single sensor.

Instead, multiple observations are combined to estimate the probability/severity of a blockage.

### Parameters

```text
Distance to obstruction
        +
Water-level condition
        +
Motor current
        +
Robot movement
        +
Camera analysis
        ↓
Blockage Score
```

Example scoring model:

| Parameter             | Maximum contribution |
| --------------------- | -------------------: |
| Obstruction distance  |                  40% |
| Water-level condition |                  20% |
| Motor current         |                  20% |
| Robot movement        |                  10% |
| Camera/AI detection   |                  10% |
| **Total**             |             **100%** |

### Severity

```text
0 – 29%     NORMAL

30 – 59%    WARNING

60 – 79%    HIGH

80 – 100%   CRITICAL
```

This scoring model is configurable and should be calibrated using real drain test data.

---

# 🧠 Blockage Detection Logic

A simplified implementation:

```cpp
int blockageScore = 0;

if (distance < 10)
    blockageScore += 40;

else if (distance < 20)
    blockageScore += 30;

else if (distance < 30)
    blockageScore += 15;


if (waterLevel > 80)
    blockageScore += 20;

else if (waterLevel > 60)
    blockageScore += 10;


if (motorCurrent > CURRENT_THRESHOLD)
    blockageScore += 20;


if (robotSpeed < SPEED_THRESHOLD)
    blockageScore += 10;
```

The final system can incorporate camera-based classification:

```text
Camera
   ↓
AI Model
   ↓
Debris detected?
   ↓
YES → increase blockage score
```

---

# 🚨 Safety and Alerts

DrainGuard can generate warnings when:

### High methane

```text
MQ-4
 ↓
Threshold exceeded
 ↓
🚨 GAS WARNING
```

### High water level

```text
Water sensor
 ↓
Critical threshold
 ↓
🚨 FLOODING WARNING
```

### Possible blockage

```text
Distance ↓
Motor current ↑
Water level ↑
       ↓
🚧 BLOCKAGE WARNING
```

### Rover overturn

```text
MPU6050
 ↓
Abnormal orientation
 ↓
⚠️ ROVER TILT WARNING
```

---

# 📷 Inspection Event Logging

When a critical condition occurs:

```text
Blockage > 80%
       ↓
Capture image
       ↓
Record sensor values
       ↓
Timestamp event
       ↓
Save to microSD
```

Example:

```text
inspection/
│
├── 2026-08-17_10-42-31.jpg
├── 2026-08-17_10-42-31.json
├── 2026-08-17_10-47-12.jpg
└── 2026-08-17_10-47-12.json
```

Example record:

```json
{
  "timestamp": "2026-08-17T10:42:31",
  "temperature": 29.1,
  "humidity": 84,
  "methane": 1642,
  "waterLevel": 76,
  "distance": 13,
  "motorCurrent": 2.4,
  "blockageScore": 82,
  "status": "CRITICAL"
}
```

---

# 🤖 Rover Operation

The rover can initially operate in manual mode and progressively move toward autonomous operation.

### Phase 1 — Manual

```text
Forward
Backward
Left
Right
Stop
```

### Phase 2 — Assisted

```text
Obstacle detected
       ↓
Stop
       ↓
Alert operator
```

### Phase 3 — Autonomous

```text
Start inspection
       ↓
Move through drain
       ↓
Monitor sensors
       ↓
Detect obstruction
       ↓
Slow/stop
       ↓
Capture image
       ↓
Calculate blockage
       ↓
Log event
       ↓
Continue / request intervention
```

---

# 🗺️ Future Autonomous Mapping

A future version can add:

* Wheel encoders
* LiDAR
* GPS for surface-level positioning
* Visual odometry
* SLAM
* Drain geometry estimation
* Inspection waypoint generation

> GPS is generally unreliable inside underground drains. For underground positioning, wheel odometry, IMU, LiDAR, UWB, or visual methods are more appropriate depending on the environment.

---

# 🔬 Testing Procedure

## Sensor Test

Verify each sensor individually:

```text
[ ] DHT22
[ ] MQ-4
[ ] Water sensor
[ ] JSN-SR04T
[ ] MPU6050
[ ] INA219
```

## Motor Test

```text
[ ] Forward
[ ] Reverse
[ ] Left
[ ] Right
[ ] Stop
```

## Communication Test

```text
[ ] ESP32 connects to Wi-Fi
[ ] XIAO connects to Wi-Fi
[ ] Sensor data transmitted
[ ] Dashboard updates
```

## Camera Test

```text
[ ] Camera initializes
[ ] Live stream works
[ ] Images captured
[ ] SD logging works
```

## Blockage Test

Test using controlled obstacles:

```text
No obstacle
      ↓
Small obstacle
      ↓
Medium obstruction
      ↓
Major obstruction
```

Record:

```text
Distance
Water level
Motor current
Blockage score
Camera result
```

and tune the thresholds based on the collected data.

---

# ⚠️ Sensor Limitations

### MQ-4

The raw ADC value should **not automatically be interpreted as an exact methane concentration in ppm**. Proper calibration is required for quantitative gas measurement.

### Water Sensor

Conductive water sensors can corrode over time. For long-term deployment, consider a more robust waterproof level sensor.

### Ultrasonic Sensor

Ultrasonic measurements can be affected by:

* Water turbulence
* Angled surfaces
* Mud
* Debris
* Narrow drain geometry

Therefore, distance should be combined with other measurements.

### Blockage Score

The blockage percentage is an **estimated system score**, not a direct measurement of the percentage of the drain physically blocked.

---

# 🔐 Safety

DrainGuard is intended to reduce the need for initial human inspection of potentially hazardous environments.

However:

* Do not treat the robot's gas reading as a certified safety measurement.
* Do not enter a drain based solely on a "safe" robot reading.
* Waterproof exposed electronics appropriately.
* Protect battery terminals from water.
* Use suitable fuses and power protection.
* Ensure the motor driver and battery are rated for the selected motors.
* Test the rover in a controlled environment before deployment.

---

# 📈 Future Development

### Hardware

* [ ] Waterproof enclosure
* [ ] Wheel encoders
* [ ] Higher-quality gas sensor
* [ ] Improved water-level sensor
* [ ] LiDAR
* [ ] Current/voltage monitoring
* [ ] LED inspection lighting
* [ ] Speaker/buzzer
* [ ] Emergency stop
* [ ] Improved traction system

### Software

* [ ] Real-time WebSocket dashboard
* [ ] Historical sensor graphs
* [ ] AI debris detection
* [ ] Automatic image classification
* [ ] Autonomous navigation
* [ ] SLAM/mapping
* [ ] Event database
* [ ] Cloud synchronization
* [ ] Mobile interface

---

# 🏆 Project Vision

DrainGuard aims to transform stormwater drain inspection from a hazardous and predominantly manual task into a **data-driven robotic inspection process**.

The long-term system combines:

```text
ROBOTICS
    +
IoT
    +
COMPUTER VISION
    +
EDGE AI
    +
SENSOR FUSION
    +
AUTONOMOUS NAVIGATION
```

to provide municipalities and infrastructure teams with actionable information about drain conditions before blockages become severe flooding problems.

---

# 👨‍💻 Development

**Project:** DrainGuard
**Category:** Robotics / IoT / Embedded Systems / Computer Vision
**Platform:** ESP32 + XIAO ESP32S3 Sense
**Development Environment:** Arduino IDE

---

## ⭐ If you find this project useful

Consider starring the repository and following the development of DrainGuard as the platform evolves from a sensor-based inspection rover into an autonomous drain-mapping and blockage-detection system.
