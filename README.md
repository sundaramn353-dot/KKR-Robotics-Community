
<div align="center">

# Industry-Grade Autonomous Line Following Robot (LFR)

### Official Technical Documentation and Implementation Guide

### KKR-Robotics-Community

---

![ROS2](https://img.shields.io/badge/ROS2-READY-22314E?style=for-the-badge&logo=ros&logoColor=white)
![ESP32](https://img.shields.io/badge/ESP32-DUAL_CORE-red?style=for-the-badge&logo=espressif&logoColor=white)
![PID](https://img.shields.io/badge/PID-CONTROL-blue?style=for-the-badge)
![PlatformIO](https://img.shields.io/badge/PLATFORMIO-EMBEDDED-orange?style=for-the-badge&logo=platformio&logoColor=white)

</div>

---

# Table of Contents

- [Introduction](#introduction)
- [System Architecture](#system-architecture)
- [Hardware Requirements](#hardware-requirements)
- [Mechanical Integration](#mechanical-integration)
- [Electrical Configuration](#electrical-configuration)
- [Software and Control Logic](#software-and-control-logic)
- [PID Control Theory](#pid-control-theory)
- [Core Control Loop](#core-control-loop-c)
- [PID Tuning Methodology](#pid-tuning-methodology)
- [Firmware Deployment](#firmware-deployment)
- [Sensor Calibration](#sensor-calibration)
- [Future ROS2 Integration](#future-ros2-integration)
- [Community and Support](#community-and-support)
- [Repository Goals](#repository-goals)
- [License](#license)

---

# Introduction

A Line Following Robot (LFR) is an autonomous robotic platform designed to navigate predefined visual trajectories using reflectance-based sensing systems and real-time feedback control algorithms.

Within industrial automation ecosystems, the same operational principles are widely implemented in:

- Automated Guided Vehicles (AGVs)
- Warehouse Logistics Systems
- Autonomous Inspection Platforms
- Industrial Material Handling Robots
- Smart Manufacturing Pipelines

This repository is structured as an industry-oriented learning and implementation framework that introduces:

- Embedded Robotics Engineering
- PID-Based Motion Control
- Differential Drive Systems
- Sensor Fusion Concepts
- Real-Time Autonomous Navigation
- ROS2 Transition Fundamentals

---

# System Architecture

The robot follows the standard robotics control pipeline:

## Sense → Think → Act

| Stage | Component | Functional Responsibility |
|---|---|---|
| SENSE | QTR Sensor Array | Detects line position using IR reflectance |
| THINK | ESP32 Microcontroller | Computes error and PID correction |
| ACT | L298N Motor Driver | Controls differential motor velocity using PWM |

---

# Hardware Requirements

The following hardware configuration is validated for stable and high-precision autonomous navigation.

| Component | Recommended Product | Functional Description |
|---|---|---|
| Microcontroller | [ESP32 DevKit V1](https://www.espressif.com/en/products/socs/esp32) | Dual-core processing with future micro-ROS compatibility |
| Sensor Array | [Pololu QTR-8RC Reflectance Sensor Array](https://www.pololu.com/product/961) | High-resolution IR-based line detection |
| Motor Driver | [L298N Dual H-Bridge Motor Driver](https://www.st.com/en/motor-drivers/l298.html) | Independent differential motor control |
| Gear Motors | [N20 Metal Gear Motors](https://www.pololu.com/category/60/micro-metal-gearmotors) | Compact high-torque precision drive motors |
| Battery System | [7.4V Li-Ion 2S Battery Pack](https://www.sparkfun.com/lithium-ion-batteries) | Stable voltage delivery for embedded systems |
| Chassis Platform | [2WD Robot Chassis](https://www.pololu.com/category/2/robot-kits) | Structural mechanical framework |
| Stability Module | [Mini Ball Caster Wheel](https://www.pololu.com/category/149/ball-casters) | Provides 3-point balance and stability |

---

# Mechanical Integration

## Phase 1 — Chassis Assembly

### Motor Installation

- Mount both N20 gear motors parallel to the chassis.
- Ensure symmetrical wheel alignment to minimize drift.
- Verify mechanical rigidity before electrical integration.

### Stability Configuration

- Install the caster wheel along the center axis.
- Maintain balanced center-of-gravity distribution.

### Sensor Placement

- Position the QTR sensor array at the front edge of the robot.
- Maintain a ground clearance between:

```text
3mm → 5mm
```

- Ensure uniform sensor elevation for consistent reflectance readings.

---

# Electrical Configuration

## Phase 2 — Wiring and Power Distribution

### Motor Driver Wiring

| L298N Pin | Connection |
|---|---|
| OUT1 / OUT2 | Left Motor |
| OUT3 / OUT4 | Right Motor |
| ENA / ENB | PWM Speed Control |

---

### ESP32 Integration

- Establish a common ground between all modules.
- Avoid unstable USB-only motor powering.
- Use regulated voltage distribution wherever possible.

---

### Sensor Interface Mapping

| QTR Sensor Pin | ESP32 GPIO |
|---|---|
| S1-S8 | User Configurable |
| VCC | 5V |
| GND | Common Ground |

---

# Software and Control Logic

## Recommended Development Environment

| Software | Purpose |
|---|---|
| Visual Studio Code | Embedded development IDE |
| PlatformIO | Firmware build system |
| ESP32 Board Package | Hardware support |
| QTRSensors Library v4.0+ | Reflectance sensor processing |

---

# PID Control Theory

To achieve stable navigation and minimize oscillatory steering behavior, the robot utilizes a Proportional-Integral-Derivative (PID) control system.

:contentReference[oaicite:0]{index=0}

---

## PID Functional Roles

| Constant | Functional Responsibility |
|---|---|
| Kp | Immediate response to positional error |
| Ki | Eliminates long-term steady-state offset |
| Kd | Reduces overshoot and oscillation |

---

# Core Control Loop (C++)

```cpp
void processControlLoop()
{
    // 1. SENSE
    uint16_t position = qtr.readLineBlack(sensorValues);

    // Calculate center error
    float error = (float)position - 3500.0f;

    // 2. THINK
    integralError += error;

    float derivative = error - previousError;

    float correction =
        (Kp * error) +
        (Ki * integralError) +
        (Kd * derivative);

    previousError = error;

    // 3. ACT
    applyMotorOutputs(
        BASE_SPEED - correction,
        BASE_SPEED + correction
    );
}
```

---

# PID Tuning Methodology

## Recommended Tuning Procedure

### Step 1 — Tune Kp

- Increase gradually until the robot responds rapidly.
- Avoid excessive oscillation.

### Step 2 — Tune Kd

- Reduce steering instability.
- Improve cornering stability.

### Step 3 — Tune Ki

- Correct long-term positional drift.
- Use minimal values to avoid instability.

---

# Firmware Deployment

## Dependency Installation

```bash
pio lib install "QTRSensors"
```

---

## Build Firmware

```bash
pio run
```

---

## Upload Firmware

```bash
pio run --target upload
```

---

## Open Serial Monitor

```bash
pio device monitor
```

---

# Sensor Calibration

Before operation:

1. Rotate the robot over:
   - Black Surface
   - White Surface

2. Record minimum and maximum sensor values.

3. Validate sensor consistency using the Serial Plotter.

---

# Future ROS2 Integration

This repository is designed as a foundational bridge toward advanced ROS2 robotics systems including:

- micro-ROS
- ROS2 Jazzy
- Differential Drive Kinematics
- Odometry Systems
- LiDAR Integration
- IMU Sensor Fusion
- Nav2 Autonomous Navigation
- SLAM-Based Mapping

---

# Community and Support

<div align="center">
    
ROS TamilNadu Community

Professional robotics collaboration platform for students, developers, and ROS2 engineers.

[![WhatsApp](https://img.shields.io/badge/WhatsApp-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)](https://chat.whatsapp.com/IiAwABlSRXR7dJFPgDu4Hm)

</div>

---

# Repository Goals

- Develop Industry-Oriented Robotics Documentation
- Promote Embedded Systems Engineering
- Bridge Beginner Robotics to ROS2
- Build Open Technical Learning Resources
- Support Collaborative Robotics Development

---

# License

This repository is maintained by Karthikesh Robotics Private Limited
.

Open-source contributions, technical improvements, and collaborative developments are welcome.
