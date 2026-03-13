---
title: "Building an Autonomous Car from Scratch"
author: "Raymond"
date: 2026-03-13 22:00:00 +0900
categories: [Robotics, ROS2]
tags: [ROS2, ESP32, micro-ROS, DIY]
---

## Overview
This project documents the development of a DIY autonomous vehicle using **ESP32 micro-ROS**.  
Previous experiments included STM32-based balancing robots and Raspberry Pi car kits with Lidar.  
Ultimately, the architecture transitioned to **ROS2 Humble**, with ESP32 handling IMU, Lidar, and odometry, while higher-level tasks ran on Raspberry Pi or a remote laptop.

![Overview](../assets/images/vscodeimages/img_2026-03-13-220801.png)
---

## Hardware

### Chassis
- Accurate odometry requires a robust chassis.
- Encoder motors mounted on aluminum plates for durability.
- Selected **Encoder Motor 310** for reliability.
- Initial camera gimbal was removed; future plan includes a depth camera.
- Current setup uses a **3D-printed Lidar mount**.

![Chassis](../assets/images/vscodeimages/img_2026-03-13-220851.png)

### ESP32 micro-ROS Board
- Supports:
  - 4 encoder motors
  - 2 servos
  - 1 custom serial port
  - Integrated IMU
- Power: 7.4V T-type connector.
- Includes 5V, 4A USB-C port for Raspberry Pi.
- Mounting holes align with Raspberry Pi for stack integration.

![ESP32 micro-ROS Board](../assets/images/vscodeimages/img_2026-03-13-220924.png)

### T-mini Plus TOF Lidar
- No native micro-ROS driver available.
- Custom driver created in `idf.py` build environment.
- Firmware rebuilt using available source code.
- GitHub link to driver source provided in related blog entry.

![Lidar](../assets/images/vscodeimages/img_2026-03-13-221012.png)

### Cabling
- Lidar requires **JST GH1.25 4-pin** connector.
- ESP32 board uses **JST Molex PicoBlade 4-pin**.
- RX/TX crossover wiring required.
- Careful color matching for VCC, GND, RX, TX.

![Molex PicoBlade](../assets/images/vscodeimages/img_2026-03-13-221115.png)

### Encoder Motor Cabling
- Default cables too long; shortened using crimping tool.
- First-time use of **PH2.0 JST sockets**.
- Tool purchase requires attention, but PH2.0 is widely applicable.

![Crimping Tool](../assets/images/vscodeimages/img_2026-03-13-221408.png)

- For PH2.0, 
![For PH2.0](../assets/images/vscodeimages/img_2026-03-13-221536.png)
---

## Software

- ROS2 Humble installed on Ubuntu acts as the **micro-ROS agent**.
- ESP32 firmware built with `idf.py menuconfig`:
  - Wi-Fi configured.
  - Agent IP set.
- Important note:
  - ESP32 requires an antenna for Wi-Fi.
  - Without antenna, deserialization errors occur.
- ROS2 command references available in earlier blog posts.

---

## SLAM Mapping and Autonomous Driving

### Process
- SLAM map creation requires slow, careful movement.
- Recommended tools:
  - `teleop_twist_keyboard`
  - Custom control applications
- Best practices:
  - Use rotation (`j`, `l` keys) instead of reverse.
  - Cover reflective surfaces (e.g., windows).
  - Ensure clear black wall outlines in the map.

### Result
- Successfully generated SLAM map.
- Achieved basic autonomous driving in **rviz2**.
- Test confirmed feasibility of navigation.

![SLAM](../assets/images/vscodeimages/img_2026-03-13-221618.png)

---

## Reflections

- Cable reliability was critical; avoided contact failures.
- Power stability ensured with **UBEC**.
- micro-ROS workload on ROS2 was significant due to multiple processes.
- Limitations:
  - ESP32 supports only 1 Node, 3 Publishers, 3 Subscribers.
  - TF and camera image processing handled on ROS2 agent instead.
- First-time CAD design and 3D printing for Lidar mount:
  - Result was functional and effective.
- Project timeline:
  - Preparation, execution, and testing took ~1 month.
  - Delays mainly due to shipping; software prepared during waiting period.

  ![UBEC](../assets/images/vscodeimages/img_2026-03-13-221649.png)

### Key Takeaways
- Attempting the build was essential—progress only comes through action.
- AI assistance played a crucial role in problem-solving and guidance.
- The project provided hands-on experience across hardware, software, and mechanics.

---